---
title: Creare raccolte di immagini condivise con l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Questo articolo descrive come usare l'interfaccia della riga di comando di Azure per creare immagini di macchine virtuali condivise in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: axayjo
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: ea884b80698fb257106ef68d293f350b2f55dc06
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103562"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>Creare una raccolta di immagini condivise con l'interfaccia della riga di comando di Azure

La [raccolta di immagini condivise](shared-image-galleries.md) semplifica la condivisione di immagini personalizzate all'interno dell'organizzazione. Le immagini personalizzate sono come le immagini di marketplace, ma si possono creare autonomamente. Le immagini personalizzate possono essere usate per le configurazioni di avvio, ad esempio il precaricamento e le configurazioni di applicazioni e altre configurazioni del sistema operativo. 

La raccolta di immagini condivise consente di condividere le immagini personalizzate delle macchine virtuali con altri all'interno dell'organizzazione, della propria area o tra aree e all'interno di un tenant AAD. Scegliere le immagini che si intende condividere, le aree nelle quali si vuole renderle disponibili e i destinatari. È possibile creare più raccolte così da raggruppare in maniera logica le immagini condivise. 

La raccolta è una risorsa di primo livello che offre un completo controllo degli accessi in base al ruolo (RBAC). Le immagini possono essere con versioni ed è possibile scegliere di eseguire la replica di ogni versione dell'immagine in un diverso set di aree di Azure. La raccolta funziona solo con le immagini gestite.

La funzionalità di raccolta di immagini condivise presenta più tipi di risorse. Verranno usate o compilate le seguenti contenute in questo articolo:

| Risorsa | Descrizione|
|----------|------------|
| **Immagine gestita** | Si tratta di un'immagine di base che può essere usata da sola o per creare una **versione dell'immagine** in una raccolta di immagini. Le immagini gestite vengono create da macchine virtuali generalizzate. Un'immagine gestita è un tipo speciale di disco rigido virtuale che può essere usato per creare più macchine virtuali e può ora essere sfruttato per creare versioni di immagini condivise. |
| **Raccolta di immagini** | Come in Azure Marketplace, una **raccolta di immagini** è un repository per la gestione e la condivisione delle immagini, ma è possibile controllare chi ha accesso. |
| **Definizione delle immagini** | Le immagini vengono definite all'interno di una raccolta e forniscono informazioni sull'immagine e sui requisiti per il suo uso interno. Questa include il fatto che l'immagine è per Windows o Linux, le note sulla versione e i requisiti minimi e massimi di memoria. Si tratta della definizione di un tipo di immagine. |
| **Versione dell'immagine** | La **versione dell'immagine** è ciò che si usa per creare una macchina virtuale quando si usa una raccolta. È possibile avere più versioni di un'immagine in base alle necessità del proprio ambiente. Come un'immagine gestita, quando si usa una **versione dell'immagine** per creare una macchina virtuale, la versione dell'immagine viene usata per creare nuovi dischi per la macchina virtuale. Le versioni delle immagini possono essere usate più volte. |



[!INCLUDE [virtual-machines-common-shared-images-cli](../../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-vm"></a>Creare una macchina virtuale

Creare una macchina virtuale dalla versione più recente dell'immagine usando il comando [AZ VM create](/cli/azure/vm#az-vm-create).

```azurecli-interactive 
az vm create\
   --resource-group myGalleryRG \
   --name myVM \
   --image "/subscriptions/subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --generate-ssh-keys
```

È anche possibile usare una versione specifica usando l'ID versione dell'immagine per il `--image` parametro. Ad esempio, per usare l'immagine versione *1.0.0* , `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`digitare:.

[!INCLUDE [virtual-machines-common-gallery-list-cli](../../../includes/virtual-machines-common-gallery-list-cli.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-cli](../../../includes/virtual-machines-common-shared-images-update-delete-cli.md)]

## <a name="next-steps"></a>Passaggi successivi
Il [Generatore di immagini di Azure (anteprima)](image-builder-overview.md) consente di automatizzare la creazione della versione di immagine. è anche possibile usarla per aggiornare e [creare una nuova versione dell'immagine da una versione di immagine esistente](image-builder-gallery-update-image-version.md). 

È anche possibile creare risorse della raccolta immagini condivise usando i modelli. Sono disponibili diversi modelli di avvio rapido di Azure: 

- [Creare una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Creare una definizione dell'immagine in una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Creare una versione dell'immagine in una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Creare una macchina virtuale da una versione dell'immagine](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Per altre informazioni sulle raccolte di immagini condivise, vedere [Panoramica](shared-image-galleries.md). Se si verificano problemi, vedere [Risoluzione dei problemi delle raccolte di immagini condivise](troubleshooting-shared-images.md).
