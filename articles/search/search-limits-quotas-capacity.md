---
title: Limiti del servizio per piani e SKU |Ricerca di Azure
description: Limiti del servizio utilizzati per la pianificazione della capacità e limiti massimi per richieste e risposte in Ricerca di Azure.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: heidist
ms.openlocfilehash: 308eb90e7ae244442a603491044e90dc3b8d052a
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141140"
---
# <a name="service-limits-in-azure-search"></a>Limiti dei servizi in Ricerca di Azure
I limiti massimi per archiviazione, carichi di lavoro e quantità di indici, documenti e altri oggetti variano a seconda che venga effettuato il provisioning di [ricerca di Azure](search-create-service-portal.md) in base ai piani tariffari **gratuiti**, **Basic**, **standard**o ottimizzati per l' **archiviazione** .

+ **gratuito** è un servizio condiviso multi-tenant fornito con la sottoscrizione di Azure.

+ Il piano **Basic** fornisce risorse di elaborazione dedicate per carichi di lavoro di produzione di dimensioni ridotte.

+ Il piano **Standard** prevede computer dedicati con maggiore capacità di elaborazione e archiviazione a ogni livello. Il piano Standard è disponibile in quattro livelli: S1, S2, S3 ed S3 HD.

+ L' **archiviazione ottimizzata** viene eseguita su computer dedicati con maggiore spazio di archiviazione totale, larghezza di banda di archiviazione e memoria superiore a quella **standard**. L'archiviazione ottimizzata è in due livelli: L1 e L2

> [!NOTE]
> A partire dal 1 ° luglio, tutti i livelli sono disponibili a livello generale, incluso il livello ottimizzato per l'archiviazione. Tutti i prezzi sono disponibili nella pagina [Dettagli prezzi](https://azure.microsoft.com/pricing/details/search/) .

  S3 Alta densità (S3 HD) è progettato per carichi di lavoro specifici: [multi-tenancy](search-modeling-multitenant-saas-applications.md) e grandi quantità di indici di dimensioni ridotte (un milione di documenti per ogni indice, tremila indici per ogni servizio). Questo livello non contiene la [funzionalità indicizzatore](search-indexer-overview.md). In S3 HD, l'inserimento dati deve sfruttare l'approccio push, usando le chiamate API per eseguire il push dei dati dall'origine all'indice. 

