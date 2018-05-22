---
title: Diagnostica delle transazioni in Azure Application Insights | Microsoft Docs
description: Diagnostica della transazioni end-to-end in Application Insights
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/19/2018
ms.author: mbullwin;sdash
ms.openlocfilehash: 7a4e4f74c02358fc117e0a66977ee3f0aef5b1dd
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2018
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Diagnostica unificata delle transazioni tra componenti

*Questa esperienza è attualmente in versione di anteprima e sostituisce i pannelli di diagnostica esistenti relativi a richieste, dipendenze ed eccezioni lato server.*

L'anteprima introduce una nuova esperienza di diagnostica unificata che correla automaticamente i dati di telemetria lato server di tutti i componenti monitorati di Application Insights in un'unica vista. Non è importante se si hanno più risorse con chiavi di strumentazione separate. Application Insights rileva la relazione sottostante e consente di diagnosticare facilmente il componente, la dipendenza o l'eccezione dell'applicazione che ha causato un errore o un rallentamento nella transazione.

## <a name="what-is-a-component"></a>Cos'è un componente?

I componenti sono parti dell'applicazione distribuita o di microservizi, distribuibili autonomamente. Gli sviluppatori e i team delle operazioni hanno visibilità o accesso a livello di codice ai dati di telemetria generati dai componenti di queste applicazioni.

* I componenti sono diversi dalle dipendenze esterne "osservate" quali SQL, EventHub e così via, a cui il team o l'organizzazione potrebbero non avere accesso (codice o dati di telemetria).
* I componenti vengono eseguiti su diverse istanze di ruolo, server o contenitore.
* I componenti possono essere chiavi di strumentazione di Application Insights diverse tra loro (anche in caso di sottoscrizioni diverse) oppure ruoli diversi che creano report per una sola chiave di strumentazione di Application Insights. La nuova esperienza mostra i dettagli per tutti i componenti, indipendentemente dalla loro configurazione.

