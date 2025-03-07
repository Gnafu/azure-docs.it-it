---
title: Ridimensionare l'ambiente Time Series Insights di Azure | Microsoft Docs
description: Questo articolo descrive come seguire le procedure consigliate quando si pianifica un ambiente di Azure Time Series Insights. Le aree coperte includono capacità di archiviazione, conservazione dei dati, capacità in ingresso, monitoraggio e continuità aziendale e ripristino di emergenza (BCDR).
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/05/2019
ms.custom: seodec18
ms.openlocfilehash: 1e0fee903372668d30db0686f6a23dd913428454
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828179"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Pianificare l'ambiente Azure Time Series Insights GA

Questo articolo descrive come pianificare l'ambiente di Azure Time Series Insights disponibilità generale (GA) in base alla velocità di ingresso prevista e ai requisiti di conservazione dei dati.

## <a name="video"></a>Video

**Guarda questo video per scoprire di più sulla conservazione dei dati in Azure Time Series Insights e su come pianificarlo**:<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Procedure consigliate

Per iniziare a usare Time Series Insights, è consigliabile conoscere la quantità di dati che si prevede di eseguire il push in base al minuto e il tempo necessario per archiviare i dati.  

Per altre informazioni sulla capacità e sulla conservazione per entrambi gli SKU di Time Series Insights, vedere [Prezzi di Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Per pianificare meglio l'ambiente di Time Series Insights per il successo a lungo termine, considerare gli attributi seguenti:

- <a href="#storage-capacity">Capacità di archiviazione</a>
- <a href="#data-retention">Periodo di conservazione dei dati</a>
- <a href="#ingress-capacity">Capacità in ingresso</a>
- <a href="#shape-your-events">Data Shaping degli eventi</a>
- <a href="#ensure-that-you-have-reference-data">Assicurarsi di disporre di dati di riferimento sul posto</a>

## <a name="storage-capacity"></a>Capacità di archiviazione

Per impostazione predefinita, Time Series Insights mantiene i dati in base alla quantità di spazio di archiviazione di &#215; cui si esegue il provisioning (unità la quantità di spazio di archiviazione per unità) e in ingresso.

## <a name="data-retention"></a>Conservazione dei dati

È possibile modificare l'impostazione relativa al **tempo di conservazione dei dati** nell'ambiente Time Series Insights. È possibile abilitare fino a 400 giorni di conservazione. 

Time Series Insights dispone di due modalità. Una modalità ottimizza per garantire che l'ambiente disponga dei dati più aggiornati. Questa modalità è abilitata per impostazione predefinita. 

L'altra modalità ottimizza per garantire che vengano soddisfatti i limiti di conservazione. Nella seconda modalità, il traffico in ingresso viene sospeso se viene soddisfatta la capacità complessiva di archiviazione dell'ambiente. 

È possibile modificare la conservazione e passare tra le due modalità nella pagina di configurazione dell'ambiente nel portale di Azure.

Nell'ambiente Time Series Insights è possibile configurare un massimo di 400 giorni di conservazione dei dati.

### <a name="configure-data-retention"></a>Configurare la conservazione dei dati

1. Nel [portale di Azure](https://portal.azure.com) selezionare l'ambiente Time Series Insights desiderato.

1. Nel riquadro **ambiente Time Series Insights** in **Impostazioni**Selezionare **Configura**.

1. Nella casella **tempo di conservazione dati (in giorni)** immettere un valore compreso tra 1 e 400.

   [![Configurare la conservazione](media/environment-mitigate-latency/configure-retention.png)](media/environment-mitigate-latency/configure-retention.png#lightbox)

> [!TIP]
> Per ulteriori informazioni su come implementare i criteri di conservazione dei dati appropriati, vedere [come configurare la conservazione](./time-series-insights-how-to-configure-retention.md).

## <a name="ingress-capacity"></a>Capacità in ingresso

La seconda area in cui concentrarsi per la pianificazione dell'ambiente Time Series Insights è la capacità in ingresso. La capacità in ingresso è una derivata dell'allocazione al minuto.

Dal punto di vista della limitazione, un pacchetto di dati in ingresso con dimensioni del pacchetto pari a 32 KB viene considerato come 32 eventi, ogni 1 KB di dimensioni. La dimensione massima consentita per gli eventi è 32 KB. I pacchetti di dati di dimensioni superiori a 32 KB vengono troncati.

La tabella seguente riepiloga la capacità in ingresso per unità per ogni SKU di Time Series Insights:

|SKU  |Conteggio eventi al mese  |Dimensioni evento al mese  |Conteggio eventi al minuto  |Dimensioni evento al minuto  |
|---------|---------|---------|---------|---------|
|S1     |   30 milioni     |  30 GB     |  720    |  720 KB   |
|S2     |   300 milioni    |   300 GB   | 7\.200   | 7\.200 KB  |

È possibile aumentare la capacità di uno SKU S1 o S2 a 10 unità in un unico ambiente. Non è possibile eseguire la migrazione da un ambiente S1 a un S2. Non è possibile eseguire la migrazione da un ambiente S2 a un S1.

Per la capacità in ingresso, determinare innanzitutto il traffico in ingresso totale richiesto su base mensile. Determinare quindi quali sono le esigenze al minuto. 

La limitazione e la latenza svolgono un ruolo nella capacità al minuto. Se si ha un picco nel traffico di dati in ingresso che dura meno di 24 ore, Time Series Insights possibile "recuperare" in una velocità in ingresso di due volte le tariffe elencate nella tabella precedente.

Se, ad esempio, si dispone di un singolo SKU S1, i dati vengono ingresati a una velocità di 720 eventi al minuto e la velocità dei dati aumenta per meno di un'ora a una frequenza di 1.440 eventi o meno, non esiste una latenza evidente nell'ambiente. Tuttavia, se si superano 1.440 eventi al minuto per più di un'ora, è probabile che si verifichi una latenza nei dati visualizzati e disponibili per le query nell'ambiente in uso.

Potrebbe non essere possibile conoscere in anticipo la quantità di dati che si prevede di inserire. In questo caso, è possibile trovare i dati di telemetria per l' [Hub di Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) e gli hub [eventi di Azure](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) nella sottoscrizione di portale di Azure. I dati di telemetria consentono di determinare come eseguire il provisioning dell'ambiente. Usare il riquadro **metriche** nel portale di Azure per la rispettiva origine evento per visualizzare i dati di telemetria. Dopo aver analizzato attentamente le metriche relative all'origine evento, è possibile eseguire in modo più efficiente la pianificazione e il provisioning dell'ambiente Time Series Insights.

### <a name="calculate-ingress-requirements"></a>Calcolare i requisiti del traffico in ingresso

Per calcolare i requisiti di ingresso:

- Verificare che la capacità in ingresso si trovi al di sopra della Tariffa media al minuto e che l'ambiente sia sufficiente per gestire l'ingresso previsto equivalente a due volte la capacità per meno di un'ora.

- Se si verificano picchi di ingresso che durano più di un'ora, usare la velocità di picco come media. Eseguire il provisioning di un ambiente con la capacità di gestire la velocità di picco.

### <a name="mitigate-throttling-and-latency"></a>Ridurre la limitazione e la latenza

Per informazioni su come evitare la limitazione e la latenza, vedere [Ridurre la limitazione e la latenza](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Dare forma agli eventi

È importante assicurarsi che il modo in cui si inviano gli eventi a Time Series Insights supporti le dimensioni dell'ambiente di cui si esegue il provisioning. Viceversa, è possibile eseguire il mapping delle dimensioni dell'ambiente al numero di eventi Time Series Insights letture e alle dimensioni di ogni evento. È anche importante considerare gli attributi che è possibile usare per sezionare e filtrare in base al momento in cui si eseguono query sui dati.

> [!TIP]
> Esaminare la documentazione di data shaping in JSON per [l'invio di eventi](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).

## <a name="ensure-that-you-have-reference-data"></a>Assicurarsi di disporre di dati di riferimento

Un *set di dati di riferimento* è una raccolta di elementi che aumentano gli eventi dall'origine evento. Il motore di ingresso Time Series Insights unisce ogni evento dell'origine evento con la riga di dati corrispondente nel set di dati di riferimento. L'evento aumentato è quindi disponibile per la query. Il join è basato sulle colonne **chiave primaria** definite nel set di dati di riferimento.

> [!NOTE]
> I dati di riferimento non vengono uniti retroattivamente. Solo i dati in ingresso correnti e futuri vengono abbinati e aggiunti al set di dati di riferimento dopo che sono stati configurati e caricati. Se si prevede di inviare una grande quantità di dati cronologici a Time Series Insights senza prima caricare o creare dati di riferimento in Time Series Insights, potrebbe essere necessario ripetere il lavoro (Suggerimento: non divertente).  

Per altre informazioni su come creare, caricare e gestire i dati di riferimento in Time Series Insights, vedere la [documentazione del set](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set)di dati di riferimento.

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, creare [un nuovo ambiente di Time Series Insights nel portale di Azure](time-series-insights-get-started.md).

- Informazioni su come [aggiungere un'origine evento di hub eventi](time-series-insights-how-to-add-an-event-source-eventhub.md) a Time Series Insights.

- Leggere le informazioni su come [configurare un'origine evento dell'hub](time-series-insights-how-to-add-an-event-source-iothub.md)Internet.
