---
title: Creare un hub IoT di Azure con un cmdlet di PowerShell | Microsoft Docs
description: Procedura per usare un cmdlet di PowerShell per creare un hub IoT.
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: fd376728a1ebdf769c7f2ae76d61a60703e13711
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66146625"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>Creare un hub IoT usando il cmdlet New-AzIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduzione

È possibile usare i cmdlet di Azure PowerShell per creare e gestire hub IoT di Azure. In questa esercitazione viene illustrato come creare un hub IoT con PowerShell.

Per completare questa esercitazione è necessario disporre di una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Connettersi alla sottoscrizione di Azure

Se si usa Cloud Shell, si è già connessi alla sottoscrizione. Se PowerShell è in esecuzione in locale, immettere il comando seguente per accedere alla sottoscrizione di Azure:

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Per la distribuzione di un hub IoT è necessario un gruppo di risorse. È possibile usare un gruppo di risorse esistente o crearne uno nuovo.

Per creare un gruppo di risorse per l'hub IoT, usare il [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.Resources/New-azResourceGroup) comando. In questo esempio viene creato un gruppo di risorse denominato **MyIoTRG1** nell'area degli **Stati Uniti orientali**:

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Creare un hub IoT

Per creare un hub IoT nel gruppo di risorse creato nel passaggio precedente, usare il [New-AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/New-azIotHub) comando. In questo esempio viene creato un hub **S1** denominato **MyTestIoTHub** nell'area degli **Stati Uniti orientali**:

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

Il nome dell'hub IoT deve essere globalmente univoco.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

È possibile elencare tutti gli hub IoT nella sottoscrizione usando il [Get-AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/Get-azIotHub) comando:

```azurepowershell-interactive
Get-AzIotHub
```

Questo esempio illustra l'hub IoT Standard S1 che è stato creato nel passaggio precedente.

È possibile eliminare l'hub IoT usando il [Remove-AzIotHub](https://docs.microsoft.com/powershell/module/az.iothub/remove-aziothub) comando:

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

In alternativa, è possibile rimuovere un gruppo di risorse e tutte le risorse che contiene con il [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.Resources/Remove-azResourceGroup) comando:

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito un hub IoT mediante il cmdlet di PowerShell, può essere opportuno ottenere informazioni più dettagliate consultando i seguenti articoli:

* [Cmdlet di PowerShell da usare con l'hub IoT](https://docs.microsoft.com/powershell/module/az.iothub/).

* [API REST del provider di risorse dell'hub IoT](https://docs.microsoft.com/rest/api/iothub/iothubresource).

Per altre informazioni sulle attività di sviluppo per l'hub IoT, vedere gli articoli seguenti:

* [Introduzione a C SDK](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Distribuzione dell'intelligenza artificiale in dispositivi perimetrali con Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