> [!NOTE]
> Il provisioning di un servizio viene eseguito in base al piano tariffario specifico. Il passaggio a un nuovo piano tariffario per ottenere più capacità prevede il provisioning di un nuovo servizio (non esiste alcun aggiornamento sul posto). Per altre informazioni, vedere [Scegliere uno SKU o un piano tariffario](search-sku-tier.md). Per altre informazioni sulla regolazione delle capacità all'interno di un servizio di cui è già stato effettuato il provisioning, vedere [Ridimensionare i livelli di risorse per le query e l'indicizzazione dei carichi di lavoro](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Limiti delle sottoscrizioni
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Limiti relativi ad Archiviazione
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Limiti per gli indici

| Risorsa | Gratuito | Basic&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Numero massimo di indici |3 |5 o 15 |50 |200 |200 |1000 per partizione o 3000 per servizio |10 |10 |
| Numero massimo di campi semplici per indice |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Numero massimo di campi di raccolta complessi per indice |40 |40 |40 |40 |40 |40 |40 |40 |
| Numero massimo di elementi in tutte le raccolte complesse per documento |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Profondità massima dei campi complessi |10 |10 |10 |10 |10 |10 |10 |10 |
| Numero massimo di [suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggesters) per indice |1 |1 |1 |1 |1 |1 |1 |1 |
| Numero massimo di [profili di punteggio](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) per indice |100 |100 |100 |100 |100 |100 |100 |100 |
| Numero massimo di funzioni per profilo |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> i servizi di base creati prima del 2017 dicembre hanno limiti inferiori (5 anziché 15) sugli indici. Il livello Basic è l'unico SKU con un limite inferiore a 100 campi per indice.

<a name="document-limits"></a>

## <a name="document-limits"></a>Limiti per i documenti 

A partire da ottobre 2018, non vi sono più limiti per i documenti per i nuovi servizi creati in qualsiasi livello fatturabile (Basic, S1, S2, S3, S3 HD) in qualsiasi area. Il limite al numero di documenti è stato abolito per la maggior parte delle aree da novembre/dicembre 2017, ma cinque aree hanno continuato ad imporre limiti sui documenti. A seconda di dove e quando è stato creato un servizio di ricerca, è possibile che si stia eseguendo un servizio ancora soggetto a limiti sui documenti.

Per determinare se il servizio dispone di limiti per i documenti, controllare il riquadro Utilizzo nella pagina Panoramica del servizio. Il numero di documenti è illimitato o sottoposto a un limite in base al livello.

  ![Riquadro Utilizzo](media/search-limits-quotas-capacity/portal-usage-tile.png)

### <a name="regions-previously-having-document-limits"></a>Aree che in precedenza avevano limiti sui documenti

Se il portale indica un limite sui documenti, il servizio è stato creato prima della fine del 2017 o è stato creato in un data center con cluster con capacità ridotte per ospitare i servizi di Ricerca di Azure:

+ Australia orientale
+ Asia orientale
+ India centrale
+ Giappone occidentale
+ Stati Uniti centro-occidentali

Per i servizi soggetti a limiti per i documenti, si applicano i limiti massimi seguenti:

|  Gratuito | Basic | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10,000 |1&nbsp;milione |15 milioni per partizione o 180 milioni per servizio |60 milioni per partizione o 720 milioni per servizio |120 milioni per partizione o 1,4 miliardi per servizio |1 milione per indice o 200 milioni per partizione |

Se il servizio ha dei limiti che costituiscono un blocco, creare un nuovo servizio e pubblicarvi nuovamente tutti i contenuti. Non è previsto alcun meccanismo automatico per eseguire nuovamente il provisioning del servizio nel nuovo hardware in background.

> [!Note] 
> Per i servizi S3 ad alta densità creati dopo la fine del 2017, è stato rimosso il limite di 200 milioni di documenti per partizione, ma rimane il limite di 1 milione di documenti per indice.


### <a name="document-size-limits-per-api-call"></a>Limiti di dimensioni dei documenti per chiamate all'API

La dimensione massima del documento quando si chiama un'API di indice è di circa 16 MB.

La dimensione del documento costituisce effettivamente un limite alla dimensione del corpo della richiesta dell'API di indice. Poiché è possibile passare all'API di indice un batch costituito da più documenti in una sola volta, il limite della dimensione dipende in effetti dal numero di documenti presenti nel batch. Per un batch con un solo documento, la dimensione massima del documento è di 16 MB di JSON.

Per limitare la dimensione del documento, è necessario escludere dalla richiesta i dati non disponibili per query. Le immagini e altri dati binari non sono direttamente disponibili per query e non devono quindi essere archiviati nell'indice. Per integrare i dati non disponibili per query nei risultati della ricerca, definire un campo non disponibile per la ricerca che consenta l'archiviazione di un riferimento URL nella risorsa.

## <a name="indexer-limits"></a>Limiti dell'indicizzatore

Sono disponibili tempi di esecuzione massimi per fornire bilanciamento e stabilità al servizio nel suo complesso, ma i set di dati di dimensioni maggiori potrebbero richiedere più tempo di indicizzazione rispetto al massimo consentito. Se un processo di indicizzazione non può essere completato nel tempo massimo consentito, provare a eseguirlo in una pianificazione. L'utilità di pianificazione tiene traccia dello stato di indicizzazione. Se un processo di indicizzazione pianificato viene interrotto per qualsiasi motivo, alla successiva esecuzione pianificata l'indicizzatore può riprendere dall'ultima interruzione.


| Risorsa | Gratuito&nbsp;<sup>1</sup> | Basic&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Numero massimo di indicizzatori |3 |5 o 15|50 |200 |200 |N/D |10 |10 |
| Numero massimo di origini dati |3 |5 o 15 |50 |200 |200 |N/D |10 |10 |
| Numero massimo di set di competenze <sup>4</sup> |3 |5 o 15 |50 |200 |200 |N/D |10 |10 |
| Carico di indicizzazione massimo per chiamata |10.000 documenti |Limitato solo da numero massimo di documenti |Limitato solo da numero massimo di documenti |Limitato solo da numero massimo di documenti |Limitato solo da numero massimo di documenti |N/D |Nessun limite |Nessun limite |
| Pianificazione minima | 5 minuti |5 minuti |5 minuti |5 minuti |5 minuti |5 minuti |5 minuti | 5 minuti |
| Tempo massimo di esecuzione <sup>5</sup> | 1-3 minuti |24 ore |24 ore |24 ore |24 ore |N/D  |24 ore |24 ore |
| Tempo massimo di esecuzione per set di competenze di ricerca cognitiva o indicizzazione BLOB con analisi delle immagini <sup>5</sup> | 3-10 minuti |2 ore |2 ore |2 ore |2 ore |N/D  |2 ore |2 ore |
| Indicizzatore BLOB: dimensioni massime per un BLOB, MB |16 |16 |128 |256 |256 |N/D  |256 |256 |
| Indicizzatore BLOB: numero massimo di caratteri di contenuto estratti da un BLOB |32.000 |64.000 |4&nbsp;milioni |4&nbsp;milioni |4&nbsp;milioni |N/D |4&nbsp;milioni |4&nbsp;milioni |

<sup>1</sup> I servizi del livello Gratuito hanno un tempo massimo di esecuzione degli indicizzatori di 3 minuti per le origini BLOB e di 1 minuto per tutte le altre origini dati. Per l'indicizzazione di intelligenza artificiale che effettua chiamate in Servizi cognitivi, i servizi gratuiti sono limitati a 20 transazioni gratuite al giorno, in cui una transazione viene definita come documento che passa correttamente attraverso la pipeline di arricchimento.

<sup>2</sup> i servizi Basic creati prima del 2017 dicembre hanno limiti inferiori (5 anziché 15) per gli indicizzatori, le origini dati e skillsets.

<sup>3</sup> I servizi del livello S3 HD non includono il supporto per l'indicizzatore.

<sup>4</sup> Numero massimo di 30 competenze per set di competenze.

<sup>5</sup> I carichi di lavoro di ricerca cognitiva e l'analisi delle immagini nell'indicizzazione BLOB di Azure presentano tempi di esecuzione più brevi rispetto all'indicizzazione di testo normale. L'analisi delle immagini e l'elaborazione del linguaggio naturale sono attività complesse e utilizzano una quantità estremamente elevata di potenza di elaborazione disponibile. Il tempo di esecuzione è stato ridotto per offrire la possibilità di eseguire altri processi nella coda.  

## <a name="synonym-limits"></a>Limiti dei sinonimi

Il numero massimo di mappe sinonime consentite varia in base al piano tariffario. Ogni regola può avere fino a 20 espansioni, dove un'espansione è un termine equivalvent. Ad esempio, se si specifica "Cat", l'associazione con "Kitty", "Feline" e "Felis" (il genere per Cats) viene conteggiata come 3 espansioni.

| Risorsa | Gratuito | Basic | S1 | S2 | S3 | S3-HD |L1 | L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| Mappe sinonimo massime |3 |3|5 |10 |20 |20 | 10 | 10 |
| Numero massimo di regole per mappa |5000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="queries-per-second-qps"></a>Query al secondo

Le stime di query al secondo devono essere sviluppate in modo indipendente da ogni cliente. Le dimensioni e la complessità dell'indice, le dimensioni e la complessità della query e la quantità di traffico sono i fattori principali in base ai quali è possibile determinare il numero di query al secondo. Non è possibile fornire stime significative se questi fattori sono sconosciuti.

Le stime sono più prevedibili se vengono calcolate su servizi in esecuzione su risorse dedicate (livelli Basic e Standard). In questo caso, infatti, è possibile stimare in modo più preciso il numero di query al secondo, poiché si ha il controllo di un numero maggiore di parametri. Per informazioni su come eseguire la stima, vedere [Considerazioni sulle prestazioni e sull'ottimizzazione di Ricerca di Azure](search-performance-optimization.md).

Per i livelli ottimizzati per l'archiviazione è necessario prevedere una velocità effettiva delle query inferiore e una latenza superiore rispetto ai livelli standard.  La metodologia per stimare le prestazioni delle query che si verificheranno è identica a quella dei livelli standard.

## <a name="data-limits-cognitive-search"></a>Limiti dei dati (ricerca cognitiva)

Una [pipeline di ricerca cognitiva](cognitive-search-concept-intro.md) che effettua chiamate a una risorsa di Analisi del testo per il [riconoscimento di entità](cognitive-search-skill-entity-recognition.md), l'[estrazione di frasi chiave](cognitive-search-skill-keyphrases.md), l'[analisi della valutazione ](cognitive-search-skill-sentiment.md) e il [rilevamento della lingua](cognitive-search-skill-language-detection.md) è soggetta a limiti dei dati. La dimensione massima di un record deve essere di 50.000 caratteri misurata [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)da. Se è necessario suddividere i dati prima di inviarli all'analizzatore di valutazione, usare la [competenza cognitiva di divisione del testo](cognitive-search-skill-textsplit.md).

## <a name="api-request-limits"></a>Limiti delle richieste API
* 16 MB al massimo per <sup>1</sup> richiesta
* 8 KB al massimo per la lunghezza dell'URL
* 1000 documenti al massimo per ogni batch di carichi, unioni o eliminazioni di indice
* 32 campi al massimo nella clausola $orderby
* 32.766 byte (32 KB meno 2 byte) di testo con codifica UTF-8 per la dimensione massima del termine di ricerca

<sup>1</sup> In Ricerca di Azure il corpo di una richiesta è soggetto a un limite massimo di 16 MB, che impone un limite pratico ai contenuti di singoli campi o raccolte non vincolati a limiti teorici. Per altre informazioni sulla composizione dei campi e sulle relative restrizioni, vedere [Supported data types](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) (Tipi di dati supportati).

## <a name="api-response-limits"></a>Limiti di risposta API
* 1000 documenti al massimo restituiti per pagina di risultati della ricerca
* 100 suggerimenti al massimo restituiti per richiesta di API di suggerimento

## <a name="api-key-limits"></a>Limiti delle chiavi API
Le chiavi API vengono usate per l'autenticazione del servizio. Sono disponibili due tipi. Le chiavi amministratore, specificate nell'intestazione della richiesta, consentono l'accesso completo in lettura e scrittura al servizio. Le chiavi di query sono di sola lettura, sono specificate nell'URL e in genere sono distribuite ad applicazioni client.

* 2 chiavi di amministrazione al massimo per ogni servizio
* 50 chiavi di query al massimo per ogni servizio
