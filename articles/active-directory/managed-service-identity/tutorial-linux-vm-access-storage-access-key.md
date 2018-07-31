---
title: Usare un'identità del servizio gestita per una macchina virtuale Linux per accedere ad Archiviazione di Azure
description: Questa esercitazione illustra come usare un'identità del servizio gestita per una macchina virtuale Linux per accedere ad Archiviazione di Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: aa0736452d7dc06c5a1a6c2710024a5fdc626af1
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258712"
---
# <a name="tutorial-use-a-linux-vm-managed-service-identity-to-access-azure-storage-via-access-key"></a>Esercitazione: Usare un'identità del servizio gestita per una macchina virtuale Linux per accedere ad Archiviazione di Azure tramite una chiave di accesso

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Questa esercitazione illustra come abilitare l'identità del servizio gestita per una macchina virtuale Linux e quindi usare l'identità per recuperare le chiavi di accesso dell'account di archiviazione. È possibile usare una chiave di accesso alle risorse di archiviazione come di consueto durante le operazioni di archiviazione, ad esempio quando si usa Storage SDK. In questa esercitazione si caricheranno e scaricheranno oggetti BLOB usando l'interfaccia della riga di comando di Azure. Si apprenderà come:

> [!div class="checklist"]
> * Abilitare l'identità del servizio gestita in una macchina virtuale Linux 
> * Concedere alla macchina virtuale l'accesso alle chiavi di accesso all'account di archiviazione in Resource Manager 
> * Ottenere un token di accesso usando l'identità della macchina virtuale e usarlo per ottenere le chiavi di accesso alle risorse di archiviazione da Resource Manager  

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Accedere ad Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).


## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Creare una macchina virtuale Linux in un nuovo gruppo di risorse

Per questa esercitazione si creerà una nuova macchina virtuale Linux, È possibile abilitare l'identità del servizio gestita anche in una macchina virtuale esistente.

