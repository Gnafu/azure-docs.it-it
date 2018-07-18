---
title: Scollegare un disco dati da una VM Windows - Azure | Microsoft Docs
description: Informazioni su come scollegare un disco dati da una macchina virtuale in Azure usando il modello di distribuzione Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: e56e9ce22cc9e2bad75c944c20bff812d8720d18
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
ms.locfileid: "30913502"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Come scollegare un disco dati da una macchina virtuale di Windows
Quando un disco dati collegato a una macchina virtuale non è più necessario, è possibile scollegarlo con facilità. Il disco verrà rimosso dalla macchina virtuale, ma non dall'archivio.

> [!WARNING]
> Se si scollega un disco, questo non viene automaticamente eliminato. Se è stata eseguita la sottoscrizione all'archiviazione Premium, si continueranno a sostenere costi di archiviazione per il disco. Per altre informazioni fare riferimento a [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](premium-storage.md#pricing-and-billing).
>
>

Se si vogliono riusare i dati presenti nel disco, è possibile ricollegarlo alla stessa macchina virtuale o collegarlo a una nuova.

## <a name="detach-a-data-disk-using-the-portal"></a>Scollegare un disco dati tramite il portale

1. Dal menu a sinistra selezionare **Macchine virtuali**.
2. Selezionare la macchina virtuale con il disco dati che si vuole scollegare e fare clic su **Arresta** per deallocare la macchina virtuale.
3. Nel riquadro delle macchine virtuali selezionare **Dischi**.
4. Nella parte superiore del riquadro **Dischi** selezionare **Modifica**.
5. Nel riquadro **Dischi** fare clic sul pulsante per scollegare il disco ![Immagine del pulsante Scollega](./media/detach-disk/detach.png) nella parte più a destra del disco dati.
5. Dopo aver rimosso il disco, fare clic su **Salva** nella parte superiore del riquadro.
6. Nel riquadro delle macchine virtuali fare clic su **Panoramica** e quindi fare clic su **Avvia** nella parte superiore del riquadro per riavviare la macchina virtuale.



Il disco rimane nello spazio di archiviazione ma non è più collegato a una macchina virtuale.

## <a name="detach-a-data-disk-using-powershell"></a>Scollegare un disco dati tramite PowerShell
In questo esempio il primo comando consente di denominare la macchina virtuale **MyVM07** nel gruppo di risorse **RG11** usando il cmdlet [Get-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) e la archivia nella variabile **$VirtualMachine**.

La seconda riga rimuove il disco dati denominato DataDisk3 dalla macchina virtuale usando il cmdlet [Remove AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk).

La terza riga aggiorna lo stato della macchina virtuale usando il cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) per completare il processo di rimozione del disco dati.

```azurepowershell-interactive
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07"
Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
Update-AzureRmVM -ResourceGroupName "RG11" -VM $VirtualMachine
```

Per altre informazioni, vedere [Remove-AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk).

## <a name="next-steps"></a>Passaggi successivi
Se si desidera riutilizzare il disco dati, è sufficiente [collegarlo a un'altra VM](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

