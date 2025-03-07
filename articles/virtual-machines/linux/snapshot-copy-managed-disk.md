---
title: Creare uno snapshot di un disco rigido virtuale in Azure | Microsoft Docs
description: Informazioni su come creare una copia di un disco rigido virtuale in Azure come backup o per la risoluzione dei problemi.
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/11/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 9f2f3ac3668f0e48716fc30fb69cd1782dbd4e56
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64706966"
---
# <a name="create-a-snapshot"></a>Creare uno snapshot 

Fare uno snapshot di un sistema operativo o di un disco dati per il backup o per risolvere i problemi della macchina virtuale. Uno snapshot è una copia completa di sola lettura di un disco rigido virtuale. 

## <a name="use-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure 

Nell'esempio seguente è necessario usare [Cloud Shell](https://shell.azure.com/bash) o aver installato l'interfaccia della riga di comando di Azure.

La procedura seguente illustra come fare uno snapshot usando il comando **az snapshot create** con il parametro **--source-disk**. Nell'esempio seguente si presuppone che esista una macchina virtuale denominata *myVM* nel gruppo di risorse *myResourceGroup*.

Ottenere l'ID disco usando [az vm show](/cli/azure/vm#az-vm-show).

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Creare uno snapshot denominato *osDisk-backup* usando [az snapshot create](/cli/azure/snapshot#az-snapshot-create).

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Se si vuole archiviare lo snapshot in una risorsa di archiviazione resiliente nella zona, è necessario crearlo in un'area che supporta le [zone di disponibilità](../../availability-zones/az-overview.md) e includere il parametro **--sku Standard_ZRS**.

È possibile visualizzare un elenco degli snapshot usando [az snapshot list](/cli/azure/snapshot#az-snapshot-list).

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Usare il portale di Azure 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. In alto a sinistra fare clic su **Crea una risorsa** e cercare **Snapshot**. Selezionare **Snapshot** dai risultati della ricerca.
3. Nel pannello **Snapshot**, fare clic su **Crea**.
4. Immettere un **Nome** per lo snapshot.
5. Selezionare un gruppo di risorse esistente o specificare il nome di un nuovo gruppo. 
7. Per **Disco di origine**, selezionare il disco gestito di cui creare lo snapshot.
8. Selezionare il **tipo di account** da usare per archiviare lo snapshot. Usare il tipo **Standard HDD** a meno che non sia necessario archiviare lo snapshot su un'unità SSD a prestazioni elevate.
9. Fare clic su **Create**(Crea).


## <a name="next-steps"></a>Passaggi successivi

 Creare una macchina virtuale da uno snapshot creando un disco gestito dallo snapshot e quindi collegando il nuovo disco gestito come disco del sistema operativo. Per altre informazioni, vedere lo script [Creare una macchina virtuale da uno snapshot](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json).

