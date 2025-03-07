---
title: Viste nelle soluzioni di gestione | Microsoft Docs
description: 'Le soluzioni di gestione includono in genere una o più viste per visualizzare i dati.  Questo articolo descrive come esportare una vista creata da Progettazione viste e includerla in una soluzione di gestione. '
services: monitoring
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 570b278c-2d47-4e5a-9828-7f01f31ddf8c
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/16/2018
ms.author: bwren
ms.openlocfilehash: cefb83d5336bb99fd09001b5ea369a0b8fc4b942
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60596638"
---
# <a name="views-in-management-solutions-preview"></a>Viste nelle soluzioni di gestione (anteprima)
> [!NOTE]
> Questo è un documento preliminare che illustra come creare soluzioni di gestione attualmente disponibili in versione di anteprima. Qualsiasi schema descritto di seguito è soggetto a modifiche.    


Le [soluzioni di gestione](solutions.md) includono in genere una o più viste per visualizzare i dati.  Questo articolo descrive come esportare una vista creata da [Progettazione viste](../../azure-monitor/platform/view-designer.md) e includerla in una soluzione di gestione.  

> [!NOTE]
> Gli esempi in questo articolo usano parametri e variabili che sono richiesti o comuni nelle soluzioni di gestione e che sono descritti in [Progettare e creare una soluzione di gestione in Azure](solutions-creating.md)
>
>

## <a name="prerequisites"></a>Prerequisiti
Questo articolo presuppone che si abbia già familiarità con le modalità di [creazione di una soluzione di gestione](solutions-creating.md) e la struttura di un file di soluzione.

## <a name="overview"></a>Panoramica
Per includere una vista in una soluzione di gestione, si crea una **risorsa** per la vista nel [file di soluzione](solutions-creating.md).  In genere, tuttavia, il codice JSON che descrive la configurazione dettagliata della vista è complesso e l'autore tipico di una soluzione probabilmente non è in grado di crearlo manualmente.  Il metodo più comune consiste nel creare la vista usando [Progettazione viste](../../azure-monitor/platform/view-designer.md), esportarla e quindi aggiungere la sua configurazione dettagliata alla soluzione.

I passaggi di base per aggiungere una vista a una soluzione sono i seguenti.  Ogni passaggio viene descritto in modo più dettagliato nelle sezioni successive.

1. Esportare la vista in un file.
2. Creare la risorsa vista nella soluzione.
3. Aggiungere i dettagli della vista.

## <a name="export-the-view-to-a-file"></a>Esportare la vista in un file
Seguire le istruzioni in [Progettazione viste di Log Analytics](../../azure-monitor/platform/view-designer.md) per esportare una vista in un file.  Il file esportato sarà nel formato JSON con gli stessi [elementi del file di soluzione](solutions-solution-file.md).  

L'elemento **resources** del file della vista avrà una risorsa di tipo **Microsoft.OperationalInsights/workspaces** che rappresenta l'area di lavoro Log Analytics.  Questo elemento avrà un sottoelemento di tipo **views** che rappresenta la vista e contiene la configurazione dettagliata.  Sarà necessario copiare i dettagli di questo elemento e incollarli nella soluzione.

## <a name="create-the-view-resource-in-the-solution"></a>Creare la risorsa vista nella soluzione
Aggiungere la risorsa vista seguente nell'elemento **resources** del file di soluzione.  Vengono usate le variabili descritte di seguito, che è necessario aggiungere.  Si noti che le proprietà **Dashboard** e **OverviewTile** sono segnaposto che verranno sovrascritti con le proprietà corrispondenti del file della vista esportato.

    {
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
        "type": "Microsoft.OperationalInsights/workspaces/views",
        "location": "[parameters('workspaceregionId')]",
        "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
        "dependson": [
            ],
        "properties": {
            "Id": "[variables('ViewName')]",
            "Name": "[variables('ViewName')]",
            "DisplayName": "[variables('ViewName')]",
            "Description": "",
            "Author": "[variables('ViewAuthor')]",
            "Source": "Local",
            "Dashboard": ,
            "OverviewTile":
        }
    }

Aggiungere le variabili seguenti all'elemento variables del file di soluzione e sostituire i valori con quelli della soluzione.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

Si noti che è possibile copiare l'intera risorsa vista dal file della vista esportato, ma è necessario apportare le modifiche seguenti per consentirne il funzionamento nella soluzione.  

