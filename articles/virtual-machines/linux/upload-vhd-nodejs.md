---
title: Caricare un'immagine Linux personalizzata con l'interfaccia della riga di comando di Azure 1.0 | Documentazione Microsoft
description: Creare e caricare un disco rigido virtuale (VHD) in Azure con un'immagine Linux personalizzata usando il modello di distribuzione Resource Manager e l'interfaccia della riga di comando di Azure 1.0.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: iainfou
ms.openlocfilehash: 6eb0cae2b70e0cbb9a4fb5fcab3a58d566d0f4d9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-image-by-using-the-azure-cli-10"></a>Caricare e creare una VM Linux da un'immagine disco personalizzata usando l'interfaccia della riga di comando di Azure 1.0
Questo articolo illustra come caricare un disco rigido virtuale su Azure usando il modello di distribuzione Resource Manager e come creare le VM Linux da questa immagine personalizzata. Questa funzionalità consente di installare e configurare una distribuzione Linux in base ai propri requisiti e quindi di usare il disco rigido virtuale per creare rapidamente macchine virtuali di Azure.


## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività
È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

- [Interfaccia della riga di comando di Azure 1.0](#quick-commands): l'interfaccia della riga di comando per i modelli di distribuzione classica e di gestione delle risorse (questo articolo)
- [Interfaccia della riga di comando di Azure 2.0](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): interfaccia avanzata per il modello di distribuzione di gestione delle risorse


## <a name="quick-commands"></a>Comandi rapidi
Se si vuole eseguire rapidamente l'attività, la sezione seguente indica in dettaglio i comandi base per caricare una VM in Azure. Altre informazioni dettagliate e il contesto per ogni passaggio sono disponibili nelle sezioni successive del documento, [a partire da qui](#requirements).

Controllare di aver effettuato l'accesso tramite l'[interfaccia della riga di comando di Azure 1.0](../../cli-install-nodejs.md) e di usare la modalità Resource Manager:

```azurecli
azure config mode arm
```

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono `myResourceGroup`, `mystorageaccount` e `myimages`.

Creare prima un gruppo di risorse. Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella località `WestUs`:

```azurecli
azure group create myResourceGroup --location "WestUS"
```

Creare un account di archiviazione in cui salvare i dischi virtuali. Nell'esempio seguente viene creato un nuovo account di archiviazione denominato `mystorageaccount`:

```azurecli
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

Elencare le chiavi di accesso per l'account di archiviazione. Annotare il valore di `key1`:

```azurecli
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

Creare un contenitore nell'account di archiviazione usando la chiave di archiviazione ottenuta. Nell'esempio seguente viene creato un contenitore denominato `myimages` usando il valore della chiave di archiviazione da `key1`:

```azurecli
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

Caricare infine il disco rigido virtuale nel contenitore creato. Specificare il percorso locale per il disco rigido virtuale in `/path/to/disk/mydisk.vhd`:

```azurecli
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

È ora possibile creare una macchina virtuale dal disco virtuale caricato [utilizzando un modello di Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd). È anche possibile utilizzare l'interfaccia della riga di comando specificando l'URI sul disco (`--image-urn`). L'esempio seguente crea una VM denominata `myVM` usando il disco virtuale caricato in precedenza:

```azurecli
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
```

L'account di archiviazione di destinazione deve essere lo stesso in cui è stato caricato il disco virtuale. È inoltre necessario specificare tutti i parametri aggiuntivi richiesti dal comando `azure vm create` , ad esempio rete virtuale, indirizzo IP pubblico, nome utente e chiavi SSH, oppure rispondere ai messaggi inerenti. Sono disponibili altre informazioni sui [parametri di Resource Manager per l'interfaccia della riga di comando](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Requisiti
Per completare la procedura seguente, è necessario:

* **Sistema operativo Linux installato in un file VHD**: installare una [distribuzione Linux approvata da Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), oppure vedere le [informazioni sulle distribuzioni non approvate](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), in un disco virtuale in formato VHD. Sono disponibili vari strumenti per la creazione di una VM e un file VHD:
  * Installare e configurare [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) o [KVM](http://www.linux-kvm.org/page/RunningKVM), prestando attenzione a usare il disco rigido virtuale come formato di immagine. Se necessario, è possibile [convertire un'immagine](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) usando `qemu-img convert`.
  * È anche possibile usare Hyper-V [in Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) o [in Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Il formato VHDX più recente non è supportato in Azure. Quando si crea una VM, specificare VHD come formato. Se necessario, è possibile convertire i dischi VHDX nel formato VHD usando [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) o il cmdlet [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) di PowerShell. Inoltre, Azure non supporta il caricamento di VHD dinamici, pertanto è necessario convertire tali dischi in VHD statici prima del caricamento. Per convertire dischi dinamici durante il processo di caricamento in Azure, sono disponibili strumenti come [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) .
> 
> 

* Le VM create da un'immagine personalizzata devono trovarsi nello stesso account di archiviazione dell'immagine stessa.
  * Creare un account di archiviazione e un contenitore in cui inserire l'immagine personalizzata e le VM create.
  * Una volta create tutte le VM, è possibile eliminare l'immagine.

Controllare di aver effettuato l'accesso tramite l'[interfaccia della riga di comando di Azure 1.0](../../cli-install-nodejs.md) e di usare la modalità Resource Manager:

```azurecli
azure config mode arm
```

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono `myResourceGroup`, `mystorageaccount` e `myimages`.

<a id="prepimage"></a>

## <a name="prepare-the-image-to-be-uploaded"></a>Preparare l'immagine da caricare
Azure supporta svariate distribuzioni di Linux (vedere la sezione [Distribuzioni approvate](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Gli articoli seguenti forniscono le istruzioni per preparare le diverse distribuzioni Linux supportate in Azure:

* **[Distribuzioni basate su CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES e openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Altro - Distribuzioni non approvate](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Vedere anche le **[Note generali sull'installazione di Linux](create-upload-generic.md#general-linux-installation-notes)** per suggerimenti più generali sulla preparazione di immagini Linux per Azure.

> [!NOTE]
> Il [Contratto di Servizio per la piattaforma Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) è applicabile alle VM che eseguono Linux solo quando una distribuzione approvata viene usata con i dettagli di configurazione specificati nella sezione "Versioni e distribuzioni supportate" in [Distribuzioni di Linux supportate da Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
I gruppi di risorse riuniscono in modo logico tutte le risorse di Azure a supporto delle macchine virtuali, come l'archiviazione e la rete virtuale. Ulteriori informazioni sui [gruppi di risorse di Azure sono disponibili qui](../../azure-resource-manager/resource-group-overview.md). Prima di caricare l'immagine disco personalizzata e creare le VM, è necessario creare un gruppo di risorse. 

Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella località `WestUS`:

```azurecli
azure group create myResourceGroup --location "WestUS"
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione
Le VM vengono archiviate come BLOB di pagine all'interno di un account di archiviazione. Ulteriori informazioni sull' [archiviazione BLOB di Azure sono disponibili qui](../../storage/common/storage-introduction.md#blob-storage). Creare un account di archiviazione per l'immagine disco personalizzata e le VM. Le VM create a partire dall'immagine disco personalizzata devono trovarsi nello stesso account di archiviazione dell'immagine.

Nell'esempio seguente viene creato un account di archiviazione denominato `mystorageaccount` nel gruppo di risorse creato in precedenza:

```azurecli
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

## <a name="list-storage-account-keys"></a>Ottenere chiavi degli account di archiviazione
Azure genera due chiavi di accesso a 512 bit per ogni account di archiviazione. Queste chiavi di accesso vengono utilizzate per autenticarsi nell'account di archiviazione, ad esempio per eseguire operazioni di scrittura. Ulteriori informazioni sulla [gestione dell'accesso all'archiviazione sono disponibili qui](../../storage/common/storage-create-storage-account.md#manage-your-storage-account). Le chiavi di accesso possono essere visualizzate tramite il comando `azure storage account keys list` .

Visualizzare le chiavi di accesso per l'account di archiviazione creato:

```azurecli
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

L'output è simile a:

```azurecli
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK

```
Prendere nota dell'elemento `key1` perché verrà utilizzato per interagire con l'account di archiviazione nei passaggi successivi.

## <a name="create-a-storage-container"></a>Creare un contenitore di archiviazione
Come si creano directory diverse per organizzare in modo logico il file system locale, per organizzare i dischi virtuali e le immagini all'interno di un account di archiviazione vengono creati contenitori. Un account di archiviazione può contenere un numero qualsiasi di contenitori. 

L'esempio seguente crea un nuovo contenitore denominato `myimages`, specificando la chiave di accesso ricavata nel passaggio precedente (`key1`):

```azurecli
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

## <a name="upload-vhd"></a>Caricare il file VHD.
Ora è effettivamente possibile caricare l'immagine disco personalizzata. Come con tutti i dischi virtuali utilizzati dalle VM, l'immagine disco personalizzata viene caricata e archiviata come BLOB di pagine.

Specificare la chiave di accesso e il contenitore creato nel passaggio precedente, quindi selezionare il percorso dell'immagine disco personalizzata sul computer locale:

```azurecli
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

## <a name="create-vm-from-custom-image"></a>Creare una VM in base a un'immagine personalizzata
Quando si creano le macchine virtuali dall'immagine disco personalizzata, specificare l'URI sull'immagine disco. Assicurarsi che l'account di archiviazione corrisponda alla destinazioni in cui viene archiviata l'immagine disco personalizzata. È possibile creare la VM tramite l'interfaccia della riga di comando di Azure o il modello JSON di Resource Manager.

### <a name="create-a-vm-using-the-azure-cli"></a>Creare una VM Linux usando l'interfaccia della riga di comando di Azure
Specificare il parametro `--image-urn` con il comando `azure vm create` in modo da puntare all'immagine disco personalizzata. Assicurarsi che `--storage-account-name` corrisponda all'account di archiviazione in cui è archiviata l'immagine disco personalizzata. Non è necessario utilizzare lo stesso contenitore come immagine disco personalizzata per archiviare le macchine virtuali. Assicurarsi di creare qualsiasi contenitore aggiuntivo seguendo la stessa procedura utilizzata in precedenza prima di caricare le immagini disco personalizzate.

L'esempio seguente crea una VM denominata `myVM` dall'immagine disco personalizzata:

```azurecli
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
    --storage-account-name mystorageaccount
```

È ancora necessario specificare tutti i parametri aggiuntivi richiesti dal comando `azure vm create` , ad esempio rete virtuale, indirizzo IP pubblico, nome utente e chiavi SSH, oppure rispondere ai messaggi inerenti. Sono disponibili altre informazioni sui [parametri di Resource Manager per l'interfaccia della riga di comando](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

### <a name="create-a-vm-using-a-json-template"></a>Creare una VM utilizzando un modello JSON
I modelli di Azure Resource Manager sono file JavaScript Object Notation (JSON) che definiscono l'ambiente che si desidera generare. I modelli sono suddivisi in diversi provider di risorse, ad esempio calcolo o rete. È possibile utilizzare i modelli esistenti o crearne di nuovi. Altre informazioni sull' [uso di Resource Manager e relativi modelli](../../azure-resource-manager/resource-group-overview.md).

Nel provider `Microsoft.Compute/virtualMachines` del modello, è presente un nodo `storageProfile` contenente i dettagli sulla configurazione della VM. I due parametri principali da modificare sono gli URI `image` e `vhd`, che puntano all'immagine disco e al disco virtuale della nuova VM. Di seguito viene mostrato un esempio di JSON per l'utilizzo di un'immagine disco personalizzata:

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

È possibile usare [questo modello esistente per creare una VM da un'immagine personalizzata](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) oppure consultare le informazioni sulla [creazione di modelli di Azure Resource Manager personalizzati](../../azure-resource-manager/resource-group-authoring-templates.md). 

Dopo aver configurato un modello, creare le VM tramite il comando `azure group deployment create` . Specificare l'URI del modello JSON con il parametro `--template-uri` :

```azurecli
azure group deployment create --resource-group myResourceGroup
    --template-uri https://uri.to.template/mytemplate.json
```

Se si dispone di un file JSON archiviato localmente nel computer in uso, è possibile utilizzare il parametro alternativo `--template-file` :

```azurecli
azure group deployment create --resource-group myResourceGroup
    --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Passaggi successivi
Dopo avere preparato e caricato il disco rigido virtuale, è possibile ottenere altre informazioni sull' [uso di Azure Resource Manager e dei modelli](../../azure-resource-manager/resource-group-overview.md). È anche consigliabile [aggiungere un disco dati](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) alle nuove VM. Se nelle VM sono in esecuzione applicazioni a cui si deve accedere, assicurarsi di [aprire le porte e gli endpoint](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

