---
title: Soluzione Capacity and Performance in Monitoraggio di Azure | Microsoft Docs
description: Usare la soluzione Capacity and Performance Monitor che aiutano a comprendere le capacità dei server Hyper-V.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 51617a6f-ffdd-4ed2-8b74-1257149ce3d4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: magoedte
ms.openlocfilehash: fcf71bf144b559c4867303988d4c1f08b7aa5605
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62101915"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-deprecated"></a>Pianificare la capacità di macchina virtuale Hyper-V con la soluzione Capacity and Performance (deprecata)

![Simbolo di Capacità e prestazioni](./media/capacity-performance/capacity-solution.png)

> [!NOTE]
> La Soluzione Capacity and Performance è stata deprecata.  I clienti che hanno già installato la soluzione possono continuare a usarla, anche se Capacità e prestazioni non può essere aggiunta ad alcuna nuova area di lavoro.

È possibile utilizzare la soluzione Capacity and Performance Monitor che aiutano a comprendere le capacità dei server Hyper-V. La soluzione fornisce informazioni approfondite sull'ambiente Hyper-V illustrando l'utilizzo generale (CPU, memoria e disco) dell'host e delle macchine virtuali in esecuzione negli host Hyper-V. Vengono acquisite le metriche relative a CPU, memoria e dischi di tutti gli host e delle macchine virtuali in esecuzione su tali server.

La soluzione:

-   Visualizza gli host con l'utilizzo più alto e più basso di CPU e memoria
-   Visualizza le VM con l'utilizzo più alto e più basso di CPU e memoria
-   Visualizza le VM con l'utilizzo più alto e più basso di operazioni di I/O al secondo e velocità effettiva
-   Visualizza le VM in esecuzione nei diversi host
-   Visualizza i dischi con velocità effettiva, operazioni di I/O al secondo e latenza più elevate nei volumi condivisi cluster
- Consente di personalizzare e applicare filtri in base ai gruppi

> [!NOTE]
> La versione precedente della soluzione Capacity and Performance, denominata Gestione della capacità, richiede sia System Center Operations Manager che System Center Virtual Machine Manager. Questa soluzione aggiornata non prevede tali dipendenze.


## <a name="connected-sources"></a>Origini connesse

La tabella seguente descrive le origini connesse che sono supportate da questa soluzione.

| Origine connessa | Supporto | DESCRIZIONE |
|---|---|---|
| [Agenti Windows](../../azure-monitor/platform/agent-windows.md) | Sì | La soluzione raccoglie informazioni su capacità e prestazioni dagli agenti Windows. |
| [Agenti Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) | No    | La soluzione non raccoglie informazioni su capacità e prestazioni dagli agenti Linux diretti.|
| [Gruppo di gestione SCOM](../../azure-monitor/platform/om-agents.md) | Sì |La soluzione raccoglie dati su capacità e prestazioni dagli agenti in un gruppo di gestione SCOM connesso. Non è necessaria una connessione diretta dall'agente SCOM a Log Analytics.|
| [Account di archiviazione di Azure](../../azure-monitor/platform/collect-azure-metrics-logs.md) | No | Archiviazione di Azure non include dati di capacità e prestazioni.|

## <a name="prerequisites"></a>Prerequisiti

- Gli agenti Windows o Operations Manager devono essere installati in host Hyper-V Windows Server 2012 o versione successiva, non in macchine virtuali.


## <a name="configuration"></a>Configurazione

Eseguire questo passaggio per aggiungere la soluzione Capacity and Performance all'area di lavoro.

- Aggiungere la soluzione Capacità e prestazioni all'area di lavoro Log Analytics usando la procedura descritta nell'articolo [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](../../azure-monitor/insights/solutions.md).

## <a name="management-packs"></a>Management Pack

Se il gruppo di gestione SCOM è connesso all'area di lavoro Log Analytics, i Management Pack seguenti verranno installati in SCOM quando si aggiunge questa soluzione. Per questi Management Pack non è richiesta alcuna configurazione o manutenzione.

- Microsoft.IntelligencePacks.CapacityPerformance

L'evento 1201 è simile al seguente:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

Quando la soluzione Capacity and Performance viene aggiornata, il numero di versione viene modificato.

Per maggiori informazioni sulla modalità di aggiornamento dei Management Pack, vedere [Connettere Operations Manager a Log Analytics](../../azure-monitor/platform/om-agents.md).