* Il parametro **type** della risorsa vista deve essere modificato da **views** a **Microsoft.OperationalInsights/workspaces**.
* La proprietà **name** della risorsa vista deve essere modificata per includere il nome dell'area di lavoro.
* La dipendenza dall'area di lavoro deve essere rimossa perché la risorsa area di lavoro non è definita nella soluzione.
* La proprietà **DisplayName** deve essere aggiunta alla vista.  I valori di **Id**, **Name** e **DisplayName** devono corrispondere.
* I nomi dei parametri devono essere modificati in modo da corrispondere al set di parametri richiesto.
* Le variabili devono essere definite nella soluzione e usate nelle proprietà appropriate.

### <a name="log-analytics-api-version"></a>Versione API di Log Analytics
Tutte le risorse di Log Analytics definite in un modello di Resource Manager hanno una proprietà **apiVersion** che definisce la versione dell'API che la risorsa deve usare.  Questa versione è diversa per le viste con query che usano il [linguaggio di query legacy e quello aggiornato](../../azure-monitor/log-query/log-query-overview.md).  

 La tabella seguente illustra le versioni dell'API di Log Analytics relative alle viste nelle aree di lavoro legacy e aggiornate: 

| Versione dell'area di lavoro | Versione dell'API | Query |
|:---|:---|:---|
| v1 (legacy)   | 2015-11-01-preview | Formato legacy.<br> Esempio: Type=Event EventLevelName = Error  |
| v2 (aggiornata) | 2015-11-01-preview | Formato legacy.  Convertito in formato aggiornato al momento dell'installazione.<br> Esempio: Type=Event EventLevelName = Error<br>Convertito in: Event &#124; where EventLevelName == "Error"  |
| v2 (aggiornata) | 2017-03-03-preview | Formato aggiornato. <br>Esempio: Event &#124; where EventLevelName == "Error"  |


## <a name="add-the-view-details"></a>Aggiungere i dettagli della vista
La risorsa vista nel file della vista esportato conterrà due elementi nell'elemento **properties** denominati **Dashboard** e **OverviewTile** che contengono la configurazione dettagliata della vista.  Copiare questi due elementi e il relativo contenuto nell'elemento **properties** della risorsa vista nel file di soluzione.

## <a name="example"></a>Esempio
L'esempio seguente mostra un file di soluzione semplice con una vista.  Al posto del contenuto di **Dashboard** e **OverviewTile** sono visualizzati i puntini di sospensione (...) per motivi di spazio.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string"
            },
            "accountName": {
                "type": "string"
            },
            "workspaceRegionId": {
                "type": "string"
            },
            "regionId": {
                "type": "string"
            },
            "pricingTier": {
                "type": "string"
            }
        },
        "variables": {
            "SolutionVersion": "1.1",
            "SolutionPublisher": "Contoso",
            "SolutionName": "Contoso Solution",
            "LogAnalyticsApiVersion": "2015-11-01-preview",
            "ViewAuthor":  "user@contoso.com",
            "ViewDescription":  "This is a sample view.",
            "ViewName":  "Contoso View"
        },
        "resources": [
            {
                "name": "[concat(variables('SolutionName'), '(' ,parameters('workspacename'), ')')]",
                "location": "[parameters('workspaceRegionId')]",
                "tags": { },
                "type": "Microsoft.OperationsManagement/solutions",
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspacename'), '/views/', variables('ViewName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspacename'))]",
                    "referencedResources": [
                    ],
                    "containedResources": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
                    ]
                },
                "plan": {
                    "name": "[concat(variables('SolutionName'), '(' ,parameters('workspaceName'), ')')]",
                    "Version": "[variables('SolutionVersion')]",
                    "product": "ContosoSolution",
                    "publisher": "[variables('SolutionPublisher')]",
                    "promotionCode": ""
                }
            },
            {
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
                "type": "Microsoft.OperationalInsights/workspaces/views",
                "location": "[parameters('workspaceregionId')]",
                "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
                "dependson": [
                ],
                "properties": {
                    "Id": "[variables('ViewName')]",
                    "Name": "[variables('ViewName')]",
                    "DisplayName": "[variables('ViewName')]",
                    "Description": "[variables('ViewDescription')]",
                    "Author": "[variables('ViewAuthor')]",
                    "Source": "Local",
                    "Dashboard": ...,
                    "OverviewTile": ...
                }
            }
          ]
    }




## <a name="next-steps"></a>Passaggi successivi
* Leggere le informazioni complete sulla creazione di [soluzioni di gestione](solutions-creating.md).
* Includere [runbook di Automazione nella soluzione di gestione](solutions-resources-automation.md).
