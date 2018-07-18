---
title: Caricare i dati immagine nel cloud con Archiviazione di Azure | Microsoft Docs
description: Usare l'archiviazione BLOB di Azure con un'applicazione Web per archiviare i dati dell'applicazione
services: storage
documentationcenter: ''
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: web
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: 307ccc6f5fce703b786708196779f0cf3d71ae96
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38461504"
---
# <a name="upload-image-data-in-the-cloud-with-azure-storage"></a>Caricare i dati immagine nel cloud con Archiviazione di Azure

Questa è la prima di una serie di esercitazioni. In questa esercitazione viene illustrato come distribuire un'applicazione Web che usa la libreria client di archiviazione di Azure per caricare immagini in un account di archiviazione. Al termine, si avrà un'applicazione Web che archivia e visualizza le immagini da Archiviazione di Azure.

# <a name="nettabnet"></a>[\.NET](#tab/net)
![Visualizzazione del contenitore delle immagini](media/storage-upload-process-images/figure2.png)

# <a name="nodejstabnodejs"></a>[Node.JS](#tab/nodejs)
![Visualizzazione del contenitore delle immagini](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Nella prima parte della serie si apprenderà come:

> [!div class="checklist"]
> * Creare un account di archiviazione
> * Creare un contenitore e impostare le autorizzazioni
> * Ottenere una chiave di accesso
> * Configurare le impostazioni dell'applicazione
> * Distribuire l'applicazione Web in Azure
> * Interagire con l'applicazione Web

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, in questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse 

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.
 
Nell'esempio seguente viene creato il gruppo di risorse denominato `myResourceGroup`.
 
```azurecli-interactive 
az group create --name myResourceGroup --location westcentralus 
``` 

## <a name="create-a-storage-account"></a>Creare un account di archiviazione
 
L'esempio carica le immagini in un contenitore BLOB in un account di archiviazione di Azure. Un account di archiviazione offre uno spazio dei nomi univoco per archiviare gli oggetti dati di Archiviazione di Azure e accedere a tali oggetti. Creare un account di archiviazione nel gruppo di risorse creato usando il comando [az storage account create](/cli/azure/storage/account#az_storage_account_create). 

> [!IMPORTANT] 
> Nella parte 2 dell'esercitazione vengono usate le sottoscrizioni di eventi per l'archiviazione BLOB. Le sottoscrizioni di eventi sono attualmente supportate solo per gli account di archiviazione BLOB nelle località seguenti: Asia sudorientale, Asia orientale, Australia orientale, Australia sud-orientale, Stati Uniti centrali, Stati Uniti orientali, Stati Uniti orientali 2, Europa occidentale, Europa settentrionale, Giappone orientale, Giappone occidentale, Stati Uniti centro-occidentali, Stati Uniti occidentali e Stati Uniti occidentali 2. A causa di questa limitazione, è necessario creare un account di archiviazione BLOB usato dall'applicazione di esempio per archiviare immagini e anteprime.   

Nel comando seguente sostituire il segnaposto `<blob_storage_account>` con il nome globalmente univoco dell'account di archiviazione BLOB.  

```azurecli-interactive 
az storage account create --name <blob_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind blobstorage --access-tier hot 
``` 
 
## <a name="create-blob-storage-containers"></a>Creare contenitori di archiviazione BLOB

L'app usa due contenitori nell'account di archiviazione BLOB. I contenitori sono simili alle cartelle e vengono usati per archiviare BLOB. Il contenitore _images_ è la posizione in cui l'applicazione carica le immagini ad alta risoluzione. In una parte successiva della serie un'app per le funzioni di Azure carica le anteprime delle immagini ridimensionate nel contenitore _thumbnails_. 

Usare il comando [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list) per ottenere le chiavi dell'account di archiviazione. Usare quindi la chiave per creare due contenitori usando il comando [az storage container create](/cli/azure/storage/container#az_storage_container_create).  
 
In questo caso, `<blob_storage_account>` è il nome dell'account di archiviazione BLOB creato. L'accesso pubblico ai contenitori _images_ è impostato su `off`, mentre l'accesso pubblico ai contenitori _thumbails_ è impostato su `container`. L'accesso pubblico `container` fa sì che le anteprime siano visibili agli utenti che visitano la pagina Web.
 
```azurecli-interactive 
$blobStorageAccount="<blob_storage_account>"

$blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container create -n images --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access off 

az storage container create -n thumbnails --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access container

echo "Make a note of your blob storage account key..." 
echo $blobStorageAccountKey 
``` 

Prendere nota del nome e della chiave dell'account di archiviazione BLOB. L'app di esempio usa queste impostazioni per connettersi all'account di archiviazione per caricare le immagini. 

## <a name="create-an-app-service-plan"></a>Creare un piano di servizio app 

Un [piano di servizio app](../../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) specifica la località, le dimensioni e le funzionalità della server farm Web che ospita l'app. 

Creare un piano di servizio app con il comando [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create). 

L'esempio seguente crea un piano di servizio app denominato `myAppServicePlan` nel piano tariffario **Gratuito**: 

```azurecli-interactive 
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE 
``` 

## <a name="create-a-web-app"></a>Creare un'app Web 

L'applicazione Web offre uno spazio di hosting per il codice dell'app di esempio che viene distribuito dal repository di esempio GitHub. Creare un'[app Web](../../app-service/app-service-web-overview.md) nel piano di servizio app `myAppServicePlan` con il comando [az webapp create](/cli/azure/webapp#az_webapp_create).  
 
Nel comando seguente sostituire `<web_app>` con un nome univoco (i caratteri validi sono `a-z`, `0-9` e `-`). Se `<web_app>` non è univoco, verrà visualizzato il messaggio di errore _Il sito Web con il nome `<web_app>` specificato esiste già._ L'URL predefinito dell'app Web è `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive 
az webapp create --name <web_app> --resource-group myResourceGroup --plan myAppServicePlan 
``` 

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Distribuire l'app di esempio dal repository GitHub

# <a name="nettabnet"></a>[\.NET](#tab/net)

Il servizio app offre diversi modi per distribuire contenuto in un'applicazione Web. In questa esercitazione si distribuisce l'app Web da un [repository pubblico di esempio GitHub](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). Configurare la distribuzione GitHub nell'applicazione Web con il comando [az webapp deployment source config](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config). Sostituire `<web_app>` con il nome dell'applicazione Web creata nel passaggio precedente.

Il progetto di esempio contiene un'app [MVC ASP.NET](https://www.asp.net/mvc) che accetta un'immagine, la salva in un account di archiviazione e visualizza le immagini da un contenitore di anteprime. L'applicazione Web usa gli spazi dei nomi [Microsoft.WindowsAzure.Storage](/dotnet/api/microsoft.windowsazure.storage?view=azure-dotnet), [Microsoft.WindowsAzure.Storage.Blob](/dotnet/api/microsoft.windowsazure.storage.blob?view=azure-dotnet) e [Microsoft.WindowsAzure.Storage.Auth](/dotnet/api/microsoft.windowsazure.storage.auth?view=azure-dotnet) dalla libreria client di archiviazione di Azure per interagire con Archiviazione di Azure. 

# <a name="nodejstabnodejs"></a>[Node.JS](#tab/nodejs)
Il servizio app offre diversi modi per distribuire contenuto in un'applicazione Web. In questa esercitazione si distribuisce l'app Web da un [repository pubblico di esempio GitHub](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node). Configurare la distribuzione GitHub nell'applicazione Web con il comando [az webapp deployment source config](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config). Sostituire `<web_app>` con il nome dell'applicazione Web creata nel passaggio precedente.

---

```azurecli-interactive 
az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

## <a name="configure-web-app-settings"></a>Configurare le impostazioni dell'app Web 

L'applicazione Web di esempio usa la [libreria client di archiviazione di Azure](/dotnet/api/overview/azure/storage?view=azure-dotnet) per chiedere i token di accesso, che vengono usati per caricare le immagini. Le credenziali dell'account di archiviazione usate dall'SDK di archiviazione sono definite nelle impostazioni dell'applicazione per l'applicazione Web. Aggiungere le impostazioni dell'applicazione all'app distribuita con il comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set). 

Nel comando seguente `<blob_storage_account>` è il nome dell'account di archiviazione BLOB e `<blob_storage_key>` è la chiave associata. Sostituire `<web_app>` con il nome dell'applicazione Web creata nel passaggio precedente.     

```azurecli-interactive 
az webapp config appsettings set --name <web_app> --resource-group myResourceGroup \
--settings AzureStorageConfig__AccountName=<blob_storage_account> \
AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbnails \
AzureStorageConfig__AccountKey=<blob_storage_key>  
``` 

Dopo che l'applicazione Web è stata distribuita e configurata, è possibile testare la funzionalità di caricamento immagini nell'app.   

## <a name="upload-an-image"></a>Caricare un'immagine 

Per testare l'applicazione Web, passare all'URL dell'app pubblicata. L'URL predefinito dell'app Web è `https://<web_app>.azurewebsites.net`. Selezionare l'area **Upload photos** (Carica foto) per selezionare e caricare un file oppure trascinare e rilasciare un file nell'area. Se è stata caricata correttamente, l'immagine scompare.

# <a name="nettabnet"></a>[\.NET](#tab/net)

![App ImageResizer](media/storage-upload-process-images/figure1.png)

Nel codice di esempio, l'attività `UploadFiletoStorage` nel file `Storagehelper.cs` viene usata per caricare le immagini nel contenitore `images` all'interno dell'account di archiviazione usando il metodo [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet). L'esempio di codice seguente contiene l'attività `UploadFiletoStorage`. 

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName, AzureStorageConfig _storageConfig)
{
    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the blob container by passing the name by reading the value from the configuration (appsettings.json)
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ImageContainer);

    // Get the reference to the block blob from the container
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

    // Upload the file
    await blockBlob.UploadFromStreamAsync(fileStream);

    return await Task.FromResult(true);
}
```

Nell'attività precedente vengono usate le classi e i metodi seguenti:

|Classe  |Metodo  |
|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)     |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)    |  [CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)       |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)    | [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_GetBlockBlobReference_System_String_)        |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)     | [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet)        |

# <a name="nodejstabnodejs"></a>[Node.JS](#tab/nodejs)

![App per il caricamento di immagini](media/storage-upload-process-images/upload-app-nodejs.png)

Nel codice di esempio la route `post` è responsabile del caricamento dell'immagine in un contenitore BLOB. La route usa i moduli per consentire l'elaborazione del caricamento:

- [multer](https://github.com/expressjs/multer) implementa la strategia di caricamento per il gestore di route
- [into-stream](https://github.com/sindresorhus/into-stream) converte il buffer in un flusso come richiesto da [createBlockBlobFromStream](http://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html#createBlockBlobFromStream)

Quando il file viene inviato alla route, i contenuti del file restano in memoria fino al caricamento del file nel contenitore BLOB.

> [!IMPORTANT]
> Il caricamento di file di dimensioni molto grandi in memoria può influire negativamente sulle prestazioni dell'applicazione Web. Se si prevede che gli utenti pubblicheranno file di grandi dimensioni, prendere in considerazione la possibilità di inserire temporaneamente i file nel file system del server Web e quindi di pianificare i caricamenti nell'archivio BLOB. Quando i file saranno nell'archivio BLOB, sarà quindi possibile rimuoverli dal file system del server.

```javascript
const
      express = require('express')
    , router = express.Router()

    , multer = require('multer')
    , inMemoryStorage = multer.memoryStorage()
    , uploadStrategy = multer({ storage: inMemoryStorage }).single('image')

    , azureStorage = require('azure-storage')
    , blobService = azureStorage.createBlobService()

    , getStream = require('into-stream')
    , containerName = 'images'
