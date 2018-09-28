---
title: Eseguire ricerche su più risorse con Azure Log Analytics | Microsoft Docs
description: Questo articolo illustra come eseguire query sulle risorse di più aree di lavoro e sull'app di Application Insights nella sottoscrizione.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 716f96b7723a947c35eb54acd67f13261363fd38
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972879"
---
# <a name="perform-cross-resource-log-searches-in-log-analytics"></a>Eseguire ricerche nei log di più risorse con Log Analytics  

Azure Log Analytics consentiva in precedenza di analizzare i dati solo all'interno dell'area di lavoro corrente, limitando la possibilità di eseguire query tra più aree di lavoro definite nella sottoscrizione.  Inoltre, era possibile solo cercare gli elementi di telemetria raccolti dall'applicazione basata sul Web con Application Insights direttamente in Application Insights o in Visual Studio.  Per questi motivi, risultava difficile anche analizzare insieme in modo nativo i dati operativi e quelli dell'applicazione.   

Ora è possibile eseguire query non solo tra più aree di lavoro di Log Analytics, ma anche su dati di un'app specifica di Application Insights nello stesso gruppo di risorse, in un altro gruppo di risorse o in un'altra sottoscrizione. Si ottiene così una vista dei dati dell'intero sistema.  È possibile eseguire questi tipi di query solo in [Log Analytics](log-analytics-log-search-portals.md#log-analytics-page). Il numero di risorse (aree di lavoro di Log Analytics e app Application Insights) che è possibile includere in una singola query è limitato a 100. 

## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Esecuzione di query tra aree di lavoro di Log Analytics e da Application Insights
Per fare riferimento a un'altra area di lavoro nella query, usare l'identificatore [*workspace*](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/workspace()), mentre per un'app di Application Insights usare l'identificatore [*app*](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/app()).  

### <a name="identifying-workspace-resources"></a>Identificazione delle risorse dell'area di lavoro
Gli esempi seguenti dimostrano che le query eseguite su più aree di lavoro di Log Analytics restituiscono un riepilogo del numero di log dalla tabella di aggiornamento in un'area di lavoro denominata *contosoretail-it*. 

Esistono vari modi per identificare un'area di lavoro:

* Nome risorsa - Un nome dell'area di lavoro leggibile dall'utente, spesso definito genericamente *nome del componente*. 

    `workspace("contosoretail").Update | count`
 
    >[!NOTE]
    >L'identificazione di un'area di lavoro in base al nome presuppone che il nome sia univoco in tutte le sottoscrizioni accessibili. Se sono presenti più applicazioni con il nome specificato, la query avrà esito negativo a causa dell'ambiguità. In questo caso è necessario usare uno degli altri identificatori.

* Nome completo - È il nome completo dell'area di lavoro, composto dal nome della sottoscrizione, dal gruppo di risorse e dal nome del componente nel formato seguente: *subscriptionName/resourceGroup/componentName*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count `

    >[!NOTE]
    >Poiché i nomi delle sottoscrizioni di Azure non sono univoci, questo identificatore potrebbe essere ambiguo. 
    >

* ID area di lavoro - L'ID dell'area di lavoro è l'identificatore univoco e non modificabile assegnato a ogni area di lavoro rappresentato come identificatore univoco globale (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* ID risorsa Azure - L'identità univoca definita da Azure dell'area di lavoro. Si usa quando il nome della risorsa è ambiguo.  Per le aree di lavoro, il formato è: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/workspaces/componentName*.  

    Ad esempio: 
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Update | count
    ```

### <a name="identifying-an-application"></a>Identificazione di un'applicazione
Gli esempi seguenti restituiscono il riepilogo del numero di richieste eseguite su un'app denominata *fabrikamapp* in Application Insights. 

L'identificazione di un'applicazione in Application Insights può essere eseguita con l'espressione *app(Identifier)*.  L'argomento *Identifier* specifica l'app usando uno degli elementi seguenti:

* Nome risorsa - Nome dell'app leggibile dall'utente, spesso definito genericamente *nome del componente*.  

    `app("fabrikamapp")`

* Nome completo - Nome completo dell'app, composto dal nome della sottoscrizione, dal gruppo di risorse e dal nome del componente nel formato seguente: *subscriptionName/resourceGroup/componentName*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Poiché i nomi delle sottoscrizioni di Azure non sono univoci, questo identificatore potrebbe essere ambiguo. 
    >

* ID - GUID dell'app.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* ID risorsa Azure - Identità univoca dell'app definita da Azure. Si usa quando il nome della risorsa è ambiguo. Il formato è: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/components/componentName*.  

    Ad esempio: 
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Esecuzione di una query in più risorse
È possibile eseguire una query in più risorse da una qualsiasi delle istanze di risorse, ad esempio aree di lavoro e app combinate.
    
Esempio di query in due aree di lavoro:    
    ```
    union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
    | where TimeGenerated >= ago(1h)
    | where UpdateState == "Needed"
    | summarize dcount(Computer) by Classification
    ```

## <a name="next-steps"></a>Passaggi successivi

Vedere il [riferimento alla ricerca nei log di Log Analytics](https://docs.loganalytics.io/docs/Language-Reference) per visualizzare tutte le opzioni della sintassi di query disponibili in Log Analytics.    