1. Fare clic sul pulsante **+/Crea nuovo servizio** nell'angolo superiore sinistro del portale di Azure.
2. Selezionare **Calcolo** e quindi **Ubuntu Server 16.04 LTS**.
3. Immettere le informazioni relative alla macchina virtuale. In **Tipo di autenticazione** selezionare **Chiave pubblica SSH** o **Password**. Le credenziali create consentono di eseguire l'accesso alla macchina virtuale.

    ![Testo immagine alt](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Dall'elenco a discesa **Sottoscrizione** selezionare una sottoscrizione per la macchina virtuale.
5. Per selezionare un nuovo **Gruppo di risorse** in cui creare la macchina virtuale, scegliere **Crea nuovo**. Al termine fare clic su **OK**.
6. Selezionare la dimensione della macchina virtuale. Per visualizzare altre dimensioni, selezionare **View all** (Visualizza tutto) o modificare il filtro Tipo di disco supportato. Nel pannello delle impostazioni mantenere le impostazioni predefinite e fare clic su **OK**.

## <a name="enable-managed-service-identity-on-your-vm"></a>Abilitare l'identità del servizio gestita nella macchina virtuale

Un'identità del servizio gestita della macchina virtuale consente di ottenere i token di accesso da Azure AD senza dover inserire le credenziali nel codice. L'abilitazione dell'identità del servizio gestito in una macchina virtuale comporta due operazioni: la registrazione della macchina virtuale con Azure Active Directory per creare la relativa identità gestita e la configurazione dell'identità sulla macchina virtuale.  

1. Passare al gruppo di risorse della nuova macchina virtuale e selezionare la macchina virtuale creata nel passaggio precedente.
2. Nella sezione "Impostazioni" della macchina virtuale a sinistra fare clic su **Configurazione**.
3. Per registrare e abilitare l'identità del servizio gestita, selezionare **Sì**. Se si vuole disabilitare questa funzionalità, selezionare No.
4. Assicurarsi di fare clic su **Salva** per salvare la configurazione.

    ![Testo immagine alt](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="create-a-storage-account"></a>Creare un account di archiviazione 

Se non ne è già disponibile uno, creare un account di archiviazione.  È anche possibile ignorare questo passaggio e concedere all'identità del servizio gestita della macchina virtuale l'accesso alle chiavi di un account di archiviazione esistente. 

1. Fare clic sul pulsante **+/Crea nuovo servizio** nell'angolo superiore sinistro del portale di Azure.
2. Fare clic su **Archiviazione**, quindi **Account di archiviazione**. Viene visualizzato un nuovo pannello "Crea account di archiviazione".
3. Immettere un **nome** per l'account di archiviazione, che verrà usato in un secondo momento.  
4. **Modello di distribuzione** e **Tipologia account** devono essere impostati su "Gestione di risorse" e "Utilizzo generico". 
5. Verificare che le impostazioni in **Sottoscrizione** e **Gruppo di risorse** corrispondano a quelle specificate al momento della creazione della macchina virtuale nel passaggio precedente.
6. Fare clic su **Create**(Crea).

    ![Creare un nuovo account di archiviazione](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Creare un contenitore BLOB nell'account di archiviazione

Successivamente verrà caricato e scaricato un file per il nuovo account di archiviazione. Poiché i file richiedono l'archiviazione BLOB, è necessario creare un contenitore BLOB in cui archiviare il file.

1. Tornare all'account di archiviazione appena creato.
2. Fare clic sul collegamento **Contenitori** a sinistra, in "Servizio BLOB".
3. Fare clic su **+ Contenitore** nella parte superiore della pagina e verrà visualizzato il pannello "Nuovo contenitore".
4. Assegnare un nome al contenitore, selezionare un livello di accesso, quindi fare clic su **OK**. Il nome specificato verrà usato più avanti nell'esercitazione. 

    ![Creare un contenitore di archiviazione](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-managed-service-identity-access-to-use-storage-account-access-keys"></a>Concedere all'identità del servizio gestita della macchina virtuale l'accesso per l'uso delle chiavi di accesso dell'account di archiviazione

Archiviazione di Azure non supporta l'autenticazione di Azure AD in modo nativo.  È tuttavia possibile usare un'identità del servizio gestita per recuperare le chiavi di accesso dell'account di archiviazione da Resource Manager e usare una chiave per accedere alle risorse di archiviazione.  In questo passaggio, all'identità del servizio gestita della macchina virtuale viene concesso l'accesso alle chiavi dell'account di archiviazione.   

1. Tornare all'account di archiviazione appena creato.
2. Fare clic sul collegamento **Controllo di accesso (IAM)** nel pannello di sinistra.  
3. Fare clic su **+Aggiungi** in cima alla pagina per aggiungere una nuova assegnazione di ruolo alla macchina virtuale.
4. Impostare **Ruolo** su "Ruolo del servizio dell'operatore della chiave dell'account di archiviazione", sul lato destro della pagina. 
5. Nell'elenco a discesa impostare **Assegna accesso a** sulla risorsa "Macchina virtuale".  
6. Assicurarsi quindi che la sottoscrizione appropriata sia presente nell'elenco a discesa **Sottoscrizione** e quindi impostare **Gruppo di risorse** su "Tutti i gruppi di risorse".  
7. In **Seleziona** scegliere infine la macchina virtuale Linux nell'elenco a discesa e quindi fare clic su **Salva**. 

    ![Testo immagine alt](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Ottenere un token di accesso usando l'identità della macchina virtuale e usarlo per chiamare Azure Resource Manager

Il resto dell'esercitazione prevede che le operazioni vengano svolte dalla macchina virtuale creata in precedenza.

Per completare questi passaggi, è necessario disporre di un client SSH. Se si usa Windows, è possibile usare il client SSH nel [sottosistema Windows per Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Per richiedere assistenza nella configurazione delle chiavi del client SSH, vedere [Come usare le chiavi SSH con Windows in Azure](../../virtual-machines/linux/ssh-from-windows.md) o [Come creare e usare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. Nel portale di Azure passare a **Macchine virtuali**, selezionare la macchina virtuale Linux e quindi nella parte superiore della pagina **Panoramica** fare clic su **Connetti**. Copiare la stringa di connessione alla macchina virtuale. 
2. Connettersi alla macchina virtuale tramite il client SSH.  
3. Successivamente, verrà richiesto di immettere la **Password** aggiunta durante la creazione della **macchina virtuale Linux**. A questo punto l'accesso è stato eseguito correttamente.  
4. Usare CURL per ottenere il token di accesso per Azure Resource Manager.  

    Di seguito sono illustrate la richiesta e la risposta CURL per il token di accesso:
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true
    ```
    
    > [!NOTE]
    > Nella richiesta precedente il valore del parametro "risorsa" deve corrispondere esattamente a quello previsto da Azure AD. Quando si usa l'ID risorsa di Azure Resource Manager, è necessario includere la barra finale nell'URI.
    > Nella risposta seguente l'elemento access_token è stato abbreviato per comodità.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```
    
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Ottenere le chiavi di accesso dell'account di archiviazione da Azure Resource Manager per eseguire le chiamate al servizio di archiviazione  

Di seguito viene usato CURL per eseguire una chiamata a Resource Manager usando il token di accesso recuperato nella sezione precedente, per recuperare la chiave di accesso alle risorse di archiviazione. Una volta recuperata la chiave di accesso alle risorse di archiviazione, è possibile chiamare le operazioni di caricamento/scaricamento. Sostituire i valori dei parametri `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` e `<STORAGE ACCOUNT NAME>` con i valori desiderati. Sostituire il valore di `<ACCESS TOKEN>` con il token di accesso recuperato prima:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> Il testo nell'URL precedente fa distinzione tra maiuscole e minuscole. Assicurarsi quindi di usare la stessa combinazione di maiuscole e minuscole usata per i gruppi di risorse. Inoltre, è importante sapere che si tratta di una richiesta POST, non di una richiesta GET, e verificare di passare un valore per acquisire un limite di lunghezza con -d che può essere NULL.  

La risposta CURL restituisce l'elenco di chiavi:  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNt..."},{"keyName":"key2","permissions":"Full","value":"U+uI0B..."}]} 
```
Creare un file di BLOB esempio da caricare nel contenitore di archiviazione BLOB. A tale scopo, in una macchina virtuale Linux eseguire il comando seguente. 

```bash
echo "This is a test file." > test.txt
```

Eseguire quindi l'autenticazione con il comando dell'interfaccia della riga di comando `az storage` usando la chiave di accesso alle risorse di archiviazione e caricare il file nel contenitore di BLOB. Per questo passaggio è necessario [installare la versione più recente dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) nella macchina virtuale, se non è già stato fatto.
 

```azurecli-interactive
az storage blob upload -c <CONTAINER NAME> -n test.txt -f test.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Risposta: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-12T03:58:56+00:00"
}
```

È anche possibile scaricare il file usando l'interfaccia della riga di comando di Azure ed eseguendo l'autenticazione con la chiave di accesso alle risorse di archiviazione. 

Richiesta: 

```azurecli-interactive
az storage blob download -c <CONTAINER NAME> -n test.txt -f test-download.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Risposta: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "test.txt",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 21,
    "contentRange": "bytes 0-20/21",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "LSghAvpnElYyfUdn7CO8aw==",
      "contentType": "text/plain"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D5067F30D0C283\"",
    "lastModified": "2017-09-28T14:42:49+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "pageBlobSequenceNumber": null,
    "serverEncrypted": false
  },
  "snapshot": null
}
```

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato come usare l'identità del servizio gestita per una macchina virtuale Linux per accedere a Archiviazione di Azure usando una chiave di accesso.  Per altre informazioni sulle chiavi di accesso ad Archiviazione di Azure, vedere:

> [!div class="nextstepaction"]
>[Gestire le chiavi di accesso alle risorse di archiviazione](/azure/storage/common/storage-create-storage-account#manage-your-storage-access-keys)