## <a name="using-the-solution"></a>Uso della soluzione

Quando si aggiunge la soluzione Capacity and Performance all'area di lavoro, Capacity and Performance viene aggiunta al dashboard Panoramica. Questo riquadro visualizza un conteggio del numero degli host Hyper-V attualmente attivi e del numero di macchine virtuali monitorate nel periodo selezionato.

![Riquadro Capacity and Performance](./media/capacity-performance/capacity-tile.png)


### <a name="review-utilization"></a>Esaminare l'utilizzo

Fare clic sul riquadro Capacity and Performance per aprire il dashboard Capacity and Performance. Il dashboard include le colonne nella tabella seguente. Ogni colonna elenca fino a dieci elementi corrispondenti ai criteri della colonna per l'ambito e l'intervallo di tempo specificati. È possibile eseguire una ricerca log per ottenere tutti i record facendo clic su **Vedi tutto** nella parte inferiore della colonna o facendo clic sull'intestazione di colonna.

- **Host**
    - **Utilizzo CPU host** Visualizza una tendenza grafica dell'utilizzo della CPU da parte dei computer host e un elenco di host, in base al periodo di tempo selezionato. Passare il puntatore sul grafico a linee per visualizzare i dettagli di uno specifico punto nel tempo. Fare clic sul grafico per visualizzare altri dettagli nella ricerca log. Fare clic su un nome host per aprire la ricerca log e visualizzare i dettagli dei contatori CPU per le VM ospitate.
    - **Utilizzo memoria host** Visualizza una tendenza grafica dell'utilizzo della memoria da parte dei computer host e un elenco di host, in base al periodo di tempo selezionato. Passare il puntatore sul grafico a linee per visualizzare i dettagli di uno specifico punto nel tempo. Fare clic sul grafico per visualizzare altri dettagli nella ricerca log. Fare clic su un nome host per aprire la ricerca log e visualizzare i dettagli del contatore della memoria per le VM ospitate.
- **Macchine virtuali**
    - **Utilizzo CPU VM** Visualizza una tendenza grafica dell'utilizzo della CPU da parte delle macchine virtuali e un elenco di macchine virtuali, in base al periodo di tempo selezionato. Passare il puntatore sul grafico a linee per visualizzare i dettagli di uno specifico punto nel tempo per le prime 3 VM. Fare clic sul grafico per visualizzare altri dettagli nella ricerca log. Fare clic su un nome VM per aprire la ricerca log e visualizzare i dettagli dei contatori CPU aggregati per le VM.
    - **Utilizzo memoria VM** Visualizza una tendenza grafica dell'utilizzo della memoria da parte delle macchine virtuali e un elenco di macchine virtuali, in base al periodo di tempo selezionato. Passare il puntatore sul grafico a linee per visualizzare i dettagli di uno specifico punto nel tempo per le prime 3 VM. Fare clic sul grafico per visualizzare altri dettagli nella ricerca log. Fare clic su un nome VM per aprire la ricerca log e visualizzare i dettagli dei contatori della memoria aggregati per le VM.
    - **Totale operazioni di I/O al secondo del disco della macchina virtuale** Visualizza una tendenza grafica del totale delle operazioni di I/O al secondo del disco per le macchine virtuali e un elenco di macchine virtuali con le operazioni di I/O al secondo per ognuna, in base al periodo di tempo selezionato. Passare il puntatore sul grafico a linee per visualizzare i dettagli di uno specifico punto nel tempo per le prime 3 VM. Fare clic sul grafico per visualizzare altri dettagli nella ricerca log. Fare clic su un nome VM per aprire la ricerca log e visualizzare i dettagli dei contatori di operazioni di I/O al secondo del disco aggregati per le VM.
    - **Totale velocità effettiva del disco della macchina virtuale** Visualizza una tendenza grafica del totale della velocità effettiva del disco per le macchine virtuali e un elenco di macchine virtuali con il totale della velocità effettiva del disco per ognuna, in base al periodo di tempo selezionato. Passare il puntatore sul grafico a linee per visualizzare i dettagli di uno specifico punto nel tempo per le prime 3 VM. Fare clic sul grafico per visualizzare altri dettagli nella ricerca log. Fare clic su un nome VM per aprire la ricerca log e visualizzare i dettagli dei contatori del totale della velocità effettiva del disco aggregati per le VM.
