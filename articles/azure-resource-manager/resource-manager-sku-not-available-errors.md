---
title: Errori di Azure dovuti all'indisponibilità di SKU | Microsoft Docs
description: Descrive come risolvere i problemi dovuti all'errore relativo all'indisponibilità di una SKU durante la distribuzione.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.author: tomfitz
ms.openlocfilehash: 490c912a6abd6570c9bc74de8b86a516a8e6f807
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
---
# <a name="resolve-errors-for-sku-not-available"></a>Risolvere gli errori dovuti all'indisponibilità di SKU

Questo articolo descrive come risolvere l'errore **SkuNotAvailable**.

## <a name="symptom"></a>Sintomo

Quando si distribuisce una risorsa, in genere una macchina virtuale, vengono visualizzati il codice di errore e il messaggio di errore seguenti:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Causa

Questo errore viene visualizzato quando lo SKU della risorsa selezionato, ad esempio le dimensioni della macchina virtuale, non è disponibile per il percorso selezionato.

## <a name="solution-1---powershell"></a>Soluzione 1: PowerShell

Per determinare quali SKU sono disponibili in un'area, usare il comando [Get-AzureRmComputeResourceSku](/powershell/module/azurerm.compute/get-azurermcomputeresourcesku). Filtrare i risultati in base all'area. Per questo comando, è necessaria la versione più recente di PowerShell.

```powershell
Get-AzureRmComputeResourceSku | where {$_.Locations -icontains "southcentralus"}
```

I risultati includono un elenco di SKU per la località e le eventuali limitazioni per tale SKU.

```powershell
ResourceType                Name      Locations Restriction                      Capability Value
------------                ----      --------- -----------                      ---------- -----
availabilitySets         Classic southcentralus             MaximumPlatformFaultDomainCount     3
availabilitySets         Aligned southcentralus             MaximumPlatformFaultDomainCount     3
virtualMachines      Standard_A0 southcentralus
virtualMachines      Standard_A1 southcentralus
virtualMachines      Standard_A2 southcentralus
```

## <a name="solution-2---azure-cli"></a>Soluzione 2: interfaccia della riga di comando di Azure

Per determinare quali SKU sono disponibili in un'area, usare il comando `az vm list-skus`. È quindi possibile usare `grep` o un'utilità analoga per filtrare l'output.

```bash
$ az vm list-skus --output table
ResourceType      Locations           Name                    Capabilities                       Tier      Size           Restrictions
----------------  ------------------  ----------------------  ---------------------------------  --------  -------------  ---------------------------
availabilitySets  eastus              Classic                 MaximumPlatformFaultDomainCount=3
avilabilitySets   eastus              Aligned                 MaximumPlatformFaultDomainCount=3
availabilitySets  eastus2             Classic                 MaximumPlatformFaultDomainCount=3
availabilitySets  eastus2             Aligned                 MaximumPlatformFaultDomainCount=3
availabilitySets  westus              Classic                 MaximumPlatformFaultDomainCount=3
availabilitySets  westus              Aligned                 MaximumPlatformFaultDomainCount=3
availabilitySets  centralus           Classic                 MaximumPlatformFaultDomainCount=3
availabilitySets  centralus           Aligned                 MaximumPlatformFaultDomainCount=3
```

## <a name="solution-3---azure-portal"></a>Soluzione 3: portale di Azure

Per determinare quali SKU sono disponibili in un'area, usare il [portale](https://portal.azure.com). Accedere al portale e aggiungere una risorsa tramite l'interfaccia. Quando si impostano i valori, vengono visualizzati gli SKU disponibili per tale risorsa. Non è necessario completare la distribuzione.

![SKU disponibili](./media/resource-manager-sku-not-available-errors/view-sku.png)

## <a name="solution-4---rest"></a>Soluzione 4: REST

Per determinare quali SKU sono disponibili in un'area, usare l'API REST per le macchine virtuali. Inviare la richiesta seguente:

```HTTP 
GET
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/skus?api-version=2016-03-30
```

Le aree e gli SKU disponibili vengono restituiti nel formato seguente:

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```

Se non si riesce a trovare uno SKU appropriato in tale area o un'area alternativa che soddisfi le esigenze aziendali, inviare una [richiesta di SKU](https://aka.ms/skurestriction) al supporto di Azure.
