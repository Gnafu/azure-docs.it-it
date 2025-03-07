---
title: 'Knowledge base: QnA Maker'
titleSuffix: Azure Cognitive Services
description: Una knowledge base QnA Maker è costituita da un set di coppie di domanda/risposta (QnA) e metadati facoltativi associati a ogni coppia QnA.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 78b04a06b71590a79180d1ae367e7d059e1b84a3
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195233"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Che cos'è una knowledge base di QnA Maker?

Una knowledge base QnA Maker è costituita da un set di coppie di domanda/risposta (QnA) e metadati facoltativi associati a ogni coppia QnA.

## <a name="key-knowledge-base-concepts"></a>Concetti chiave della knowledge base

* **Domande** - Una domanda contiene un testo che meglio rappresenta una query dell'utente. 
* **Risposte** - Una risposta è la risposta restituita quando una query dell'utente viene confrontata con la domanda associata.  
* **Metadati** - I metadati sono tag associati a una coppia QnA e vengono rappresentati come coppie di chiave-valore. I tag di metadati vengono usati per filtrare le coppie di QnA e limitano il set su cui viene eseguita la query corrispondente.

Una singola QnA, rappresentata da un ID numerico di QnA, ha diverse varianti di una domanda ed eseguono il mapping di una sola risposta. Ogni coppia di questo tipo può inoltre avere più campi di metadati associati, ovvero una chiave e un valore.

![Knowledge base di QnA Maker](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Formato di contenuto della Knowledge Base

Quando si inseriscono contenuto avanzato in una knowledge base, QnA Maker prova a convertire il contenuto in un markdown. Leggere [questo](https://aka.ms/qnamaker-docs-markdown-support) blog per comprendere i formati markdown più comprensibili dai client chat.

I campi di metadati sono costituiti da coppie chiave-valore separate da due punti **(Prodotto: Shredder)** . Sia la chiave che il valore deve essere di solo testo. La chiave dei metadati non deve contenere spazi. I metadati supportano solo un valore per chiave.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Come QnA Maker elabora una query utente per selezionare la risposta migliore

La Knowledge base con formazione e [pubblicazione](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QnA Maker riceve una query utente, da un bot o da un'altra applicazione client, nell' [API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). Il diagramma seguente illustra il processo di ricezione della query utente.

![Processo di classificazione per una query utente](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Processo di classificazione

Il processo è illustrato nella tabella seguente:

|Passaggio|Scopo|
|--|--|
|1|L'applicazione client invia la query utente all' [API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|QnA Maker pre-elaborazione della query utente con rilevamento della lingua, ortografia e Word breaker.|
|3|Questa pre-elaborazione viene eseguita per modificare la query utente per ottenere risultati di ricerca ottimali.|
|4|Questa query modificata viene inviata all'indice di ricerca di Azure, `top` ricevendo il numero di risultati. Se la risposta corretta non è presente in questi risultati, aumentare leggermente `top` il valore di. In genere il valore 10 per `top` funziona nel 90% delle query.|
|5|QnA Maker applica conteggi avanzati per determinare la correttezza dei risultati recuperati di ricerca di Azure per la query utente. |
|6|Il modello di classificazione con training usa il punteggio della funzionalità, dal passaggio 5, per classificare i risultati di ricerca di Azure.|
|7|I nuovi risultati vengono restituiti all'applicazione client in ordine di rango.|
|||

Le funzionalità usate includono ma non sono limitate alla semantica a livello di parola, alla priorità a livello di termine in un corpus e ai modelli semantici appresi in modo approfondito per determinare la somiglianza e la pertinenza tra due stringhe di testo.

## <a name="http-request-and-response-with-endpoint"></a>Richiesta e risposta HTTP con endpoint
Quando si pubblica la Knowledge base, il servizio crea un **endpoint** http basato su REST che può essere integrato nell'applicazione, in genere un bot di chat. 

### <a name="the-user-query-request-to-generate-an-answer"></a>Richiesta di query utente per generare una risposta

Una **query utente** è la domanda che l'utente finale chiede alla Knowledge base, ad esempio, `How do I add a collaborator to my app?`. La query è spesso in un formato di linguaggio naturale o alcune parole chiave che rappresentano la domanda, ad esempio `help with collaborators`,. La query viene inviata alle proprie conoscenze da una **richiesta** http nell'applicazione client.

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

Per controllare la risposta, impostare proprietà quali [scoreThreshold](./confidence-score.md#choose-a-score-threshold), [Top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)e [strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags).

Usare il [contesto di conversazione](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) con la funzionalità a più [turni](../how-to/multiturn-conversation.md) per continuare la conversazione per ridefinire le domande e le risposte, per trovare la risposta corretta e finale.

### <a name="the-response-from-a-call-to-generate-answer"></a>Risposta da una chiamata a genera risposta

La **risposta** http è la risposta recuperata dalla Knowledge base, in base alla corrispondenza migliore per una query utente specificata. La risposta include la risposta e il Punteggio di stima. Se è stata richiesta più di una risposta top, con la `top` proprietà, si ottiene più di una risposta top, ognuno con un punteggio. 

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

### <a name="test-and-production-knowledge-base"></a>Knowledge base di test e produzione
Una Knowledge Base è il repository di domande e risposte create, gestite e usate tramite QnA Maker. Ogni livello di QnA Maker può essere usato per più Knowledge Base.

Una Knowledge Base ha due stati, test e pubblicata. 

La **Knowledge base test** è la versione che viene modificata, salvata e testata per l'accuratezza e la completezza delle risposte. Le modifiche apportate alla Knowledge Base di test non interessano l'utente finale dell'applicazione o del chat bot. La Knowledge base test è nota come `test` nella richiesta HTTP. 

La **Knowledge base pubblicata** è la versione usata nel bot/applicazione di chat. L'azione di pubblicazione di una Knowledge Base inserisce il contenuto della Knowledge Base di test nella versione pubblicata. Poiché la Knowledge Base pubblicata è la versione usata dall'applicazione tramite l'endpoint, è necessario assicurarsi che il contenuto sia corretto e ben testato. La Knowledge base pubblicata è nota come `prod` nella richiesta HTTP. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ciclo di vita di sviluppo di una knowledge](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Vedere anche

[Panoramica di QnA Maker](../Overview/overview.md)

Creare e modificare la Knowledge base con: 
* [API REST](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET SDK](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Genera risposta con: 
* [API REST](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET SDK](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
