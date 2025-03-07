---
title: Log del server per Database di Azure per MySQL
description: Viene descritto il log query lente disponibili nel Database di Azure per MySQL e i parametri disponibili per l'abilitazione di diversi livelli di registrazione.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 1a8956d40ef30e8d52fbdded3448019e14ab16a5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67062412"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>Log query lente nel Database di Azure per MySQL
Nel Database di Azure per MySQL, il log delle query lente è disponibile per gli utenti. L'accesso al log delle transazioni non è supportato. Il log delle query lente può essere usato per identificare eventuali colli di bottiglia delle prestazioni e procedere alla risoluzione dei problemi.

Per altre informazioni sul log delle query lente MySQL, vedere la [sezione relativa ai log di query lente](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) del manuale di riferimento per MySQL.

## <a name="access-slow-query-logs"></a>Accedere ai log query lente
È possibile elencare e scaricare i Database di Azure per i log di query lente MySQL usando il portale di Azure e la CLI di Azure.

Nel portale di Azure selezionare il server del Database di Azure per MySQL. Nell'intestazione **Monitoraggio** selezionare la pagina **Log del server**.

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere [Configurare e accedere ai log del server usando l'interfaccia della riga di comando di Azure](howto-configure-server-logs-in-cli.md).

## <a name="log-retention"></a>Conservazione dei log
I log sono disponibili per un massimo di sette giorni dalla data di creazione. Se le dimensioni totali dei log disponibili superano 7 GB, i file meno recenti vengono eliminati fino a quando non è disponibile dello spazio. 

I log vengono ruotati ogni 24 ore o 7 GB, a seconda del valore raggiunto per primo.

## <a name="configure-slow-query-logging"></a>Configurare la registrazione di query lente 
Per impostazione predefinita il log delle query lente è disabilitato. Per abilitarlo, impostare slow_query_log su ON.

Altri parametri che è possibile modificare includono:

- **long_query_time**: se una query richiede più tempo del valore di long_query_time (in secondi), la query viene registrata. Il valore predefinito è 10 secondi.
- **log_slow_admin_statements**: se è ON include le istruzioni a livello amministrativo come ALTER_TABLE e ANALYZE_TABLE nelle istruzioni scritte in slow_query_log.
- **log_queries_not_using_indexes**: determina se le query che non usano gli indici vengono registrate in slow_query_log
- **log_throttle_queries_not_using_indexes**: Questo parametro limita il numero di query non di indice che possono essere scritte nel log di query lente. Questo parametro ha effetto quando log_queries_not_using_indexes è impostato su ON.

Per una descrizione completa dei parametri per il log di query lente, vedere la [documentazione sul log di query lente](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) per MySQL.

## <a name="diagnostic-logs"></a>Log di diagnostica
Database di Azure per MySQL è integrato con i log di diagnostica di Monitoraggio di Azure. Dopo aver abilitato i log query lente nel proprio server MySQL, è possibile scegliere in modo che vengano inviati a log di monitoraggio di Azure, hub eventi o archiviazione di Azure. Per altre informazioni sull'abilitazione dei log di diagnostica, vedere la sezione sulle procedure della [documentazione sui log di diagnostica](../azure-monitor/platform/diagnostic-logs-overview.md).

> [!IMPORTANT]
> Questa funzionalità di diagnostica per i log del server è disponibile solo in utilizzo generico e ottimizzate per la memoria [sui piani tariffari](concepts-pricing-tiers.md).

La tabella seguente descrive il contenuto di ogni log. A seconda del metodo di output, è possibile che i campi inclusi e il relativo ordine di visualizzazione siano differenti.

| **Proprietà** | **Descrizione** |
|---|---|
| `TenantId` | ID del tenant. |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Timestamp in cui il log è stato registrato in formato UTC. |
| `Type` | Tipo di log. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID per la sottoscrizione a cui appartiene il server. |
| `ResourceGroup` | Nome del gruppo di risorse a cui appartiene il server. |
| `ResourceProvider` | Nome del provider di risorse. Sempre `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI della risorsa |
| `Resource` | Nome del server |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Nome del server |
| `start_time_t` [UTC] | Ora di inizio della query |
| `query_time_s` | Tempo totale di esecuzione della query |
| `lock_time_s` | Tempo totale in cui la query è rimasta bloccata |
| `user_host_s` | Username |
| `rows_sent_s` | Numero di righe inviate |
| `rows_examined_s` | Numero di righe esaminate |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | Inserire ID |
| `sql_text_s` | Query completa |
| `server_id_s` | ID del server |
| `thread_id_s` | ID del thread |
| `\_ResourceId` | URI della risorsa |

## <a name="next-steps"></a>Fasi successive
- [Come configurare e accedere ai log del server usando l'interfaccia della riga di comando di Azure](howto-configure-server-logs-in-cli.md).
