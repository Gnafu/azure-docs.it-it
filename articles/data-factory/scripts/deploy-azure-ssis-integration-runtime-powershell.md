---
title: 'Script di PowerShell: distribuire il runtime di integrazione di Azure SSIS | Microsoft Docs'
description: Questo script di PowerShell consente di creare un runtime di integrazione di Azure SSIS che può eseguire pacchetti SSIS nel cloud.
services: data-factory
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2017
ms.author: douglasl
ms.openlocfilehash: 4551d0cebf51fd2c028b6b83a75863cedb32fdc3
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54014030"
---
# <a name="powershell-script---deploy-azure-ssis-integration-runtime"></a>Script di PowerShell: distribuire il runtime di integrazione di Azure SSIS

Questo script di PowerShell di esempio consente di creare un runtime di integrazione di Azure SSIS che può eseguire pacchetti SSIS in Azure.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/data-factory/deploy-azure-ssis-integration-runtime/deploy-azure-ssis-integration-runtime.ps1 "Deploy Azure-SSIS Integration Runtime")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo aver eseguito lo script di esempio, è possibile eseguire il comando seguente per rimuovere il gruppo di risorse e tutte le risorse associate a questo:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourceGroupName
```
Per rimuovere la data factory dal gruppo di risorse, eseguire il comando seguente: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti:

| Comando | Note |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2) | Creare una data factory. |
| [Set-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2integrationruntime) | Crea un runtime di integrazione di Azure SSIS che può eseguire pacchetti SSIS nel cloud |
| [Start-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/start-azurermdatafactoryv2integrationruntime) | Avvia il runtime di integrazione SSIS di Azure. |
| [Get-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2integrationruntime) | Ottiene informazioni sul runtime di integrazione di Azure SSIS. |
| [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).

Altri esempi di script PowerShell di Azure Data Factory sono disponibili in [Azure Data Factory PowerShell samples](../samples-powershell.md) (Esempi di PowerShell per Azure Data Factory).
