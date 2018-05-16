---
title: Demo per la formazione autonoma sulla soluzione Mapping dei servizi in Azure | Microsoft Docs
description: Service Map è una soluzione di Azure che rileva automaticamente i componenti delle applicazioni nei sistemi Windows e Linux e mappa la comunicazione tra i servizi. Questa demo per la formazione autonoma illustra l'uso di Mapping dei servizi per identificare e diagnosticare un problema simulato in un'applicazione Web.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: monitoring
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: bwren
ms.openlocfilehash: b406cb2a9e96e3ba3514ed08c20483df57de9c71
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2018
---
# <a name="self-paced-demo---service-map"></a>Demo per la formazione autonoma - Mapping dei servizi
Questa demo per la formazione autonoma illustra l'uso della [soluzione Mapping dei servizi](monitoring-service-map.md) in Azure per identificare e diagnosticare un problema simulato in un'applicazione Web. Mapping dei servizi individua automaticamente i componenti delle applicazioni nei sistemi Windows e Linux ed esegue la mappatura della comunicazione fra i servizi. Consente di consolidare i dati raccolti da altri servizi e da altre soluzioni per agevolare l'analisi delle prestazioni e l'identificazione dei problemi. Vengono anche usate le [ricerche nei log in Log Analytics](../log-analytics/log-analytics-log-searches.md) per eseguire il drill-down sui dati raccolti e identificare il problema radice.


## <a name="scenario-description"></a>Descrizione dello scenario
È stata appena ricevuta una notifica relativa a problemi di prestazioni dell'applicazione del portale clienti ACME. Le uniche informazioni disponibili sono che i problemi sono iniziati all'incirca alle 4.00 PST di oggi. A parte un set di server Web, non si è completamente sicuri di tutti i componenti da cui dipende il portale. 

## <a name="components-and-features-used"></a>Componenti e le funzionalità usati
- [Soluzione Service Map](monitoring-service-map.md)
- [Ricerche nei log in Log Analytics](../log-analytics/log-analytics-log-searches.md)


## <a name="walkthrough"></a>Procedura dettagliata

