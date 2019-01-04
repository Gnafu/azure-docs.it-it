---
title: Origini dei dati di monitoraggio
description: Informazioni su tutte le origini dati di monitoraggio disponibili in Azure al momento.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: 62474aba9eb429755ff5e7c78725ee3ce2d90117
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53728211"
---
# <a name="consume-monitoring-data-from-azure"></a>Uso di dati di monitoraggio in Azure

Nella piattaforma di Azure il monitoraggio dati e la pipeline di Monitoraggio di Azure sono stati unificati in un unica posizione, ma è noto che attualmente non tutti i dati di monitoraggio sono disponibili nella pipeline. In questo articolo vengono riepilogati i vari metodi per accedere a livello di programmazione ai dati di monitoraggio dai servizi di Azure.

## <a name="options-for-data-consumption"></a>Opzioni per l'uso di dati

| Tipo di dati | Categoria | Servizi supportati | Metodi di accesso |
| --- | --- | --- | --- |
| Metriche a livello di piattaforma di Monitoraggio di Azure | Metriche | [Vedere l'elenco di seguito](metrics-supported.md) | <ul><li>**API REST:** [API delle metriche di Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/metrics)</li><li>**BLOB del servizio di archiviazione o hub eventi:** [Impostazioni di diagnostica](diagnostic-logs-overview.md#diagnostic-settings)</li></ul> |
| Metriche del sistema operativo guest di calcolo (ad esempio contatori delle prestazioni) | Metriche | Macchine virtuali di [Windows](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) e Linux (v2), [servizi cloud](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**BLOB o tabella di archiviazione:** [Diagnostica di Azure per Windows o Linux](diagnostics-extension-to-storage.md)</li><li>**Hub eventi:** [Diagnostica di Azure per Windows](diagnostics-extension-stream-event-hubs.md)</li></ul> |
| Metriche personalizzate o dell'applicazione | Metriche | Un'applicazione qualsiasi dotata di Application Insights | <ul><li>**API REST:** [API REST di Application Insights](https://dev.applicationinsights.io/reference)</li></ul> |
| Metriche di archiviazione | Metrica | Archiviazione di Azure | <ul><li>**Tabella di archiviazione:** [Analisi archiviazione](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Dati di fatturazione | Metriche | Tutti i servizi di Azure | <ul><li>**API REST:** [API di uso delle risorse di Azure e API RateCard](../../billing/billing-usage-rate-card-overview.md)</li></ul> |
| Log attività | Eventi | Tutti i servizi di Azure | <ul><li>**API REST:** [API per gli eventi di Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/eventcategories)</li><li>**BLOB del servizio di archiviazione o hub eventi:** [Profilo del log](activity-logs-overview.md#export-the-activity-log-with-a-log-profile)</li></ul> |
| Log di diagnostica di Monitoraggio di Azure | Eventi | [Vedere l'elenco di seguito](tutorial-dashboards.md) | <ul><li>**BLOB del servizio di archiviazione o hub eventi:** [Impostazioni di diagnostica](diagnostic-logs-overview.md#diagnostic-settings)</li></ul> |
| Log del sistema operativo guest di calcolo (ad esempio IIS, ETW, SysLog) | Eventi | Macchine virtuali di [Windows](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) e Linux (v2), [servizi cloud](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**BLOB o tabella di archiviazione:** [Diagnostica di Azure per Windows o Linux](diagnostics-extension-to-storage.md)</li><li>**Hub eventi:** [Diagnostica di Azure per Windows](diagnostics-extension-stream-event-hubs.md)</li></ul> |
| Log del servizio app | Eventi | Servizi app | <ul><li>**Archiviazione BLOB, tabella o file:** [Diagnostica app Web](../../app-service/troubleshoot-diagnostic-logs.md)</li></ul> |
| Log di archiviazione | Eventi | Archiviazione di Azure | <ul><li>**Tabella di archiviazione:** [Analisi archiviazione](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Avvisi del Centro sicurezza | Eventi | Centro sicurezza di Azure | <ul><li>**API REST:** [Avvisi di sicurezza](https://msdn.microsoft.com/library/mt704050.aspx)</li></ul> |
| Creazione di report in Active Directory | Eventi | Azure Active Directory | <ul><li>**API REST:** [API Graph di Azure Active Directory](../../active-directory/reports-monitoring/concept-reporting-api.md)</li></ul> |
| Stato delle risorse del Centro sicurezza | Status | [Tutte le risorse supportate](https://msdn.microsoft.com/library/mt704041.aspx#Anchor_1) | <ul><li>**API REST:** [stati di sicurezza](https://msdn.microsoft.com/library/mt704041.aspx)</li></ul> |
| Integrità delle risorse | Status | Servizi supportati | <ul><li>**API REST:** [API REST per l'integrità delle risorse](https://azure.microsoft.com/blog/reduce-troubleshooting-time-with-azure-resource-health/)</li></ul> |
| Avvisi delle metriche di Monitoraggio di Azure | Notifiche | [Vedere l'elenco di seguito](metrics-supported.md) | <ul><li>**Webhook:** [avvisi delle metriche di Azure](alerts-webhooks.md)</li></ul> |
| Avvisi di Log attività di Monitoraggio di Azure | Notifiche | Tutti i servizi di Azure | <ul><li>**Webhook:** avvisi dei log attività di Azure</li></ul> |
| Notifiche relative alla scalabilità automatica | Notifiche | [Vedere l'elenco di seguito](autoscale-overview.md#supported-services-for-autoscale) | <ul><li>**Webhook:** [schema di payload del webhook di notifica di scalabilità automatica](autoscale-webhook-email.md#autoscale-notification-webhook-payload-schema)</li></ul> |
| Avvisi di query di ricerca log | Notifiche | Log Analytics | <ul><li>**Webhook:** [azione webhook per le regole di avviso relative ai log](alerts-log-webhook.md)</li></ul> |
| Avvisi relativi alle metriche in Application Insights | Notifiche | Application Insights | <ul><li>**Webhook:** [avvisi di Application Insights](../../azure-monitor/app/alerts.md)</li></ul> |
| Test Web in Application Insights | Notifiche | Application Insights | <ul><li>**Webhook:** [avvisi di Application Insights](../../azure-monitor/app/alerts.md)</li></ul> |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [metriche di Monitoraggio di Azure](data-collection.md)
- Altre informazioni su [Log attività di Azure](activity-logs-overview.md)
- Altre informazioni sui [Log di diagnostica di Azure](diagnostic-logs-overview.md)
