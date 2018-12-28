---
title: Raccogliere log e metriche per i servizi di Azure per Log Analytics | Documentazione Microsoft
description: È possibile configurare la diagnostica per le risorse di Azure per scrivere log e metriche in Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: magoedte
ms.openlocfilehash: 6b5fc02e763b66f1d7d01643d1c9d6c971d6db84
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2018
ms.locfileid: "53434206"
---
# <a name="collect-azure-service-logs-and-metrics-for-use-in-log-analytics"></a>Raccolta di log e metriche per i servizi di Azure da usare in Log Analytics

Esistono quattro diversi modi per raccogliere log e metriche per i servizi di Azure:

1. Da Diagnostica di Azure direttamente a Log Analytics (*Diagnostica* nella tabella seguente)
2. Da Diagnostica di Azure ad Archiviazione di Azure a Log Analytics (*Archiviazione* nella tabella seguente)
3. Connettori per i servizi di Azure (*Connettori* nella tabella seguente)
4. Script per raccogliere e inviare i dati a Log Analytics (spazi vuoti nella tabella seguente e per i servizi non elencati)


| Service                 | Tipo di risorsa                           | Log        | Metriche     | Soluzione |
| --- | --- | --- | --- | --- |
| Gateway di applicazione    | Microsoft.Network/applicationGateways   | Diagnostica | Diagnostica | [Analisi dei gateway applicazione di Azure](../../azure-monitor/insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-log-analytics) |
| Application Insights    |                                         | Connettore   | Connettore   | [Application Insights Connector](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) (anteprima) |
| Account di Automazione     | Microsoft.Automation/AutomationAccounts | Diagnostica |             | [Altre informazioni](../../automation/automation-manage-send-joblogs-log-analytics.md)|
| Account Batch          | Microsoft.Batch/batchAccounts           | Diagnostica | Diagnostica | |
| Servizi cloud classici  |                                         | Archiviazione     |             | [Altre informazioni](azure-storage-iis-table.md) |
| Servizi cognitivi      | Microsoft.CognitiveServices/accounts    |             | Diagnostica | |
| Data Lake Analytics     | Microsoft.DataLakeAnalytics/accounts    | Diagnostica |             | |
| Data Lake Store         | Microsoft.DataLakeStore/accounts        | Diagnostica |             | |
| Spazio dei nomi dell'hub eventi     | Microsoft.EventHub/namespaces           | Diagnostica | Diagnostica | |
| Hub IoT                | Microsoft.Devices/IotHubs               |             | Diagnostica | |
| Key Vault               | Microsoft.KeyVault/vaults               | Diagnostica |             | [KeyVault Analytics](../../azure-monitor/insights/azure-key-vault.md) |
| Servizi di bilanciamento del carico          | Microsoft.Network/loadBalancers         | Diagnostica |             |  |
| App per la logica              | Microsoft.Logic/workflows <br> Microsoft.Logic/integrationAccounts | Diagnostica | Diagnostica | |
| Gruppi di sicurezza di rete | Microsoft.Network/networksecuritygroups | Diagnostica |             | [Analisi del gruppo di sicurezza di rete di Azure](../../azure-monitor/insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) |
| Insiemi di credenziali di ripristino         | Microsoft.RecoveryServices/vaults       |             |             | [Azure Recovery Services Analytics (Anteprima)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
| Servizi di ricerca         | Microsoft.Search/searchServices         | Diagnostica | Diagnostica | |
| Spazio dei nomi del bus di servizio   | Microsoft.ServiceBus/namespaces         | Diagnostica | Diagnostica | [Service Bus Analytics (Anteprima)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
| Service Fabric          |                                         | Archiviazione     |             | [Service Fabric Analytics (anteprima)](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |
| SQL (versione 12)               | Microsoft.Sql/servers/databases <br> Microsoft.Sql/servers/elasticPools |             | Diagnostica | [Azure SQL Analytics (Anteprima)](../../azure-monitor/insights/azure-sql.md) |
| Archiviazione                 |                                         |             | Script      | [Azure Storage Analytics (Anteprima)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution) |
| Macchine virtuali        | Microsoft.Compute/virtualMachines       | Estensione   | Estensione <br> Diagnostica  | |
| Set di scalabilità di macchine virtuali | Microsoft.Compute/virtualMachines <br> Microsoft.Compute/virtualMachineScaleSets/virtualMachines |             | Diagnostica | |
| Server farm Web        | Microsoft.Web/serverfarms               |             | Diagnostica | |
| Microsoft Azure               | Microsoft.Web/sites <br> Microsoft.Web/sites/slots |             | Diagnostica | [Azure Web Apps Analytics (Anteprima)](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-web-apps-analytics) |


> [!NOTE]
> Per monitorare le macchine virtuali di Azure (sia Linux che Windows), è consigliabile installare l'[estensione macchina virtuale di Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md). L'agente fornisce informazioni dettagliate raccolte all'interno delle macchine virtuali. È anche possibile usare l'estensione per i set di scalabilità di macchine virtuali.
>
>

## <a name="azure-diagnostics-direct-to-log-analytics"></a>Da Diagnostica di Azure direttamente a Log Analytics
Molte risorse di Azure sono in grado di scrivere i log di diagnostica e le metriche direttamente in Log Analytics e questo è il modo migliore per raccogliere i dati per l'analisi. Quando si usa Diagnostica di Azure, i dati vengono scritti immediatamente in Log Analytics e non occorre scriverli prima nell'archiviazione.

Le risorse di Azure che supportano [Monitoraggio di Azure](../../azure-monitor/overview.md) possono inviare i log e le metriche direttamente a Log Analytics.

> [!NOTE]
> L'invio delle metriche multidimensionali a Log Analytics tramite impostazioni di diagnostica non è attualmente supportato. Le metriche con dimensioni sono esportate come metriche a singola dimensione di tipo flat e aggregate a livello di valori di dimensione.
>
> *Ad esempio*: La metrica 'Messaggi in arrivo' su un hub eventi può essere esplorata e rappresentata in un grafico a livello di singola coda. Tuttavia, in caso di esportazione tramite impostazione di diagnostica, la metrica viene rappresentata come tutti i messaggi in ingresso in tutte le code nell'hub eventi.
>
>

* Per informazioni dettagliate sulle metriche disponibili, vedere [Metriche supportate con il monitoraggio di Azure](../../azure-monitor/platform/metrics-supported.md).
* Per informazioni dettagliate sui registri disponibili, vedere [Servizi supportati e schema per i log di diagnostica](../../azure-monitor/platform/tutorial-dashboards.md).

### <a name="enable-diagnostics-with-powershell"></a>Abilitare la diagnostica con PowerShell
È necessaria la versione di novembre 2016 (2.3.0) o versione successiva di [Azure PowerShell](/powershell/azure/overview).

L'esempio di PowerShell seguente mostra come usare [Set-AzureRmDiagnosticSetting](/powershell/module/azurerm.insights/set-azurermdiagnosticsetting) per abilitare la diagnostica in un gruppo di sicurezza di rete. Lo stesso approccio funziona per tutte le risorse supportate. Impostare `$resourceId` sull'ID della risorsa per cui si vuole abilitare la diagnostica.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzureRmDiagnosticSetting -ResourceId $ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="enable-diagnostics-with-resource-manager-templates"></a>Abilitare la diagnostica con modelli di Resource Manager

Per abilitare la diagnostica per una risorsa al momento della creazione e inviare i dati di diagnostica all'area di lavoro di Log Analytics, è possibile usare un modello simile al seguente. Questo esempio è riferito a un account di Automazione, ma funziona per tutti i tipi di risorse supportati.

```json
        {
            "type": "Microsoft.Automation/automationAccounts/providers/diagnosticSettings",
            "name": "[concat(parameters('omsAutomationAccountName'), '/', 'Microsoft.Insights/service')]",
            "apiVersion": "2015-07-01",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('omsAutomationAccountName'))]",
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspaceName'))]"
            ],
            "properties": {
                "workspaceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('omsWorkspaceName'))]",
                "logs": [
                    {
                        "category": "JobLogs",
                        "enabled": true
                    },
                    {
                        "category": "JobStreams",
                        "enabled": true
                    }
                ]
            }
        }
```

[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="azure-diagnostics-to-storage-then-to-log-analytics"></a>Da Diagnostica di Azure ad Archiviazione di Azure e poi a Log Analytics

Per raccogliere i log da alcune risorse, è possibile inviare i log ad Archiviazione di Azure e quindi configurare Log Analytics per leggere i log dall'archiviazione.

Log Analytics può usare questo approccio per raccogliere i dati di diagnostica da Archiviazione di Azure per le risorse e i log seguenti:

| Risorsa | Log |
| --- | --- |
| Service Fabric |ETWEvent <br> Evento operativo <br> Evento Reliable Actor <br> Evento Reliable Service |
| Macchine virtuali |Syslog Linux <br> Evento Windows <br> Log IIS <br> ETWEvent Windows |
| Ruoli Web <br> Ruoli di lavoro |Syslog Linux <br> Evento Windows <br> Log IIS <br> ETWEvent Windows |

> [!NOTE]
> Quando si inviano dati di diagnostica a un account di archiviazione e quando Log Analytics legge i dati da tale account, vengono addebitate le normali tariffe dati di Azure per l'archiviazione e le transazioni.
>
>

Vedere [Uso dell'archivio BLOB per IIS e dell'archivio tabelle per gli eventi](azure-storage-iis-table.md) per altre informazioni sulle modalità di raccolta di questi log in Log Analytics.

## <a name="connectors-for-azure-services"></a>Connettori per i servizi di Azure

Esiste un connettore per Application Insights, che consente di inviare i dati raccolti da Application Insights a Log Analytics.

Altre informazioni sul [connettore Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/).

## <a name="scripts-to-collect-and-post-data-to-log-analytics"></a>Script per raccogliere i dati e inviarli a Log Analytics

Per i servizi di Azure che non forniscono un modo diretto per inviare i log e le metriche a Log Analytics, è possibile usare uno script di Automazione di Azure per raccogliere i log e le metriche. Lo script può quindi inviare i dati a Log Analytics tramite l'[API di raccolta dati](../../azure-monitor/platform/data-collector-api.md)

La raccolta di modelli di Azure include [esempi dell'uso di Automazione di Azure](https://azure.microsoft.com/resources/templates/?term=OMS) per raccogliere dati dai servizi e inviarli a Log Analytics.

## <a name="next-steps"></a>Passaggi successivi

* [Usare l'archiviazione BLOB per IIS e l'archiviazione tabelle per gli eventi](azure-storage-iis-table.md) per leggere i log dei servizi di Azure che scrivono dati di diagnostica in un archivio tabelle o i log IIS scritti in un archivio BLOB.
* [Abilitare soluzioni](../../azure-monitor/insights/solutions.md) per fornire informazioni dettagliate sui dati.
* [Usare query di ricerca](../../azure-monitor/log-query/log-query-overview.md) per analizzare i dati.
