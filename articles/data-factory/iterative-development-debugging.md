---
title: Sviluppo e debug iterativi in Azure Data Factory | Microsoft Docs
description: Informazioni su come sviluppare ed eseguire in modo iterativo il debug delle pipeline di Data Factory nel portale di Azure.
ms.date: 09/26/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
author: djpmsft
ms.author: daperlov
manager: craigg
ms.openlocfilehash: a1c4bf55fbb8242293fc0e30bbe16a6884b901cb
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178612"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Sviluppo e debug iterativi con Azure Data Factory

Azure Data Factory consente di eseguire in modo iterativo lo sviluppo e il debug di pipeline di Data Factory.

Per un'introduzione di otto minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>Funzionalità di debug iterativo
Creare pipeline e avviare esecuzioni dei test usando la funzionalità **Debug** nel canvas della pipeline senza dover scrivere una sola riga di codice.

![Funzionalità di debug nel canvas della pipeline](media/iterative-development-debugging/iterative-development-image1.png)

Visualizzare i risultati dell'esecuzione dei test nella finestra **Output** del canvas della pipeline.

![Finestra Output del canvas della pipeline](media/iterative-development-debugging/iterative-development-image2.png)

Quando un'esecuzione dei test ha esito positivo, aggiungere altre attività alla pipeline e continuare il debug in modo iterativo. È anche possibile **annullare** un'esecuzione dei test in corso.

![Annullare un'esecuzione dei test](media/iterative-development-debugging/iterative-development-image3.png)

Durante un'esecuzione dei test, non è necessario pubblicare le modifiche in Data Factory prima di selezionare **Debug**. Questa funzione risulta utile negli scenari in cui ci si vuole assicurare che le modifiche funzionino come previsto prima di aggiornare il flusso di lavoro di Data Factory.

> [!IMPORTANT]
> Selezionando **Debug** la pipeline viene eseguita. Quindi, se per esempio la pipeline contiene attività di copia, l'esecuzione dei test copia i dati dall'origine alla destinazione. Di conseguenza, durante il debug è consigliabile usare cartelle di test nelle attività di copia e in altre attività. Dopo aver eseguito il debug della pipeline, passare alle cartelle che si vogliono usare durante il funzionamento normale.

## <a name="visualizing-debug-runs"></a>Visualizzazione delle esecuzioni di debug

È possibile visualizzare tutte le esecuzioni di debug in corso della data factory in un'unica posizione. Selezionare **View debug runs** (Visualizza esecuzioni di debug) nell'angolo superiore destro della pagina. Questa funzionalità è utile negli scenari con pipeline master che avviano esecuzioni di debug per le pipeline figlio e in cui si vuole una visualizzazione singola per visualizzare tutte le esecuzioni di debug attive.

![Selezionare l'icona View active debug runs (Visualizza esecuzioni di debug attive)](media/iterative-development-debugging/view-debug-runs-image1.png)

![Elenco di esempi di esecuzioni di debug attive](media/iterative-development-debugging/view-debug-runs-image2.png)

Se si dispone di sessioni di debug del flusso di dati attive, tali sessioni verranno visualizzate nella parte inferiore della finestra di debug attiva. È possibile selezionare una sessione del flusso di dati attiva e arrestare il rispettivo cluster.

![Elenco di esempio delle esecuzioni di debug del flusso di dati attivo](media/data-flow/dfsessions.png)

## <a name="monitoring-debug-runs"></a>Esegue il monitoraggio delle esecuzioni di debug

Le esecuzioni dei test avviate con la funzionalità **Debug** non sono disponibili nell'elenco della scheda **Monitoraggio**. Nella scheda **Monitoraggio** sono visibili solo le esecuzioni attivate con i trigger **Trigger Now** (Attiva adesso), **Pianificazione** o **Finestra a cascata**. È possibile vedere ultima esecuzione dei test avviata con la funzionalità **Debug** nella finestra **Output** del canvas della pipeline.

## <a name="setting-breakpoints-for-debugging"></a>Impostazione di punti di interruzione per il debug

Data Factory consente anche di eseguire il debug finché non si raggiunge una specifica attività nel canvas della pipeline. È sufficiente inserire un punto di interruzione sull'attività fino alla quale si vuole testare e selezionare **Debug**. Data Factory esegue i test solo fino all'attività con il punto di interruzione nel canvas della pipeline. Questa funzionalità *Debug Until* (Debug fino a) è utile quando non si vuole testare l'intera pipeline, ma solo un subset delle attività all'interno della pipeline.

![Punti di interruzione nel canvas della pipeline](media/iterative-development-debugging/iterative-development-image4.png)

Per impostare un punto di interruzione, selezionare un elemento nel canvas della pipeline. L'opzione *Debug Until* (Debug fino a) viene visualizzata sotto forma di cerchio rosso vuoto nell'angolo in alto a destra dell'elemento.

![Prima dell'impostazione di un punto di interruzione sull'elemento selezionato](media/iterative-development-debugging/iterative-development-image5.png)

Dopo avere selezionato l'opzione *Debug fino a*, diventa un cerchio rosso pieno per indicare che il punto di interruzione è abilitato.

![Dopo l'impostazione di un punto di interruzione sull'elemento selezionato](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>Passaggi successivi
[Integrazione e distribuzione continue in Azure Data Factory](continuous-integration-deployment.md)