### <a name="1-connect-to-the-oms-experience-center"></a>1. Connettersi a OMS Experience Center
Questa procedura dettagliata usa [Operations Management Suite Experience Center](https://experience.mms.microsoft.com/), che offre un ambiente di Log Analytics completo con dati di esempio. Per iniziare, seguire questo collegamento, specificare le informazioni e quindi selezionare lo scenario **Insight & Analytics**.


### <a name="2-start-service-map"></a>2. Avviare Service Map
Per avviare la soluzione, fare clic sul riquadro **Service Map**.

![Riquadro Service Map](media/monitoring-walkthrough-servicemap/tile.png)

Verrà visualizzata la console di Service Map. Il riquadro a sinistra contiene un elenco di computer nell'ambiente in cui è installato l'agente per Service Map. Il computer da visualizzare viene selezionato da questo elenco.

![Elenco di computer](media/monitoring-walkthrough-servicemap/computer-list.png)


### <a name="3-view-computer"></a>3. Visualizzare un computer
I server Web sono denominati AcmeWFE001 e AcmeWFE002, questo sembra un buon punto di partenza. Fare clic su **AcmeWFE001**. Verrà visualizzata la mappa per AcmeWFE001 e tutte le relative dipendenze. La mappa mostra i processi in esecuzione nel computer selezionato e i servizi esterni con cui comunicano.

![Server Web](media/monitoring-walkthrough-servicemap/web-server.png)

Per visualizzare le prestazioni dell'applicazione Web, fare clic sul processo **AcmeAppPool (IIS App Pool)**. Verranno visualizzati i dettagli di questo processo ed evidenziate le relative dipendenze. 

![Pool di app](media/monitoring-walkthrough-servicemap/app-pool.png)


### <a name="4-change-time-window"></a>4. Cambiare l'intervallo di tempo

Il problema è iniziato alle 4.00, occorre quindi verificare quale fosse la situazione in quel momento. Fare clic su **Intervallo di tempo** e impostare l'ora sulle 4.00 PST con una durata di 20 minuti, mantenendo la data corrente e facendo le dovute modifiche in base al fuso orario locale.

![Selezione ora](./media/monitoring-walkthrough-servicemap/time-picker.png)


### <a name="5-view-alert"></a>5. Visualizzare un avviso

A questo punto si può vedere che la dipendenza **acmetomcat** mostra un avviso. Potrebbe trattarsi del problema che si sta cercando. Fare clic sull'icona dell'avviso in **acmetomcat** per visualizzare i relativi dettagli. L'uso della CPU è indicato come critico ed è possibile espandere l'avviso per visualizzare altri dettagli. Si tratta probabilmente della causa del rallentamento delle prestazioni. 

![Avviso](./media/monitoring-walkthrough-servicemap/alert.png)


### <a name="6-view-performance"></a>6. Visualizzare le prestazioni

Esaminare **acmetomcat** più da vicino. Fare clic nell'angolo superiore destro di **acmetomcat** e selezionare **Carica mappa del server** per visualizzare i dettagli e le dipendenze per questo computer. È possibile esaminare più a fondo i contatori delle prestazioni per verificare la situazione. Selezionare la scheda **Prestazioni** per visualizzare i [contatori delle prestazioni raccolti da Log Analytics](../log-analytics/log-analytics-data-sources-performance-counters.md) nell'intervallo di tempo. Si possono notare picchi periodici nelle prestazioni del processore e della memoria.

![Prestazioni](./media/monitoring-walkthrough-servicemap/performance.png)


### <a name="7-view-change-tracking"></a>7. Visualizzare il rilevamento modifiche
Provare ora a scoprire la possibile causa di questo utilizzo elevato. Fare clic sulla scheda **Riepilogo**, che contiene le informazioni che Log Analytics ha raccolto dal computer, come le connessioni non riuscite, gli avvisi critici e le modifiche al software. Le sezioni con informazioni recenti rilevanti dovrebbero già essere espanse. È possibile espandere altre sezioni per esaminare le informazioni in esse contenute.


Espandere **Rilevamento modifiche**, se non è già aperto. Qui vengono mostrate le informazioni raccolte dalla [soluzione Rilevamento modifiche](../log-analytics/log-analytics-change-tracking.md). Sembra che in questo intervallo di tempo sia stata apportata una modifica al software. Fare clic su **Software** per visualizzare i dettagli. Un processo di backup è stato aggiunto al computer poco dopo le 4.00. Sembra quindi che sia questa la causa del consumo eccessivo di risorse.

![Rilevamento modifiche](./media/monitoring-walkthrough-servicemap/change-tracking.png)



### <a name="8-view-details-in-log-search"></a>8. Visualizzare i dettagli nella ricerca dei log
Per fare un'altra verifica, è possibile esaminare le informazioni dettagliate sulle prestazioni raccolte nell'area di lavoro di Log Analytics. Fare nuovamente clic sulla scheda **Avvisi** e quindi su uno degli avvisi di **utilizzo elevato della CPU**. Fare clic su **Mostra in Ricerca log**. Verrà visualizzata la finestra Ricerca log, in cui è possibile eseguire [ricerche nei log](../log-analytics/log-analytics-log-searches.md) su tutti i dati archiviati nell'area di lavoro. Service Map ha già compilato una query per recuperare l'avviso che si sta cercando. 

![Ricerca log](./media/monitoring-walkthrough-servicemap/log-search.png)


### <a name="9-open-saved-search"></a>9. Aprire una ricerca salvata
Provare ora a ottenere maggiori dettagli sulla raccolta di prestazioni che ha generato l'avviso, per verificare il sospetto che i problemi siano causati da tale processo di backup. Modificare l'intervallo di tempo in **6 ore**. Quindi fare clic su **Preferiti** e scorrere fino alle ricerche salvate per **Service Map**. Le query sono state create in modo specifico per l'analisi. Fare clic su **Top 5 Processes by CPU for acmetomcat** (Primi 5 processi per CPU per acmetomcat).

![Ricerca salvata](./media/monitoring-walkthrough-servicemap/saved-search.png)


Questa query restituisce un elenco dei primi cinque processi per utilizzo del processore in **acmetomcat**. È possibile esaminare la query per iniziare a conoscere il linguaggio di query usato per le ricerche nei log. Se si è interessati ai processi in altri computer, è possibile modificare la query per recuperare tali informazioni.

In questo caso si può notare che il processo di backup usa in modo costante circa il 60% della CPU del server dell'app. È evidente che questo nuovo processo è la causa del problema di prestazioni. Per risolvere il problema, naturalmente, il nuovo software di backup dovrà essere rimosso dal server dell'applicazione. È possibile usare Configurazione dello stato desiderato, gestito da Automazione di Azure, per definire criteri che garantiscano che tale processo non venga mai eseguito in questi sistemi critici.


## <a name="summary-points"></a>Riepilogo
- [Service Map](monitoring-service-map.md) permette di visualizzare l'intera applicazione, anche se non se ne conoscono tutti i server e le dipendenze.
- Service Map presenta i dati raccolti da altre soluzioni di gestione per identificare i problemi dell'applicazione e dell'infrastruttura sottostante.
- [Ricerche log](../log-analytics/log-analytics-log-searches.md) consente di eseguire il drill down su dati specifici raccolti nell'area di lavoro di Log Analytics.  

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni su [Service Map](monitoring-service-map.md).
- [Distribuire e configurare](monitoring-service-map-configure.md) Service Map.
- Informazioni su [Log Analytics](../log-analytics/log-analytics-overview.md), necessario per Service Map, che archivia i dati operativi memorizzati dagli agenti.