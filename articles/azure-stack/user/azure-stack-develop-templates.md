---
title: Sviluppare modelli per Azure Stack | Documentazione Microsoft
description: Informazioni sulle procedure consigliate sui modelli di Azure Stack
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 8a5bc713-6f51-49c8-aeed-6ced0145e07b
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 046866d9ed7ce65e3b46be1c67b4ab2058cefa4d
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/18/2018
ms.locfileid: "34304148"
---
# <a name="azure-resource-manager-template-considerations"></a>Considerazioni sui modelli di Azure Resource Manager

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Quando si sviluppa un'applicazione, è importante assicurare la portabilità del modello tra Azure e Azure Stack. In questo articolo vengono fornite considerazioni per lo sviluppo di Azure Resource Manager [modelli](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf), in modo da poter prototipo la distribuzione dell'applicazione e di test in Azure senza accesso a un ambiente dello Stack di Azure.

## <a name="resource-provider-availability"></a>Disponibilità di provider di risorse

Il modello che si prevede di distribuire deve utilizzare solo servizi di Microsoft Azure che sono già disponibili o in un'anteprima nello Stack di Azure.

## <a name="public-namespaces"></a>Spazi dei nomi pubblici

Azure Stack ha spazi dei nomi degli endpoint di servizio diversi rispetto al cloud pubblico di Azure perché è ospitato nel data center. Di conseguenza, gli endpoint pubblici hardcoded nei modelli di Azure Resource Manager esito negativo quando si tenta di distribuirli allo Stack di Azure. È possibile compilare in modo dinamico gli endpoint del servizio utilizzando il *riferimento* e *concatenare* funzioni per recuperare valori da provider di risorse durante la distribuzione. Ad esempio, anziché hardcoded *NET* nel modello, recuperare il [primaryEndpoints.blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-simple-windows-vm/azuredeploy.json#L201) impostare dinamicamente il *osDisk.URI* endpoint:

     "osDisk": {"name": "osdisk","vhd": {"uri":
     "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
      '/',variables('OSDiskName'),'.vhd')]"}}

## <a name="api-versioning"></a>Controllo delle versioni API

Le versioni dei servizi di Azure possono differire tra Azure e Azure Stack. Ogni risorsa richiede il **apiVersion** attributo, che definisce le funzionalità offerte. Per garantire la compatibilità di versione API nello Stack di Azure, le versioni API seguenti sono valide per ogni Provider di risorse:

| Provider di risorse | apiVersion |
| --- | --- |
| Calcolo |`'2015-06-15'` |
| Rete |`'2015-06-15'`, `'2015-05-01-preview'` |
| Archiviazione |`'2016-01-01'`, `'2015-06-15'`, `'2015-05-01-preview'` |
| Insieme di credenziali delle chiavi | `'2015-06-01'` |
| Servizio app |`'2015-08-01'` |

## <a name="template-functions"></a>Funzioni di modello

Gestione risorse di Azure [funzioni](../../azure-resource-manager/resource-group-template-functions.md) forniscono le funzionalità necessarie per compilare modelli dinamici. Ad esempio, è possibile usare funzioni per attività come:

* Concatenazione o l'eliminazione di stringhe.
* Fare riferimento ai valori di altre risorse.
* Esegue l'iterazione sulle risorse per distribuire più istanze.

Queste funzioni non sono disponibili nello Stack di Azure:

* Skip
* Take

## <a name="resource-location"></a>Percorso risorsa

Modelli di Azure Resource Manager di utilizzare un attributo di percorso per inserire le risorse durante la distribuzione. In Azure i percorsi fanno riferimento a un'area come Stati Uniti occidentali o Sud America. In Azure Stack i percorsi sono diversi perché Azure Stack si trova nel data center. Per garantire che i modelli siano trasferibili tra Azure e Azure Stack, è necessario fare riferimento al percorso del gruppo di risorse durante la distribuzione delle singole risorse. È possibile farlo usando `[resourceGroup().Location]` per garantire che tutte le risorse ereditano la posizione del gruppo di risorse. Nel frammento riportato di seguito è riportato un esempio dell'utilizzo di questa funzione durante la distribuzione di un account di archiviazione:

    "resources": [
    {
      "name": "[variables('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "[variables('apiVersionStorage')]",
      "location": "[resourceGroup().location]",
      "comments": "This storage account is used to store the VM disks",
      "properties": {
      "accountType": "Standard_GRS"
      }
    }
    ]

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire modelli con PowerShell](azure-stack-deploy-template-powershell.md)
* [Distribuire modelli con l'interfaccia della riga di comando di Azure](azure-stack-deploy-template-command-line.md)
* [Distribuire modelli con Visual Studio](azure-stack-deploy-template-visual-studio.md)