;

const handleError = (err, res) => {
    res.status(500);
    res.render('error', { error: err });
};

const getBlobName = originalName => {
    const identifier = Math.random().toString().replace(/0\./, ''); // remove "0." from start of string
    return `${originalName}-${identifier}`;
};

router.post('/', uploadStrategy, (req, res) => {

    const
          blobName = getBlobName(req.file.originalname)
        , stream = getStream(req.file.buffer)
        , streamLength = req.file.buffer.length
    ;

    blobService.createBlockBlobFromStream(containerName, blobName, stream, streamLength, err => {

        if(err) {
            handleError(err);
            return;
        }

        res.render('success', { 
            message: 'File uploaded to Azure Blob storage.' 
        });
    });
});
```
---

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Verificare che l'immagine venga visualizzata nell'account di archiviazione

Accedere al [portale di Azure](https://portal.azure.com). Nel menu a sinistra selezionare **Account di archiviazione** e quindi selezionare il nome dell'account di archiviazione. In **Panoramica** selezionare il contenitore **images**.

Verificare che l'immagine venga visualizzata nel contenitore.

![Visualizzazione del contenitore delle immagini](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Test della visualizzazione dell'anteprima

Per testare la visualizzazione dell'anteprima, caricare un'immagine nel contenitore delle anteprime per garantire che l'applicazione sia in grado di leggere il contenitore delle anteprime.

Accedere al [portale di Azure](https://portal.azure.com). Nel menu a sinistra selezionare **Account di archiviazione** e quindi selezionare il nome dell'account di archiviazione. Selezionare **Contenitori** in **Servizio BLOB** e quindi selezionare il contenitore **thumbnails**. Selezionare **Carica** per aprire il riquadro **Carica BLOB**.

Scegliere un file usando il selettore di file e selezionare **Carica**.

Tornare all'app per verificare che l'immagine caricata nel contenitore **thumbnails** sia visibile.

# <a name="nettabnet"></a>[\.NET](#tab/net)
![Visualizzazione del contenitore delle immagini](media/storage-upload-process-images/figure2.png)

# <a name="nodejstabnodejs"></a>[Node.JS](#tab/nodejs)
![Visualizzazione del contenitore delle immagini](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Nel contenitore **thumbnails** nel portale di Azure selezionare l'immagine caricata e quindi selezionare **Elimina** per eliminare l'immagine. Nella seconda parte della serie si automatizza la creazione delle immagini di anteprima, pertanto questa immagine di test non è necessaria.

È possibile abilitare la rete CDN per memorizzare nella cache i contenuti dell'account di archiviazione di Azure. Benché non sia descritta in questa esercitazione, per informazioni su come abilitare la rete CDN con il proprio account di archiviazione di Azure è possibile visitare la pagina [Integrare un account di archiviazione di Azure con la rete CDN di Azure](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Passaggi successivi

Nella prima parte della serie è stato spiegato come configurare un'applicazione Web che interagisce con l'archiviazione, ad esempio come:

> [!div class="checklist"]
> * Creare un account di archiviazione
> * Creare un contenitore e impostare le autorizzazioni
> * Ottenere una chiave di accesso
> * Configurare le impostazioni dell'applicazione
> * Distribuire l'applicazione Web in Azure
> * Interagire con l'applicazione Web

Passare alla seconda parte della serie di informazioni sull'uso della griglia di eventi per attivare una funzione di Azure per ridimensionare un'immagine.

> [!div class="nextstepaction"]
> [Usare la griglia di eventi per attivare una funzione di Azure per ridimensionare un'immagine caricata](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
