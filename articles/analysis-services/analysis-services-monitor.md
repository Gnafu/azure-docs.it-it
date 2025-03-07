---
title: Monitorare le metriche dei server di Azure Analysis Services | Microsoft Docs
description: Informazioni su come monitorare le metriche dei server di Analysis Services nel portale di Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7c7aaf911930f83775f66c47377bc68edb059519
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70958976"
---
# <a name="monitor-server-metrics"></a>Monitorare le metriche dei server

Analysis Services fornisce le metriche in Azure Esplora metriche, uno strumento gratuito nel portale, che consente di monitorare le prestazioni e l'integrità dei server. È possibile, ad esempio, monitorare la memoria e l'utilizzo di CPU, il numero di connessioni client ed eseguire query sull'utilizzo di risorse. Analysis Services usa lo stesso framework di monitoraggio usato dalla maggior parte degli altri servizi Azure. Per altre informazioni, vedere [Introduzione ad Azure Esplora metriche](../azure-monitor/platform/metrics-getting-started.md).

Per eseguire una diagnostica più approfondita, tenere traccia delle prestazioni, identificare le tendenze in più risorse del servizio in un gruppo di risorse o una sottoscrizione, usare [Monitoraggio di Azure](../azure-monitor/overview.md). Monitoraggio di Azure può generare un servizio fatturabile.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Per monitorare le metriche per un server Analysis Services

1. Nel portale di Azure selezionare **Metriche**.

    ![Monitoraggio nel portale di Azure](./media/analysis-services-monitor/aas-monitor-portal.png)

