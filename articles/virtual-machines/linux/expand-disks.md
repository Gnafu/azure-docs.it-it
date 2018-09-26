---
title: Espandere i dischi rigidi virtuali in una macchina virtuale Linux in Azure | Microsoft Docs
description: Informazioni su come espandere i dischi rigidi virtuali in una macchina virtuale Linux con l'interfaccia della riga di comando di Azure
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: rogarana
ms.openlocfilehash: 0c2d4d1413b6cfd0b5e457e720b59c6c7b575092
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974545"
---
# <a name="how-to-expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Procedura per espandere i dischi rigidi virtuali in una macchina virtuale Linux con l'interfaccia della riga di comando di Azure

Le dimensioni predefinite del disco rigido virtuale per il sistema operativo sono in genere di 30 GB in una VM Linux in Azure. È possibile [aggiungere dischi dati](add-disk.md) per aumentare lo spazio di archiviazione, ma è anche possibile espandere un disco dati esistente. Questo articolo illustra come espandere i dischi gestiti di una macchina virtuale Linux mediante l'interfaccia della riga di comando di Azure. 

> [!WARNING]
> Assicurarsi sempre di eseguire il backup dei dati prima di eseguire operazioni di ridimensionamento dei dischi. Per altre informazioni, vedere [Eseguire il backup di macchine virtuali Linux in Azure](tutorial-backup-vms.md).

## <a name="expand-azure-managed-disk"></a>Creare un disco gestito di Azure
Assicurarsi di avere installato la versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-az-cli2) e di aver eseguito l'accesso a un account Azure mediante il comando [az login](/cli/azure/reference-index#az_login).

Questo articolo richiede una VM esistente in Azure con almeno un disco dati collegato e preparato. Se non si ha già una VM da usare, vedere [Creare e preparare una VM con dischi dati](tutorial-manage-disks.md#create-and-attach-disks).

Negli esempi seguenti sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono *myResourceGroup* e *myVM*.

1. Non è possibile eseguire operazioni sui dischi rigidi virtuali quando la macchina virtuale è in esecuzione. Deallocare la macchina virtuale con [az vm deallocate](/cli/azure/vm#az_vm_deallocate). L'esempio seguente dealloca la VM denominata *myVM* nel gruppo di risorse *myResourceGroup*:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > Per espandere il disco rigido virtuale è necessario deallocare la macchina virtuale. `az vm stop` non rilascia le risorse di calcolo. Per rilasciare le risorse di calcolo, usare `az vm deallocate`.

1. Vedere un elenco di dischi gestiti presenti nel gruppo di risorse con [az disk list](/cli/azure/disk#az_disk_list). L'esempio seguente mostra un elenco di dischi gestiti nel gruppo di risorse denominato *myResourceGroup*:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Espandere il disco richiesto con [az disk update](/cli/azure/disk#az_disk_update). L'esempio seguente espande il disco gestito denominato *myDataDisk* per portarlo a *200*Gb:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Quando si espande un disco gestito, si esegue il mapping delle dimensioni aggiornate per le dimensioni del disco gestito più vicino. Per consultare una tabella delle dimensioni e dei livelli dei dischi disponibili, vedere [Panoramica di Azure Managed Disks - Prezzi e fatturazione](../windows/managed-disks-overview.md#pricing-and-billing).

1. Avviare la macchina virtuale con [az vm start](/cli/azure/vm#az_vm_start). L'esempio seguente avvia la VM denominata *myVM* nel gruppo di risorse *myResourceGroup*:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-disk-partition-and-filesystem"></a>Espandere il file system e la partizione del disco
Per usare il disco espanso, è necessario espandere la partizione e il file system sottostanti.

1. Eseguire SSH nella macchina virtuale con le credenziali appropriate. È possibile ottenere l'indirizzo IP pubblico della VM con [az vm show](/cli/azure/vm#az_vm_show):

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
    ```

1. Per usare il disco espanso, è necessario espandere la partizione e il file system sottostanti.

    a. Se il disco è già montato, smontarlo:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Usare `parted` per visualizzare informazioni sul disco e ridimensionare la partizione:

    ```bash
    sudo parted /dev/sdc
    ```

    Visualizzare le informazioni sul layout di partizione esistente con `print`. L'output è simile all'esempio seguente, che mostra che il disco sottostante ha dimensioni di 215 GB:

    ```bash
    GNU Parted 3.2
    Using /dev/sdc1
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) print
    Model: Unknown Msft Virtual Disk (scsi)
    Disk /dev/sdc1: 215GB
    Sector size (logical/physical): 512B/4096B
    Partition Table: loop
    Disk Flags:
    
    Number  Start  End    Size   File system  Flags
        1      0.00B  107GB  107GB  ext4
    ```

    c. Espandere la partizione con `resizepart`. Immettere il numero di partizione, *1*, e le dimensioni per la nuova partizione:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Per uscire, immettere `quit`

1. Dopo aver ridimensionato la partizione, verificarne la coerenza con `e2fsck`:

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. Ridimensionare quindi il file system con `resize2fs`:

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. Montare la partizione nella posizione desiderata, ad esempio `/datadrive`:

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Per verificare che il disco del sistema operativo sia stato ridimensionato, usare `df -h`. L'output di esempio seguente mostra che il disco dati */dev/sdc1* ha ora dimensioni di 200 GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Passaggi successivi
Se è necessario altro spazio di archiviazione, è possibile [aggiungere dischi dati a una VM Linux](add-disk.md). Per altre informazioni sulla crittografia del disco, vedere [Crittografare i dischi di una VM Linux usando l'interfaccia della riga di comando di Azure](encrypt-disks.md).
