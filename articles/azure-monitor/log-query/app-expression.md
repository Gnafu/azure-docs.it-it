---
title: Espressione app() nelle query di log di Monitoraggio di Azure | Microsoft Docs
description: L'espressione di app viene usata in una query di log di monitoraggio di Azure per recuperare dati da un'app specifica di Application Insights in stesso gruppo di risorse, un altro gruppo di risorse o un'altra sottoscrizione.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/25/2019
ms.author: bwren
ms.openlocfilehash: a1a605bc733597430f64dceeb6c485db0abf657b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60589240"
---
# <a name="app-expression-in-azure-monitor-query"></a>Espressione app() in una query di Monitoraggio di Azure

L'espressione `app` viene usata in una query di Monitoraggio di Azure per recuperare dati da un'app Application Insights specifica nello stesso gruppo di risorse, in un altro gruppo di risorse o in un'altra sottoscrizione. È utile per includere dati dell'applicazione in una query di log di Monitoraggio di Azure e per eseguire query su dati di più applicazioni in una query di Application Insights.



## <a name="syntax"></a>Sintassi

`app(`*Identificatore*`)`


## <a name="arguments"></a>Argomenti

- *Identificatore*: identifica l'app usando uno dei formati indicati nella tabella seguente.

| Identificatore | DESCRIZIONE | Esempio
|:---|:---|:---|
| Nome risorsa | Nome leggibile dell'app (anche noto come "nome componente") | app("fabrikamapp") |
| Nome completo | Nome completo dell'app nel formato: "subscriptionName/resourceGroup/componentName" | app('AI-Prototype/Fabrikam/fabrikamapp') |
| ID | GUID dell'app | app("988ba129-363e-4415-8fe7-8cbab5447518") |
| ID risorssa di Azure | Identificatore della risorsa di Azure |app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>Note

* È necessario disporre dell'accesso in lettura all'applicazione.
* L'identificazione di un'applicazione in base al nome presuppone che quest'ultimo sia univoco in tutte le sottoscrizioni accessibili. Se sono presenti più applicazioni con il nome specificato, la query avrà esito negativo a causa dell'ambiguità. In questo caso è necessario usare uno degli altri identificatori.
* Usare l'espressione correlata [area di lavoro](workspace-expression.md) per eseguire query tra aree di lavoro di Log Analytics.
* L'espressione app() non è attualmente supportata in query di ricerca quando si usa il portale di Azure per creare una [regola di avviso per i log personalizzata](../platform/alerts-log.md), a meno che non venga usata un'applicazione di Application Insights come risorsa per la regola di avviso.

## <a name="examples"></a>Esempi

```Kusto
app("fabrikamapp").requests | count
```
```Kusto
app("AI-Prototype/Fabrikam/fabrikamapp").requests | count
```
```Kusto
app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count
```
```Kusto
app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>Passaggi successivi

- Vedere l'[espressione workspace](workspace-expression.md) per fare riferimento a un'area di lavoro Log Analytics.
- Vedere altre informazioni su come vengono archiviati i [dati di Monitoraggio di Azure](../../azure-monitor/log-query/log-query-overview.md).
- Accedere alla documentazione completa relativa al [linguaggio di query Kusto](/azure/kusto/query/).