> [!NOTE]
> * **Mancano i collegamenti agli elementi correlati?** Tutti i dati di telemetria relativi a una richiesta, una dipendenza e un'eccezione lato server sono inclusi nelle sezioni [superiore](#cross-component-transaction-chart) e [inferiore](#all-telemetry-related-to-the-selected-component-operation) sul lato sinistro. 
> * La sezione [superiore](#cross-component-transaction-chart) mette in correlazione la transazione tra tutti i componenti. Per ottenere risultati ottimali, verificare che tutti i componenti siano instrumentati con gli SDK più recenti e stabili di Application Insights. Se sono disponibili più risorse di Application Insights, assicurarsi di disporre dei diritti appropriati per visualizzarne i dati di telemetria.
> * La sezione [inferiore](#all-telemetry-related-to-the-selected-component-operation) sul lato sinistro mostra **tutti** i dati di telemetria, tra cui tracce ed eventi correlati alla richiesta del componente selezionato.

## <a name="enable-transaction-diagnostics-experience"></a>Abilitare l'esperienza di diagnostica delle transazioni
Abilitare "Unified details: E2E Transaction Diagnostics" (Dettagli unificati: diagnostica della transazione end-to-end) dall'[elenco delle anteprime](app-insights-previews.md)

![Abilitazione dell'anteprima](media/app-insights-e2eTxn-diagnostics/previews.png)

Questa versione di anteprima è attualmente disponibile per le eccezioni, le dipendenze e le richieste lato server. È possibile accedere alla nuova esperienza dalle esperienze di valutazione **Risultati della ricerca**, **Prestazioni** o **Errore**. L'anteprima sostituisce i pannelli dei dettagli classici corrispondenti.

![Esempi di Prestazioni](media/app-insights-e2eTxn-diagnostics/performanceSamplesClickThrough.png)

## <a name="transaction-diagnostics-experience"></a>Esperienza di diagnostica delle transazioni
Questa vista è composta da tre parti principali: un grafico delle transazioni tra componenti, un elenco temporale di tutti i dati di telemetria relativi a un'operazione specifica del componente e il riquadro dei dettagli relativi agli elementi di telemetria selezionati a sinistra.

![Parti principali](media/app-insights-e2eTxn-diagnostics/3partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Grafico delle transazioni tra componenti

Questo grafico offre una sequenza temporale con barre orizzontali per la durata delle richieste e delle dipendenze tra componenti. Anche le eccezioni raccolte vengono contrassegnate sulla sequenza temporale.

* La prima riga di questo grafico rappresenta il punto di ingresso, la richiesta in ingresso per il primo componente chiamato in questa transazione. La durata è il tempo totale impiegato per il completamento della transazione.
* Tutte le chiamate a dipendenze esterne sono rappresentate da semplici righe non comprimibili, con icone che indicano il tipo di dipendenza.
* Le chiamate ad altri componenti sono rappresentate da righe comprimibili. Ogni riga corrisponde a un'operazione specifica richiamata nel componente.
* Per impostazione predefinita, la richiesta, la dipendenza o l'eccezione selezionata inizialmente viene visualizzata nel grafico.
* Selezionare una riga qualsiasi per visualizzarne i relativi [dettagli a destra](#details-of-the-selected-telemetry). 

> [!NOTE]
Le chiamate ad altri componenti hanno due righe: una riga rappresenta la chiamata in uscita (dipendenza) dal componente chiamante e l'altra riga corrisponde alla richiesta in ingresso al componente chiamato. L'icona iniziale e lo stile diverso delle barre della durata consentono di distinguere le chiamate tra loro.

## <a name="all-telemetry-related-to-the-selected-component-operation"></a>Tutti i dati di telemetria correlati all'operazione relativa al componente selezionato

Ogni riga selezionata nel grafico delle transazioni tra componenti è correlata a un'operazione richiamata per un determinato componente. Questa operazione del componente selezionato si riflette nel titolo della sezione inferiore. Aprire questa sezione per visualizzare una sequenza temporale semplice di tutti i dati di telemetria relativi all'operazione specifica. È possibile selezionare qualsiasi elemento di telemetria in questo elenco per visualizzarne i corrispondenti [dettagli sulla destra](#details-of-the-selected-telemetry).

![Sequenza temporale di tutti i dati di telemetria](media/app-insights-e2eTxn-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>Dettagli dei dati di telemetria selezionati

Questo riquadro mostra i dettagli degli elementi selezionati da una delle due sezioni sulla sinistra. "Show all" (Mostra tutto) elenca tutti gli attributi standard raccolti. Gli attributi personalizzati vengono elencati separatamente sotto il set di attributi standard. Fare clic su "Open profiler traces" (Apri tracce profiler) o "Apri snapshot di debug" per la diagnostica a livello di codice nei riquadri dei dettagli corrispondenti.

![Dettagli dell'eccezione](media/app-insights-e2eTxn-diagnostics/exceptiondetail.png)

## <a name="profiler-and-snapshot-debugger"></a>Profiler e Snapshot Debugger

[Application Insights Profiler](app-insights-profiler.md) o [Snapshot Debugger](app-insights-snapshot-debugger.md) consentono di eseguire la diagnostica a livello di codice dei problemi di prestazioni e degli errori. Grazie a questa esperienza è possibile visualizzare le tracce del profiler o gli snapshot di un componente con un solo clic.

Per eventuali problemi relativi al funzionamento di Profiler, contattare **serviceprofilerhelp@microsoft.com**.

Per eventuali problemi relativi al funzionamento di Snapshot Debugger, contattare **snapshothelp@microsoft.com**.

![Integrazione del debugger](media/app-insights-e2eTxn-diagnostics/debugSnapshot.png)

## <a name="faq"></a>Domande frequenti

*Nel grafico viene visualizzato un solo componente, mentre gli altri vengono visualizzati solo come dipendenze esterne senza mostrare alcun dettaglio sulle operazioni eseguite all'interno di tali componenti.*

Possibili motivi:

* Gli altri componenti sono instrumentati con Application Insights?
* Usano l'SDK di Application Insights più recente e stabile?
* Se questi componenti sono risorse separate di Application Insights, si dispone dell'accesso necessario ai relativi dati di telemetria?

Se si dispone dell'accesso e i componenti sono instrumentati con gli SDK di Application Insights più recenti, segnalare il problema tramite il canale di feedback in alto a destra.

*Sono presenti solo le dipendenze esterne. È comunque consigliabile usare questa anteprima?*

Sì. La nuova esperienza riunisce tutti i dati di telemetria lato server correlati in un'unica vista. In futuro i pannelli dei dettagli precedenti verranno sostituiti da questa esperienza, è quindi consigliabile provarla e inviare i propri commenti. Ecco come appare la transazione di un solo componente:

![Esperienza di un solo componente](media/app-insights-e2eTxn-diagnostics/singleComponent.png)

*Il grafico mostra righe duplicate per le dipendenze. È normale?*

In questa fase la chiamata di dipendenza in uscita viene mostrata separatamente dalla richiesta in ingresso. In genere, le due chiamate hanno lo stesso aspetto, cambia solo il valore della durata a causa del round trip della rete. L'icona iniziale e lo stile diverso delle barre della durata consentono di distinguere le chiamate tra loro. Questa organizzazione dei dati crea confusione? Inviare i propri commenti.

*Perché ci sono degli sfasamenti di orario tra le diverse istanze del componente?*

Le sequenze temporali vengono modificate in base agli sfasamenti di orario nel grafico delle transazioni. È possibile visualizzare i timestamp esatti nel riquadro dei dettagli o tramite Analytics.

*Perché nella nuova esperienza manca la maggior parte delle query degli elementi correlati?*

Si tratta di un comportamento previsto da progettazione. Tutti gli elementi correlati in tutti i componenti sono già disponibili a sinistra (nelle sezioni in alto e in basso). La nuova esperienza presenta due elementi correlati che non sono riportati a sinistra: tutti i dati di telemetria generati nei cinque minuti precedenti e successivi a questo evento e la sequenza temporale dell'utente.

*Perché la nuova esperienza non ha una determinata funzionalità presente nei pannelli precedenti?*

L'utente è invitato a inviare un commento. Microsoft desidera risolvere i problemi prima che questa esperienza sia disponibile a livello generale. A quel punto le viste precedenti verranno deprecate. Per il momento è possibile disabilitare l'anteprima per tornare ai pannelli precedenti.

## <a name="known-issues"></a>Problemi noti

* Gli esempi di errore della mappa delle applicazioni sono collegati ai pannelli dei dettagli precedenti.
* Le informazioni legate al cluster automatico nei risultati della ricerca sono collegati ai pannelli dei dettagli precedenti.
* L'integrazione degli elementi di lavoro non è disponibile nella nuova esperienza.