- **Volumi condivisi cluster**
    - **Totale velocità effettiva** Visualizza la somma delle operazioni di lettura e di scrittura nei volumi condivisi cluster.
    - **Totale operazioni di I/O al secondo** Visualizza la somma di operazioni di input/output al secondo nei volumi condivisi cluster.
    - **Totale latenza** Visualizza il totale della latenza nei volumi condivisi cluster.
- **Densità host** Il riquadro superiore visualizza il numero totale di host e di macchine virtuali disponibili per la soluzione. Fare clic sul riquadro superiore per visualizzare altri dettagli nella ricerca log. Elenca anche tutti gli host e il numero di macchine virtuali ospitate. Fare clic su un host per esaminare i risultati delle VM in una ricerca log.


![Pannello Host del dashboard](./media/capacity-performance/dashboard-hosts.png)

![Pannello Macchine virtuali del dashboard](./media/capacity-performance/dashboard-vms.png)


### <a name="evaluate-performance"></a>Valutare le prestazioni

Gli ambienti di elaborazione di produzione sono molto diversi da un'organizzazione all'altra. Anche i carichi di lavoro di capacità e prestazioni possono dipendere da come vengono eseguite le VM e da che cosa si considera normale. È probabile che specifiche procedure per misurare le prestazioni non si applichino a tutti gli ambienti. Per questo è meglio presentare indicazioni prescrittive più generiche. Microsoft pubblica svariati articoli con indicazioni prescrittive che consentono di misurare le prestazioni.

In sintesi, la soluzione raccogli i dati su capacità e prestazioni da svariate origini, inclusi i contatori delle prestazioni. Usare tali dati su capacità e prestazioni che vengono presentati in diverse superfici della soluzione e confrontare i risultati con quelli dell'articolo [Measuring Performance on Hyper-V](https://msdn.microsoft.com/library/cc768535.aspx) (Misurazione delle prestazioni in Hyper-V). Anche se la pubblicazione dell'articolo non è recente, le metriche, le considerazioni e le linee guida sono ancora valide. L'articolo contiene collegamenti ad altre risorse utili.


## <a name="sample-log-searches"></a>Ricerche di log di esempio

La tabella seguente presenta ricerche log di esempio per i dati su capacità e prestazioni raccolti e calcolati da questa soluzione.


| Query | DESCRIZIONE |
|:--- |:--- |
| Tutte le configurazioni di memoria degli host | Perf &#124; where ObjectName == "Capacity and Performance" and CounterName == "Host Assigned Memory MB" &#124; summarize MB = avg(CounterValue) by InstanceName |
| Tutte le configurazioni di memoria delle macchine virtuali | Perf &#124; where ObjectName == "Capacity and Performance" and CounterName == "VM Assigned Memory MB" &#124; summarize MB = avg(CounterValue) by InstanceName |
| Scomposizione totale IOPS dei dischi tra tutte le macchine virtuali | Perf &#124; where ObjectName == "Capacity and Performance" and (CounterName == "VHD Reads/s" or CounterName == "VHD Writes/s") &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), CounterName, InstanceName |
| Scomposizione totale velocità effettiva dei dischi tra tutte le macchine virtuali | Perf &#124; where ObjectName == "Capacity and Performance" and (CounterName == "VHD Read MB/s" or CounterName == "VHD Write MB/s") &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), CounterName, InstanceName |
| Scomposizione totale IOPS tra tutti i CSV | Perf &#124; where ObjectName == "Capacity and Performance" and (CounterName == "CSV Reads/s" or CounterName == "CSV Writes/s") &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), CounterName, InstanceName |
| Scomposizione totale velocità effettiva tra tutti i CSV | Perf &#124; where ObjectName == "Capacity and Performance" and (CounterName == "CSV Reads/s" or CounterName == "CSV Writes/s") &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), CounterName, InstanceName |
| Scomposizione totale latenza tra tutti i CSV | Perf &#124; where ObjectName == "Capacity and Performance" and (CounterName == "CSV Read Latency" or CounterName == "CSV Write Latency") &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), CounterName, InstanceName |


## <a name="next-steps"></a>Passaggi successivi
* Per visualizzare informazioni dettagliate su capacità e prestazioni, usare [Ricerche log in Log Analytics](../../azure-monitor/log-query/log-query-overview.md).
