---
title: Navigazione e dashboard in Azure Application Insights | Microsoft Docs
description: Creare visualizzazioni di query e grafici APM chiave.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 39b0701b-2fec-4683-842a-8a19424f67bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: afbf2bc32aa737eb5f6dde41035b206d6e260252
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55813764"
---
# <a name="navigation-and-dashboards-in-the-application-insights-portal"></a>Navigazione e dashboard nel portale Application Insights
Dopo aver [impostato Application Insights nel progetto](../../azure-monitor/app/app-insights-overview.md), i dati di telemetria sull'uso e le prestazioni dell'app verranno visualizzati nella risorsa di Application Insights del progetto nel [portale di Azure](https://portal.azure.com).

## <a name="find-your-telemetry"></a>Trovare i dati di telemetria
Nel [portale di Azure](https://portal.azure.com) passare alla risorsa di Application Insights creata per la propria app.

![Fare clic su Sfoglia, selezionare Application Insights, quindi fare clic sull'app.](./media/app-insights-dashboards/00-start.png)

Il pannello (pagina) Panoramica dell'app mostra un riepilogo della metrica di diagnostica chiave dell'app e permette di accedere alle altre funzionalità del portale.

![Route principali per visualizzare i dati di telemetria](./media/app-insights-dashboards/010-oview.png)

È possibile personalizzare tutti i grafici e le griglie e aggiungerli a un dashboard. In questo modo è possibile raccogliere i dati di telemetria chiave da diverse app in un dashboard centrale.

## <a name="dashboards"></a>Dashboard
Il primo oggetto visualizzato dopo l'accesso al [portale di Microsoft Azure](https://portal.azure.com) è un dashboard, in cui è possibile raggruppare i grafici più importanti di tutte le risorse di Azure, inclusi i dati di telemetria di [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md).

![Un dashboard personalizzato.](./media/app-insights-dashboards/31.png)

1. **Passare a risorse specifiche** ad esempio l'app in Application Insights: usare la barra a sinistra.
2. **Tornare al dashboard corrente** o passare ad altre visualizzazioni recenti: usare il menu a discesa in alto a sinistra.
3. **Passare ad altri dashboard**: usare il menu a discesa nel titolo del dashboard.
4. **Modificare, creare e condividere dashboard** nella barra degli strumenti.
5. **Modificare il dashboard**: Passare il puntatore del mouse su un riquadro e usare la relativa barra superiore per spostarlo, personalizzarlo o rimuoverlo.

## <a name="add-to-a-dashboard"></a>Aggiungere elementi a un dashboard
I pannelli o i set di grafici particolarmente interessanti possono essere aggiunti al dashboard. Saranno visibili al successivo accesso.

![Per aggiungere un grafico, passare il mouse su di esso e fare clic su "..." nell'intestazione.](./media/app-insights-dashboards/33.png)

1. Aggiungere il grafico al dashboard. Una copia del grafico viene visualizzata nel dashboard.
2. Aggiungere l'intero pannello al dashboard. Il pannello viene visualizzato nel dashboard sotto forma di riquadro su cui è possibile fare clic.
3. Fare clic nell'angolo superiore sinistro per tornare al dashboard corrente. A questo punto è possibile usare il menu a discesa per tornare alla visualizzazione corrente.

Si noti che i grafici sono raggruppati in riquadri e che un riquadro può contenere più di un grafico. Viene aggiunto al dashboard l'intero riquadro.

Il grafico viene aggiornato automaticamente con una frequenza che dipende dall'intervallo di tempo del grafico:

* Intervallo di tempo fino a 1 ora: aggiornare ogni 5 minuti.
* Intervallo di tempo da 1 a 24 ore: aggiornare ogni 15 minuti.
* Intervallo di tempo superiore a 24 ore: (intervallo di tempo)/60.

### <a name="pin-any-query-in-analytics"></a>Aggiungere query in Analisi
È anche possibile [aggiungere grafici di Analisi](../../azure-monitor/log-query/get-started-portal.md) a un dashboard condiviso. In questo modo è possibile aggiungere grafici di una query arbitraria insieme alle metriche standard. 

I risultati vengono ricalcolati automaticamente ogni ora. Fare clic sull'icona Aggiorna del grafico per un ricalcolo immediato. (L'aggiornamento del browser non esegue il ricalcolo).

## <a name="adjust-a-tile-on-the-dashboard"></a>Modificare un riquadro nel dashboard
Dopo aver aggiunto un riquadro al dashboard, è possibile modificarlo.

![Passare il puntatore del mouse su un grafico per modificarlo.](./media/app-insights-dashboards/36.png)

1. Aggiungere un grafico al riquadro.
2. Impostare la metrica, la dimensione group-by e lo stile (tabella, grafico) di un grafico.
3. Trascinare il diagramma per ingrandire; fare clic sul pulsante di annullamento per reimpostare l'intervallo di tempo; impostare le proprietà di filtro per i grafici nel riquadro.
4. Impostare il titolo del riquadro.

I riquadri aggiunti dai pannelli di Esplora metriche hanno più opzioni di modifica rispetto ai riquadri aggiunti da un pannello Panoramica.

Il riquadro originale aggiunto non è interessato dalle modifiche.

## <a name="switch-between-dashboards"></a>Passare da un dashboard all'altro
È possibile salvare più dashboard e passare da un dashboard all'altro. Quando si aggiunge un grafico o un pannello, l'aggiunta viene eseguita nel dashboard corrente.

![Per passare da un dashboard all'altro, fare clic su Dashboard e selezionare un dashboard salvato. Per creare e salvare un nuovo dashboard, fare clic su Nuovo. Per modificare la disposizione, fare clic su Modifica.](./media/app-insights-dashboards/32.png)

È possibile ad esempio usare un dashboard per la visualizzazione a schermo intero nell'area del team e un altro dashboard per lo sviluppo generale.

Nel dashboard i pannelli vengono visualizzati sotto forma di riquadri: fare clic sul riquadro desiderato per passare al pannello. I grafici sono una replica dei grafici che si trovano nel percorso originale.

![Fare clic su un riquadro per aprire il pannello che rappresenta](./media/app-insights-dashboards/35.png)

## <a name="share-dashboards"></a>Condividere dashboard
Dopo aver creato un dashboard, è possibile condividerlo con altri utenti.

![Fare clic su Condividi nell'intestazione del dashboard](./media/app-insights-dashboards/41.png)

Altre informazioni su [ruoli e controllo di accesso](../../azure-monitor/app/resources-roles-access-control.md).

## <a name="create-dashboards-programmatically"></a>Creare dashboard a livello di codice
È possibile automatizzare la creazione di dashboard usando [Azure Resource Manager](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards-create-programmatically) e un semplice editor di JSON.

## <a name="app-navigation"></a>Esplorazione delle app
Il pannello Panoramica è il gateway per altre informazioni sull'app.

* **Qualsiasi grafico o riquadro**: fare clic su qualsiasi riquadro o grafico per vedere altri dettagli sugli elementi visualizzati.

### <a name="overview-blade-buttons"></a>Pulsanti del pannello Panoramica
![Barra di spostamento superiore del pannello Panoramica](./media/app-insights-dashboards/app-overview-top-nav.png)

* [**Esplora metriche**](../../azure-monitor/app/metrics-explorer.md): consente di creare grafici su prestazioni e uso.
* [**Cerca**](../../azure-monitor/app/diagnostic-search.md): consente di analizzare istanze specifiche di eventi, ad esempio richieste, eccezioni o tracce di log.
* [**Analisi**](../../azure-monitor/app/analytics.md): consente di eseguire query avanzate con i dati di telemetria.
* **Intervallo di tempo**: consente di regolare l'intervallo visualizzato da tutti i grafici nel pannello.
* **Elimina**: consente di eliminare la risorsa di Application Insights per l'app. È necessario anche rimuovere i pacchetti di Application Insights dal codice dell'app o modificare la [chiave di strumentazione](../../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key) nell'app per indirizzare i dati di telemetria a una risorsa di Application Insights diversa.

### <a name="essentials-tab"></a>Scheda Informazioni di base
* [Chiave di strumentazione](../../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key): identifica la risorsa dell'app.

### <a name="app-navigation-bar"></a>Barra di spostamento delle app
![Barra di spostamento sinistra](./media/app-insights-dashboards/app-left-nav-bar.png)

* **Panoramica**: consente di tornare al pannello Panoramica delle app.
* **Log attività**: fornisce avvisi e informazioni su eventi amministrativi di Azure.
* [**Controllo di accesso**](../../azure-monitor/app/resources-roles-access-control.md): fornire l'accesso a membri del team e altri utenti.
* [**Tag**](../../azure-resource-manager/resource-group-using-tags.md): consente di usare tag per raggruppare un'app con altre app.

RICERCA CAUSA

* [**Mappa delle applicazioni**](app-map.md): una mappa attiva che mostra i componenti dell'applicazione, ricavata dalle informazioni sulle dipendenze.
* [**Rilevamento intelligente**](../../azure-monitor/app/proactive-diagnostics.md): consente di esaminare gli avvisi recenti sulle prestazioni.
* [**Flusso attivo**](../../azure-monitor/app/live-stream.md): un set fisso di metriche quasi istantanee, utile quando si distribuisce una nuova compilazione o si esegue il debug.
* [**Test Web/disponibilità**](../../azure-monitor/app/monitor-web-app-availability.md): consente di inviare regolarmente richieste all'App Web da ogni parte del mondo.*
* [**Errori, Prestazioni**](../../azure-monitor/app/web-monitor-performance.md): eccezioni, frequenze di errori e tempi di risposta per le richieste verso l'app e dall'app alle [dipendenze](../../azure-monitor/app/asp-net-dependencies.md).
* [**Performance**](../../azure-monitor/app/web-monitor-performance.md): tempo di risposta e tempi di risposta delle dipendenze.
* [Server](../../azure-monitor/app/web-monitor-performance.md) : contatori delle prestazioni. Disponibile se si [installa Status Monitor](../../azure-monitor/app/monitor-performance-live-website-now.md).
* **Browser** : prestazioni AJAX e di visualizzazione di pagine. Disponibile se si [instrumentano le pagine Web](../../azure-monitor/app/javascript.md).
* **Utilizzo** : numero di sessioni, utenti e visualizzazioni di pagine. Disponibile se si [instrumentano le pagine Web](../../azure-monitor/app/javascript.md).

CONFIGURA

* **Per iniziare** : esercitazione inline.
* **Proprietà** : chiave di strumentazione, sottoscrizione e ID risorsa.
* [Avvisi](../../azure-monitor/app/alerts.md) : configurazione degli avvisi sulle metriche.
* [Esportazione continua](../../azure-monitor/app/export-telemetry.md) : configurazione dell'esportazione dei dati di telemetria nell'archiviazione di Azure.
* [Test delle prestazioni](../../azure-monitor/app/monitor-web-app-availability.md#performance-tests) : impostazione di un carico sintetico nel sito Web.
* [Quota e prezzi](../../azure-monitor/app/pricing.md) e [campionamento per inserimento](../../azure-monitor/app/sampling.md).
* **Accesso API**: consente di creare [annotazioni sulla versione](annotations.md) e per l'API di accesso ai dati.
* [**Elementi di lavoro**](../../azure-monitor/app/diagnostic-search.md#create-work-item): consente di connettersi a un sistema di verifica del lavoro per poter creare bug durante l'analisi dei dati di telemetria.

IMPOSTAZIONI

* [**Blocchi**](../../azure-resource-manager/resource-group-lock-resources.md): bloccano le risorse di Azure.
* [**Script di automazione**](../../azure-monitor/app/powershell.md): consente di esportare una definizione della risorsa di Azure da usare come modello per la creazione di nuove risorse.


## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Passaggi successivi

|  |  |
| --- | --- |
| [Esplora metriche](../../azure-monitor/app/metrics-explorer.md)<br/>Consente di filtrare e segmentare le metriche |![Esempio di ricerca](./media/app-insights-dashboards/64.png) |
| [Ricerca diagnostica](../../azure-monitor/app/diagnostic-search.md)<br/>Consente di cercare e analizzare eventi ed eventi correlati e di creare bug |![Esempio di ricerca](./media/app-insights-dashboards/61.png) |
| [Analisi](../../azure-monitor/app/analytics.md)<br/>Linguaggio di query avanzato |![Esempio di ricerca](./media/app-insights-dashboards/63.png) |
