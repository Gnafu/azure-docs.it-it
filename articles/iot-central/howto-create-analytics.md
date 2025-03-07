---
title: Analizzare i dati dei dispositivi nell'applicazione Azure IoT Central | Microsoft Docs
description: Analizzare i dati dei dispositivi nell'applicazione Azure IoT Central.
author: lmasieri
ms.author: lmasieri
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: ffe8b350c1b5cea23aeb65092c7912c6d6c1ed89
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052964"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Come usare le analisi per analizzare i dati dei dispositivi

*Questo articolo è rivolto a operatori, autori e amministratori.*

Azure IoT Central offre funzionalità avanzate di analisi che consentono di comprendere le grandi quantità di dati provenienti dai dispositivi. Per iniziare, visitare **Analytics** nel menu di navigazione a sinistra.

## <a name="querying-your-data"></a>Query dei dati

È necessario scegliere un **set di dispositivi**, aggiungere un **filtro** (facoltativo) e selezionare un **periodo di tempo** per iniziare. Al termine, selezionare **Mostra i risultati** per iniziare a visualizzare i dati.

* **Device Set (Set di dispositivi):** Un [set di dispositivi](howto-use-device-sets.md) è un gruppo di dispositivi definito dall'utente. Ad esempio, tutti frigoriferi in Oakland o tutte le turbine eoliche rev 2.0.

* **Filtri:** è facoltativo aggiungere dei filtri per la ricerca per individuare i dati. È possibile aggiungere fino a 10 filtri alla volta. Ad esempio, all'interno di tutti i frigoriferi in Oakland, trovare quelli che hanno avuto temperatura al di sopra dei 60 gradi.
* **Periodo di tempo:** per impostazione predefinita verranno recuperati i dati dagli ultimi 10 minuti. È possibile modificare questo valore su uno degli intervalli di tempo predefiniti o selezionare un periodo di tempo personalizzato.

  ![Query Analytics](media/howto-create-analytics/analytics-query.png)

## <a name="visualizing-your-data"></a>Visualizzazione dei dati

Una volta che è stata eseguita una query dei dati, sarà possibile avviare la visualizzazione. È possibile mostrare/nascondere le misure, modificare il modo in cui i dati sono aggregati e suddividere ulteriormente i dati per le proprietà diverse del dispositivo.  

* **Diviso per:** divisione dei dati in base alle proprietà del dispositivo per consentire di indagare ulteriormente i dati. Ad esempio, è possibile suddividere i risultati in base all'ID dispositivo o alla posizione.

* **Misure:** è possibile scegliere di mostrare/nascondere fino a 10 elementi di telemetria diversi allo stesso tempo, segnalati dai dispositivi. Le misure sono ad esempio, temperatura e umidità.

* **Aggregazione:** per impostazione predefinita i dati sono aggregati in base alla media, ma è possibile scegliere di modificare l'aggregazione sulla base delle esigenze.

   ![Visualizzazione di Analytics suddivisa in](media/howto-create-analytics/analytics-splitby.png)

## <a name="interacting-with-your-data"></a>Interazione con i dati

Sono disponibili varie opzioni per modificare i risultati delle query per soddisfare le esigenze di visualizzazione. È possibile alternare tra una visualizzazione grafico e una visualizzazione griglia, fare zoom avanti e indietro, aggiorna il set di dati e modificare la modalità in cui vengono visualizzate le righe.

* **Mostra griglia:** I risultati sono disponibili in un formato di tabella, che consente di visualizzare il valore specifico per ogni punto dati. Questa visualizzazione soddisfa anche gli standard di accessibilità.
* **Mostra il grafico:** I risultati vengono visualizzati in un formato di riga per facilitare l'identificazione verso l'alto o verso il basso tendenze e anomalie.

  ![Passare alla visualizzazione griglia per l'analitica](media/howto-create-analytics/analytics-showgrid.png)

Zoom consente di accedere alla home page sui dati. Se si trova un periodo di tempo nel quale si desidera concentrarsi all'interno del set di risultati, usare il cursore per acquisire l'area che si intende ingrandire e usare i controlli disponibili per eseguire una delle seguenti azioni:

* **Eseguire lo zoom avanti:** Dopo aver selezionato un periodo di tempo, zoom avanti sono abilitato e consente di eseguire lo zoom per i dati.
* **Eseguire lo zoom indietro:** questo controllo consente di eseguire lo zoom di un livello indietro all'ultimo zoom. Ad esempio, se hai zoom in per i dati tre volte, zoom indietro accetta è eseguire il unico passaggio alla volta.
* **Reimpostare zoom:** dopo aver eseguito diversi livelli di zoom, è possibile usare il controllo di reimpostazione dello zoom per ritornare al set di risultati originale.

  ![Eseguire lo zoom avanti sui dati](media/howto-create-analytics/analytics-zoom.png)

È possibile modificare lo stile della riga per soddisfare diverse esigenze. Sono disponibili quattro opzioni:

* **Linea:** Una linea retta tra ciascuno dei punti dati.
* **Smooth:** Una linea curva tra ogni punto.
* **Passaggio:** Riga tra ogni punto nel grafico è un passaggio.
* **A dispersione:** Tutti i punti vengono tracciati sul grafico senza le righe che li connettono.

  ![Tipi di linea diversi disponibili in Analytics](media/howto-create-analytics/analytics-linetypes.png)

Infine, è possibile disporre i dati tra l'asse y, scegliere una delle tre modalità:

* **In pila:** i grafici per ogni misura sono impilati e ciascuno dei grafici ha il proprio asse y. I grafici in pila sono utili quando si hanno più misure selezionate e si vuole avere una singola visualizzazione delle misure.
* **Istogrammi:** viene tracciato un grafico per ogni misura rispetto a un solo asse y, ma i valori per l'asse y cambiano in base alla misura evidenziata. Gli istogrammi sono utili quando si vuole sovrapporre più misure e si vuole vedere gli schemi fra le misure per lo stesso intervallo di tempo.
* **Asse y condiviso:** tutti i grafici condividono lo stesso asse y e i valori per l'asse non cambiano. I grafici con l'asse y condiviso sono utili quando si vuole esaminare una sola misura suddividendo i dati in base a qualche criterio.

  ![Disporre i dati tra l'asse y con diverse modalità di visualizzazione](media/howto-create-analytics/analytics-yaxis.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la procedura per creare analisi personalizzate per l'applicazione Azure IoT Central, il prossimo passo suggerito è:

> [!div class="nextstepaction"]
> [Preparare e connettere un'applicazione Node.js](howto-connect-nodejs.md)