2. In **Metriche disponibili** selezionare le metriche da includere nel grafico. 

    ![Grafico di monitoraggio](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Metriche del server

Usare questa tabella per determinare le metriche più adatte allo scenario di monitoraggio. Nello stesso grafico possono essere visualizzate solo metriche della stessa unità.

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Lunghezza coda processi nel pool di comandi|Conteggio|Average|Numero dei processi nella coda del pool dei thread dei comandi.|
|CurrentConnections|Connessione: connessioni correnti|Conteggio|Average|Numero corrente delle connessioni client stabilite.|
|CurrentUserSessions|Sessioni utente correnti|Conteggio|Average|Numero corrente di sessioni utente attive.|
|mashup_engine_memory_metric|Memoria Motore M|Byte|Average|Utilizzo della memoria da parte dei processi del motore mashup|
|mashup_engine_qpu_metric|QPU Motore M|Conteggio|Average|Utilizzo QPU da parte dei processi motore mashup|
|memory_metric|Memoria|Byte|Average|Memoria. Intervallo 0-25 GB per S1, 0-50 GB per S2 e 0-100 GB per S4|
|memory_thrashing_metric|Thrashing di memoria|Percent|Average|Thrashing di memoria medio.|
|CleanerCurrentPrice|Memoria: prezzo corrente pulitura memoria|Conteggio|Average|Prezzo corrente della memoria, in €/byte/tempo, moltiplicato per 1000.|
|CleanerMemoryNonshrinkable|Memoria: pulitura memoria non compattabile|Byte|Average|Quantità di memoria, in byte, non soggetta a compattazione da parte della pulitura della memoria in background.|
|CleanerMemoryShrinkable|Memoria: pulitura memoria compattabile|Byte|Average|Quantità di memoria, in byte, soggetta a compattazione da parte della pulitura della memoria in background.|
|MemoryLimitHard|Memoria: limite memoria assoluto|Byte|Average|Limite assoluto della memoria, dal file di configurazione.|
|MemoryLimitHigh|Memoria: limite memoria massimo|Byte|Average|Limite superiore della memoria, dal file di configurazione.|
|MemoryLimitLow|Memoria: limite memoria minimo|Byte|Average|Limite inferiore della memoria, dal file di configurazione.|
|MemoryLimitVertiPaq|Memoria: limite memoria VertiPaq|Byte|Average|Limite in memoria dal file di configurazione.|
|MemoryUsage|Memoria: Utilizzo memoria|Byte|Average|Utilizzo della memoria del processo server utilizzato per il calcolo del prezzo di una memoria più pulita. Equivale al contatore Process\PrivateBytes più le dimensioni dei dati con mapping in memoria, ignorando la memoria con mapping o allocazione eseguita dal motore di analisi in memoria (VertiPaq) in eccesso rispetto al limite di memoria del motore.|
|private_bytes_metric|Byte privati |Byte|Average|Quantità totale di memoria allocata dal processo del motore Analysis Services e dai processi del contenitore mashup, esclusa la memoria condivisa con altri processi.|
|virtual_bytes_metric|Virtual Bytes |Byte|Average|Dimensioni correnti dello spazio degli indirizzi virtuali usato dal processo del motore Analysis Services e dai processi del contenitore mashup.|
|mashup_engine_private_bytes_metric|Byte privati motore M |Byte|Average|La quantità totale di processi del contenitore mashup di memoria è stata allocata, esclusa la memoria condivisa con altri processi.|
|mashup_engine_virtual_bytes_metric|Byte virtuali del motore M |Byte|Average|Dimensioni correnti dello spazio degli indirizzi virtuali che i processi del contenitore mashup usano.|
|Gestione quote|Memoria: Gestione quote|Byte|Average|Quota di memoria corrente, in byte. Le quote di memoria sono note anche come concessioni di memoria o prenotazioni di memoria.|
|QuotaBlocked|Memoria: quota bloccata|Conteggio|Average|Numero corrente di richieste di quota bloccate fino a quando non vengono liberate altre quote di memoria.|
|VertiPaqNonpaged|Memoria: VertiPaq non di paging|Byte|Average|Byte di memoria bloccata nel working set per l'uso da parte del motore in memoria.|
|VertiPaqPaged|Memoria: VertiPaq di paging|Byte|Average|Byte di memoria di paging in uso per i dati in memoria.|
|ProcessingPoolJobQueueLength|Lunghezza coda processi nel pool di elaborazione|Conteggio|Average|Numero dei processi non di I/O nella coda del pool dei thread di elaborazione.|
|RowsConvertedPerSec|Elaborazione: righe convertite al secondo|CountPerSecond|Average|Velocità di conversione delle righe durante l'elaborazione.|
|RowsReadPerSec|Elaborazione: righe lette al secondo|CountPerSecond|Average|Velocità di lettura delle righe da tutti i database relazionali.|
|RowsWrittenPerSec|Elaborazione: righe scritte al secondo|CountPerSecond|Average|Velocità di scrittura delle righe durante l'elaborazione.|
|qpu_metric|QPU|Conteggio|Average|QPU. Intervallo 0-100 per S1, 0-200 per S2 e 0-400 per S4|
|QueryPoolBusyThreads|Thread occupati pool di query|Conteggio|Average|Numero dei thread occupati nel pool dei thread di query.|
|SuccessfullConnectionsPerSec|Connessioni riuscite al secondo|CountPerSecond|Average|Numero delle connessioni completate correttamente al secondo.|
|CommandPoolBusyThreads|Thread: thread occupati nel pool di comandi|Conteggio|Average|Numero dei thread occupati nel pool dei thread dei comandi.|
|CommandPoolIdleThreads|Thread: thread inattivi nel pool di comandi|Conteggio|Average|Numero dei thread inattivi nel pool dei thread dei comandi.|
|LongParsingBusyThreads|Thread: thread occupati per analisi dei thread lunghi|Conteggio|Average|Numero dei thread occupati nel pool dei thread per l'analisi dei thread lunghi.|
|LongParsingIdleThreads|Thread: thread inattivi per analisi dei thread lunghi|Conteggio|Average|Numero dei thread inattivi nel pool dei thread per l'analisi dei thread lunghi.|
|LongParsingJobQueueLength|Thread: lunghezza coda processi di analisi dei thread lunghi|Conteggio|Average|Numero dei processi nella coda del pool dei thread per l'analisi dei thread lunghi.|
|ProcessingPoolIOJobQueueLength|Thread: lunghezza coda processi di I/O nel pool di elaborazione|Conteggio|Average|Numero di processi di I/O nella coda del pool dei thread di elaborazione.|
|ProcessingPoolBusyIOJobThreads|Thread: thread di processi di I/O occupati nel pool di elaborazione|Conteggio|Average|Numero di thread che eseguono processi di I/O nel pool dei thread di elaborazione.|
|ProcessingPoolBusyNonIOThreads|Thread: thread non di I/O occupati nel pool di elaborazione|Conteggio|Average|Numero dei thread che eseguono processi non di I/O nel pool dei thread di elaborazione.|
|ProcessingPoolIdleIOJobThreads|Thread: thread di processi di I/O inattivi nel pool di elaborazione|Conteggio|Average|Numero di thread inattivi per i processi di I/O nel pool dei thread di elaborazione.|
|ProcessingPoolIdleNonIOThreads|Thread: thread non di I/O inattivi nel pool di elaborazione|Conteggio|Average|Numero di thread inattivi nel pool dei thread di elaborazione dedicato a processi non di I/O.|
|QueryPoolIdleThreads|Thread: thread inattivi nel pool di query|Conteggio|Average|Numero di thread inattivi per i processi di I/O nel pool dei thread di elaborazione.|
|QueryPoolJobQueueLength|Thread: lunghezza coda processi nel pool di query|Conteggio|Average|Numero dei processi nella coda del pool dei thread di query.|
|ShortParsingBusyThreads|Thread: thread occupati per analisi dei thread brevi|Conteggio|Average|Numero dei thread occupati nel pool dei thread per l'analisi dei thread brevi.|
|ShortParsingIdleThreads|Thread: thread inattivi per analisi dei thread brevi|Conteggio|Average|Numero dei thread inattivi nel pool dei thread per l'analisi dei thread brevi.|
|ShortParsingJobQueueLength|Thread: lunghezza coda processi di analisi dei thread brevi|Conteggio|Average|Numero dei processi nella coda del pool dei thread per l'analisi dei thread brevi.|
|TotalConnectionFailures|Numero totale di errori di connessione|Conteggio|Average|Numero totale dei tentativi di connessione non riusciti.|
|TotalConnectionRequests|Numero totale di richieste di connessione|Conteggio|Average|Numero totale delle richieste di connessione |

## <a name="next-steps"></a>Passaggi successivi
[Panoramica di monitoraggio di Azure](../azure-monitor/overview.md)      
[Introduzione ad Azure Esplora metriche](../azure-monitor/platform/metrics-getting-started.md)      
[Metriche nell'API REST di Monitoraggio di Azure](/rest/api/monitor/metrics)
