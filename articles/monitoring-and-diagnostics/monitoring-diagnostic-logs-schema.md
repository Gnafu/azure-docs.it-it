---
title: Servizi e schemi supportati per i log di Diagnostica di Azure | Microsoft Docs
description: Informazioni sullo schema di eventi e i servizi per i log di Diagnostica di Azure.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/12/2018
ms.author: johnkem
ms.openlocfilehash: 91c3f1507bb4fb64d5395917e8e431951f77e72b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Servizi, schemi e categorie supportati per i log di Diagnostica di Azure

I [log di diagnostica delle risorse di Azure](monitoring-overview-of-diagnostic-logs.md) vengono generati dalle risorse di Azure e descrivono il funzionamento di tale risorsa. Questi log sono specifici del tipo di risorsa. In questo articolo vengono descritti l'insieme di servizi supportati e lo schema eventi per gli eventi generati da ogni servizio. Questo articolo include anche un elenco completo delle categorie di log disponibili per ogni tipo di risorsa.

## <a name="supported-services-and-schemas-for-resource-diagnostic-logs"></a>Servizi e schemi supportati per i log di diagnostica delle risorse
Lo schema per i log di diagnostica di risorsa varia a seconda della risorsa e della categoria di log.   

| Service | Schema e documenti |
| --- | --- |
| Analysis Services | Lo schema non è disponibile. |
| Gestione API | [Log di diagnostica di Gestione API](../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Gateway applicazione |[Registrazione diagnostica per il gateway applicazione](../application-gateway/application-gateway-diagnostics.md) |
| Automazione di Azure |[Analisi dei log per Automazione di Azure](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Registrazione diagnostica di Azure Batch](../batch/batch-diagnostics.md) |
| Customer Insights | Lo schema non è disponibile. |
| Rete per la distribuzione di contenuti (CDN) | Lo schema non è disponibile. |
| Cosmos DB | [Registrazione di Azure Cosmos DB](../cosmos-db/logging.md) |
| Data Lake Analytics |[Accesso ai log di diagnostica per Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Archivio Data Lake |[Accesso ai log di diagnostica per Archivio Data Lake di Azure](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Hub eventi |[Log di diagnostica di Hub eventi in Azure](../event-hubs/event-hubs-diagnostic-logs.md) |
| Hub IoT | [Operazioni dell'hub IoT](../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Insieme di credenziali di chiave |[Registrazione dell'insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-logging.md) |
| Bilanciamento del carico |[Analisi dei log per Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md) |
| App per la logica |[Schema di rilevamento personalizzato per le app per la logica B2B](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Gruppi di sicurezza di rete |[Analisi dei log per i gruppi di sicurezza di rete](../virtual-network/virtual-network-nsg-manage-log.md) |
| Protezione DDoS | Lo schema non è disponibile. |
| Servizi di ripristino | [Modello di dati per Backup di Microsoft Azure](../backup/backup-azure-reports-data-model.md)|
| Ricerca |[Abilitazione e uso di Analisi del traffico di ricerca](../search/search-traffic-analytics.md) |
| Gestione server | Lo schema non è disponibile. |
| Bus di servizio |[Log di diagnostica del bus di servizio di Azure](../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Database SQL | [Registrazione diagnostica del database SQL di Azure](../sql-database/sql-database-metrics-diag-logging.md) |
| Analisi dei flussi |[Log di diagnostica dei processi](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Reti virtuali | Lo schema non è disponibile. |

## <a name="supported-log-categories-per-resource-type"></a>Categorie di log supportate per tipo di risorsa
|Tipo di risorsa|Categoria|Nome visualizzato della categoria|
|---|---|---|
|Microsoft.AnalysisServices/servers|Engine (Motore)|Engine (Motore)|
|Microsoft.AnalysisServices/servers|Service|Service|
|Microsoft.ApiManagement/service|GatewayLogs|Log correlati ad ApiManagement Gateway|
|Microsoft.Automation/automationAccounts|JobLogs|Log del processo|
|Microsoft.Automation/automationAccounts|JobStreams|Flussi del processo|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Stato del nodo Dsc|
|Microsoft.Batch/batchAccounts|ServiceLog|Log del servizio|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Ottiene le metriche dell'endpoint, ad esempio la larghezza di banda, i dati in uscita e così via|
|Microsoft.CustomerInsights/hubs|AuditEvents|Eventi di controllo|
|Microsoft.DataFactory/factories|ActivityRuns|Log delle esecuzioni di attività pipeline|
|Microsoft.DataFactory/factories|PipelineRuns|Log delle esecuzioni di pipeline|
|Microsoft.DataFactory/factories|TriggerRuns|Log delle esecuzioni trigger|
|Microsoft.DataLakeAnalytics/accounts|Audit|Log di controllo|
|Microsoft.DataLakeAnalytics/accounts|Requests|Log delle richieste|
|Microsoft.DataLakeStore/accounts|Audit|Log di controllo|
|Microsoft.DataLakeStore/accounts|Requests|Log delle richieste|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|Log del server PostgreSQL|
|Microsoft.Devices/IotHubs|connessioni|connessioni|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Telemetria dei dispositivi|
|Microsoft.Devices/IotHubs|C2DCommands|Comandi da cloud a dispositivo|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Operazioni relative alle identità dei dispositivi|
|Microsoft.Devices/IotHubs|FileUploadOperations|Operazioni di caricamento file|
|Microsoft.Devices/IotHubs|Route|Route|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|Operazioni da cloud a dispositivi gemelli|
|Microsoft.Devices/IotHubs|TwinQueries|Query dei dispositivi gemelli|
|Microsoft.Devices/IotHubs|JobsOperations|Operazioni dei processi|
|Microsoft.Devices/IotHubs|DirectMethods|Metodi diretti|
|Microsoft.Devices/IotHubs|E2EDiagnostics|E2E Diagnostics (anteprima)|
|Microsoft.Devices/provisioningServices|DeviceOperations|Operazioni del dispositivo|
|Microsoft.Devices/provisioningServices|ServiceOperations|Operazioni di servizio|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.EventHub/namespaces|ArchiveLogs|Log di archiviazione|
|Microsoft.EventHub/namespaces|OperationalLogs|Log operativi|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Log di scalabilità automatica|
|Microsoft.KeyVault/vaults|AuditEvent|Log di controllo|
|Microsoft.Logic/workflows|WorkflowRuntime|Eventi di diagnostica del runtime del flusso di lavoro|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Eventi di rilevamento degli account di integrazione|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Event del gruppo di sicurezza di rete|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Contatore di regole del gruppo di sicurezza di rete|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Eventi di avviso del servizio di bilanciamento del carico|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Stato di integrità dei probe del servizio di bilanciamento del carico|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|Notifiche di protezione DDoS|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Avvisi di protezione VM|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Log di accesso del gateway applicazione|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Log delle prestazioni del gateway applicazione|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Log del firewall del gateway applicazione|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Log di diagnostica del gateway|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Log di diagnostica del tunnel|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Log di diagnostica della route|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|Log di diagnostica IKE|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|Log di diagnostica P2S|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Evento dei risultati di integrità dei probe di Traffic Manager|
|Microsoft.Network/expressRouteCircuits|GWMCountersTable|Tabella di contatori GWM|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Dati dei report di Backup di Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Processi di Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Eventi di Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Elementi replicati di Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Statistiche di replica di Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Punti di ripristino di Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Velocità di caricamento dei dati di replica di Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Varianza dei dati del disco protetti di Azure Site Recovery|
|Microsoft.Search/searchServices|OperationLogs|Log delle operazioni|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Log operativi|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|Statistiche di runtime di Query Store|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|Statistiche relative alle attese di Query Store|
|Microsoft.Sql/servers/databases|Errors|Errors|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Statistiche relative alle attese del database|
|Microsoft.Sql/servers/databases|Timeout|Timeout|
|Microsoft.Sql/servers/databases|Blocchi|Blocchi|
|Microsoft.Sql/servers/databases|SQLInsights|SQL Insights|
|Microsoft.Sql/servers/databases|Audit|Log di controllo|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|Evento di controllo di sicurezza SQL|
|Microsoft.StreamAnalytics/streamingjobs|Esecuzione|Esecuzione|
|Microsoft.StreamAnalytics/streamingjobs|Creazione|Creazione|

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sui log di diagnostica](monitoring-overview-of-diagnostic-logs.md)
* [Trasmettere log di diagnostica di Azure a **Hub eventi**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Modificare le impostazioni di diagnostica di risorsa usando l'API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Analizzare i log di Archiviazione di Azure con Log Analytics](../log-analytics/log-analytics-azure-storage.md)
