---
title: 'Unità richiesta e stima della velocità effettiva: Azure Cosmos DB | Microsoft Docs'
description: Informazioni su come comprendere, specificare e stimare i requisiti relativi alle unità richiesta in Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: d0a3c310-eb63-4e45-8122-b7724095c32f
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: rimman
ms.openlocfilehash: 2b69b3b5fee0d1148a762f817d9c5a8bc67806e7
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
---
# <a name="request-units-in-azure-cosmos-db"></a>Unità richiesta in Azure Cosmos DB

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) è il database multimodello distribuito a livello globale di Microsoft. Con Azure Cosmos DB non è necessario affittare macchine virtuali, distribuire software o monitorare database. Azure Cosmos DB è gestito e monitorato costantemente dai migliori tecnici Microsoft, in modo da offrire disponibilità, prestazioni e protezione dei dati di elevata qualità. È possibile accedere ai dati usando le API preferite, ad esempio l'[API SQL](documentdb-introduction.md), l'[API MongoDB](mongodb-introduction.md), l'[API Table](table-introduction.md) e Graph tramite l'[API Gremlin](graph-introduction.md). Tutte queste API sono supportate a livello nativo. 

La valuta di Azure Cosmos DB è costituita dalle **unità richiesta (UR)**. Con le unità richiesta, non è necessario riservare capacità di lettura/scrittura né effettuare il provisioning di CPU, memoria e operazioni di I/O al secondo. Azure Cosmos DB supporta una serie di API con operazioni diverse, che vanno dalla semplice lettura e scrittura alle query per grafi più complesse. Poiché non tutte le richieste sono uguali, viene loro assegnata una quantità normalizzata di **unità richiesta** in base alla quantità di calcolo necessaria per servire la richiesta. Il numero di unità richiesta per un'operazione è deterministico ed è possibile tenere traccia del numero di unità richiesta utilizzate da qualsiasi operazione in Azure Cosmos DB tramite un'intestazione della risposta. 

Per prestazioni prevedibili, è necessario riservare una velocità effettiva in unità di 100 UR/secondo. È possibile [eseguire una stima delle esigenze di velocità effettiva](request-units.md#estimating-throughput-needs) usando il [calcolatore di unità richiesta](https://www.documentdb.com/capacityplanner) di Azure Cosmos DB.

![Calcolatore della velocità effettiva][5]

Alla fine della lettura, si avranno le risposte alle domande seguenti:  

* Cosa sono le unità richiesta e gli addebiti richiesta in Azure Cosmos DB?
* Come è possibile specificare la capacità delle unità richiesta per un contenitore in Azure Cosmos DB?
* Come si possono stimare le esigenze relative alle unità richiesta per l'applicazione?
* Cosa accade se si supera la capacità delle unità richiesta per un contenitore in Azure Cosmos DB?

Dal momento che Azure Cosmos DB è un database multimodello, è importante tenere presente che questo articolo è applicabile a tutti i modelli di dati e le API in Azure Cosmos DB. Questo articolo usa termini generici, come *contenitore* ed *elemento*, per fare riferimento in modo generico a una raccolta, un grafo o una tabella e un documento, un nodo o un'entità, rispettivamente.

## <a name="request-units-and-request-charges"></a>Unità richiesta e addebiti richiesta
Azure Cosmos DB offre prestazioni veloci e prevedibili, *riservando* risorse per soddisfare le esigenze a livello di velocità effettiva dell'applicazione.  Poiché i modelli di carico e accesso dell'applicazione cambiano nel tempo, Azure Cosmos DB consente di aumentare o diminuire facilmente la quantità di velocità effettiva riservata disponibile per l'applicazione.

Con Azure Cosmos DB, la velocità effettiva riservata è specificata in termini di unità richiesta elaborate al secondo. Si possono considerare le unità richiesta come una specie di valuta della velocità effettiva, secondo cui si *riserva* una quantità garantita di unità richiesta disponibili al secondo per l'applicazione.  Ogni operazione in Azure Cosmos DB, ovvero scrittura di un documento, esecuzione di una query, aggiornamento di un documento, utilizza CPU, memoria e operazioni di I/O al secondo.  In altre parole, ogni operazione comporta un *addebito richiesta* espresso in *unità richiesta*.  La conoscenza dei fattori che influiscono sugli addebiti delle unità richiesta, insieme ai requisiti di velocità effettiva dell'applicazione, consente di eseguire l'applicazione nel modo più economicamente conveniente possibile. Anche Esplora dati nel portale di Azure è un ottimo strumento per testare gli elementi di base di una query.

È consigliabile guardare prima di tutto il video seguente, in cui Andrew Liu, Program Manager di Azure Cosmos DB, illustra le unità richiesta.

> [!VIDEO https://www.youtube.com/embed/stk5WSp5uX0]
> 
> 

## <a name="specifying-request-unit-capacity-in-azure-cosmos-db"></a>Specifica della capacità in unità richiesta in Azure Cosmos DB
Quando si crea un nuovo contenitore, si specifica il numero di unità richiesta al secondo (UR/sec) da riservare. In base alla velocità effettiva di cui è stato effettuato il provisioning, Azure Cosmos DB alloca partizioni fisiche per ospitare il contenitore e suddivide/ribilancia la crescita dei dati nelle partizioni.

I contenitori di Azure Cosmos DB possono essere creati come fissi o illimitati. I contenitori a dimensione fissa hanno un limite massimo di 10 GB e velocità effettiva di 10.000 UR/s. Per creare un contenitore illimitato, è necessario specificare una velocità effettiva minima di 1.000 UR/s e una [chiave di partizione](partition-data.md). Dal momento che i dati potrebbero essere stati suddivisi in più partizioni, è necessario scegliere una chiave di partizione che abbia un'elevata cardinalità (da 100 a milioni di valori distinti). La selezione di una chiave di partizione con molti valori distinti garantisce la scalabilità uniforme di contenitori/tabelle/grafi e richieste in Azure Cosmos DB. 

> [!NOTE]
> Una chiave di partizione è un limite logico, non un limite fisico. Non è quindi necessario limitare il numero di valori distinti per le chiavi di partizioni. È in effetti consigliabile avere un numero maggiore di valori distinti per le chiavi di partizione, perché Azure Cosmos DB offre un numero maggiore di opzioni per il bilanciamento del carico.

Ecco un frammento di codice per la creazione di un contenitore con 3000 unità richiesta al secondo usando .NET SDK:

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

Azure Cosmos DB usa un modello di prenotazione per la velocità effettiva, ovvero viene fatturata la quantità di velocità effettiva *riservata*, indipendentemente dalla quantità di tale velocità effettiva *usata* attivamente. A mano a mano che i modelli di carico, dati e utilizzo dell'applicazione cambiano, è possibile aumentare e ridurre facilmente la quantità di unità richiesta riservate usando gli SDK o il [portale di Azure](https://portal.azure.com).

Viene eseguito il mapping di ogni contenitore a una risorsa `Offer` in Azure Cosmos DB, che include metadati sulla velocità effettiva di cui è stato effettuato il provisioning. È possibile modificare la velocità effettiva allocata esaminando l'offerta corrispondente relativa alla risorsa per un contenitore, quindi aggiornarla con il nuovo valore per la velocità effettiva. Ecco un frammento di codice per la modifica della velocità effettiva di un contenitore fino a 5000 unità richiesta al secondo usando .NET SDK:

```csharp
// Fetch the resource to be updated
Offer offer = client.CreateOfferQuery()
                .Where(r => r.ResourceLink == collection.SelfLink)    
                .AsEnumerable()
                .SingleOrDefault();

// Set the throughput to 5000 request units per second
offer = new OfferV2(offer, 5000);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

La modifica della velocità effettiva non influisce sulla disponibilità del contenitore. La nuova velocità effettiva riservata viene in genere applicata entro pochi secondi, in corrispondenza dell'applicazione della nuova velocità effettiva.

## <a name="throughput-isolation-in-globally-distributed-databases"></a>Isolamento della velocità effettiva nei database distribuiti a livello globale

In caso di replica del database in più di un'area, Azure Cosmos DB implementa l'isolamento della velocità effettiva per assicurare che l'uso delle unità richiesta in un'area non abbia ripercussioni negative sull'uso delle unità richiesta in un'altra area. Ad esempio, se si eseguono un'operazione di scrittura di dati in un'area e un'operazione di lettura di dati in un'altra area, le unità richiesta usate per eseguire l'operazione di scrittura nell'area *A* non vengono detratte dalle unità richiesta usate per l'operazione di lettura eseguita nell'area *B*. Le unità richiesta non vengono suddivise tra le aree in cui è stata eseguita la distribuzione. Ogni area in cui il database viene replicato dispone di tutte le unità richiesta per le quali è stato eseguito il provisioning. Per altre informazioni sulla replica globale, vedere [Come distribuire i dati a livello globale con Azure Cosmos DB](distribute-data-globally.md).

## <a name="request-unit-considerations"></a>Considerazioni sulle unità richiesta
Quando si stima il numero di unità richiesta di cui eseguire il provisioning per il contenitore di Azure Cosmos DB, è importante considerare le variabili seguenti:

* **Dimensioni dell'elemento**. Con l'aumento delle dimensioni, aumenta anche il numero di unità richiesta usate per leggere o scrivere i dati.
* **Numero di proprietà dell'elemento**. Supponendo l'indicizzazione predefinita di tutte le proprietà, le unità usate per scrivere un documento, un nodo o un'entità aumentano man mano che aumenta il numero delle proprietà.
* **Coerenza dei dati**. Se si usano modelli di coerenza dei dati come Assoluta o Decadimento ristretto, verranno usate unità richiesta aggiuntive per leggere gli elementi.
* **Proprietà indicizzate**. I criteri di indicizzazione in ogni contenitore determinano le proprietà che vengono indicizzate per impostazione predefinita. È possibile ridurre l'utilizzo di unità richiesta limitando il numero di proprietà indicizzate o abilitando l'indicizzazione differita.
* **Indicizzazione del documento**. Per impostazione predefinita, ogni elemento viene automaticamente indicizzato. Se si sceglie di non indicizzare alcuni elementi, è possibile usare un numero inferiore di unità richiesta.
* **Modelli di query**. La complessità di una query influisce sulla quantità di unità richiesta usate per un'operazione. Il numero di predicati, la natura dei predicati, le proiezioni, il numero di funzioni definite dall'utente e le dimensioni dei dati di origine sono tutti fattori che incidono sul costo delle operazioni di query.
* **Utilizzo di script**.  Come le query, le stored procedure e i trigger utilizzano le unità richiesta in base alla complessità delle operazioni da eseguire. Quando si sviluppa l'applicazione, controllare l'intestazione per l'addebito delle richieste per comprendere meglio il modo in cui ciascuna operazione usa la capacità delle unità di richiesta.

## <a name="estimating-throughput-needs"></a>Stima delle esigenze di velocità effettiva
Un'unità di richiesta è una misura normalizzata del costo di elaborazione della richiesta. Una singola unità richiesta rappresenta la capacità di elaborazione necessaria per leggere, tramite collegamento automatico o ID, un singolo elemento da 1 KB costituito da 10 valori di proprietà univoci, escluse le proprietà di sistema. Una richiesta di creazione (inserimento), sostituzione o eliminazione dello stesso elemento utilizzerà più capacità di elaborazione del servizio e quindi più unità richiesta.   

> [!NOTE]
> La base di 1 unità richiesta per un elemento da 1 KB corrisponde a una semplice operazione GET tramite collegamento automatico o ID dell'elemento.
> 
> 

La tabella riportata di seguito mostra il numero di unità richiesta di cui effettuare il provisioning per elementi con tre dimensioni diverse, ovvero 1 KB, 4 KB e 64 KB, e due livelli di prestazioni diversi, ovvero 500 letture al secondo + 100 scritture al secondo e 500 letture al secondo + 500 scritture al secondo. La coerenza dei dati è stata configurata come *Sessione* e i criteri di indicizzazione sono stati impostati su *Nessuno*.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Dimensioni dell'elemento</strong></p></td>
            <td valign="top"><p><strong>Letture al secondo</strong></p></td>
            <td valign="top"><p><strong>Scritture al secondo</strong></p></td>
            <td valign="top"><p><strong>Unità richiesta</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1) + (100 * 5) = 1.000 UR/sec</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1) + (500 * 5) = 3.000 UR/sec</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1,3) + (100 * 7) = 1.350 UR/sec</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1,3) + (500 * 7) = 4.150 UR/sec</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 10) + (100 * 48) = 9.800 UR/sec</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 10) + (500 * 48) = 29.000 UR/sec</p></td>
        </tr>
    </tbody>
</table>

### <a name="use-the-request-unit-calculator"></a>Usare il calcolatore di unità richiesta
Per semplificare l'ottimizzazione delle stime di velocità effettiva da parte dei clienti, è disponibile un [calcolatore di unità richiesta](https://www.documentdb.com/capacityplanner) basato sul Web, che consente di stimare i requisiti relativi alle unità richiesta per operazioni tipiche, incluse le seguenti:

* Creazione di elementi (scrittura)
* Lettura di elementi
* Eliminazione di elementi
* Aggiornamento di elementi

Lo strumento include anche il supporto per la stima delle esigenze di archiviazione dei dati in base agli elementi di esempio specificati.

L'uso dello strumento è molto semplice:

1. Caricare uno o più elementi rappresentativi, ad esempio un documento JSON.
   
    ![Caricare elementi nel calcolatore di unità richiesta][2]
2. Per stimare i requisiti di archiviazione, immettere il numero totale di elementi (ad esempio, documenti, tabelle o grafi) che si prevede di archiviare.
3. Immettere il numero di operazioni di creazione, lettura, aggiornamento ed eliminazione necessarie (al secondo). Per stimare gli addebiti delle unità richiesta per le operazioni di aggiornamento di elementi, caricare una copia dell'elemento di esempio usato nel passaggio 1 precedente che include aggiornamenti di campi tipici.  Ad esempio, se gli aggiornamenti di elementi modificano in genere due proprietà denominate *lastLogin* e *userVisits*, è sufficiente copiare un elemento di esempio, aggiornare i valori per queste due proprietà e caricare l'elemento copiato.
   
    ![Immettere i requisiti relativi alla velocità effettiva nel calcolatore di unità richiesta][3]
4. Fare clic su Calcola ed esaminare i risultati.
   
    ![Risultati del calcolatore di unità richiesta][4]

> [!NOTE]
> Se sono presenti tipi di elementi che variano notevolmente in termini di dimensioni e numero di proprietà indicizzate, caricare un campione di ogni *tipo* di elemento tipico nello strumento e quindi calcolare i risultati.
> 
> 

### <a name="use-the-azure-cosmos-db-request-charge-response-header"></a>Usare l'intestazione della risposta di addebito della richiesta di Azure Cosmos DB
Ogni risposta dal servizio Azure Cosmos DB include un'intestazione personalizzata (`x-ms-request-charge`) che contiene le unità richiesta usate per una determinata richiesta. Questa intestazione è accessibile anche tramite gli SDK di Azure Cosmos DB. In .NET SDK, `RequestCharge` è una proprietà dell'oggetto `ResourceResponse`.  Per quanto riguarda le query, Esplora dati di Azure Cosmos DB disponibile nel portale di Azure offre informazioni sugli addebiti per le richieste relative alle query eseguite.

Con questa premessa, un metodo per stimare la quantità di velocità effettiva riservata richiesta dall'applicazione consiste nel registrare l'addebito delle unità richiesta associato all'esecuzione di operazioni tipiche rispetto a un elemento rappresentativo usato dall'applicazione e quindi nello stimare il numero di operazioni che si prevede di eseguire al secondo.  Assicurarsi di misurare e includere anche le query tipiche e l'utilizzo di script di Azure Cosmos DB.

> [!NOTE]
> Se sono presenti tipi di elementi che variano notevolmente in termini di dimensioni e numero di proprietà indicizzate, registrare l'addebito delle unità richiesta per le operazioni applicabili associato a ogni *tipo* di elemento tipico.
> 
> 

Ad esempio: 

1. Registrare l'addebito delle unità richiesta di creazione (inserimento) di un elemento tipico. 
2. Registrare l'addebito delle unità richiesta di lettura di un elemento tipico.
3. Registrare l'addebito delle unità richiesta di aggiornamento di un elemento tipico.
4. Registrare l'addebito delle unità richiesta di query tipiche su elementi comuni.
5. Registrare l'addebito delle unità richiesta di tutti gli script personalizzati (stored procedure, trigger, funzioni definite dall'utente) utilizzati dall'applicazione.
6. Calcolare le unità richiesta necessarie in base al numero stimato di operazioni che si prevede di eseguire al secondo.

## <a name="a-request-unit-estimate-example"></a>Esempio di stima delle unità richiesta
Considerare questo documento da circa 1 KB:

```json
{
 "id": "08259",
  "description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
  "tags": [
    {
      "name": "cereals ready-to-eat"
    },
    {
      "name": "kellogg"
    },
    {
      "name": "kellogg's crispix"
    }
  ],
  "version": 1,
  "commonName": "Includes USDA Commodity B855",
  "manufacturerName": "Kellogg, Co.",
  "isFromSurvey": false,
  "foodGroup": "Breakfast Cereals",
  "nutrients": [
    {
      "id": "262",
      "description": "Caffeine",
      "nutritionValue": 0,
      "units": "mg"
    },
    {
      "id": "307",
      "description": "Sodium, Na",
      "nutritionValue": 611,
      "units": "mg"
    },
    {
      "id": "309",
      "description": "Zinc, Zn",
      "nutritionValue": 5.2,
      "units": "mg"
    }
  ],
  "servings": [
    {
      "amount": 1,
      "description": "cup (1 NLEA serving)",
      "weightInGrams": 29
    }
  ]
}
```

> [!NOTE]
> I documenti sono minimizzati in Azure Cosmos DB, quindi le dimensioni del documento precedente calcolate dal sistema sono leggermente inferiori a 1 KB.
> 
> 

La tabella seguente mostra gli addebiti delle unità richiesta approssimativi per le operazioni tipiche su questo elemento. L'addebito delle unità richiesta approssimativo presuppone che il livello di coerenza dell'account sia impostato su *Sessione* e che tutti gli elementi siano indicizzati automaticamente:

| Operazione | Addebito delle unità richiesta |
| --- | --- |
| Creare elemento |~15 unità richiesta |
| Leggere l'elemento |~1 unità richiesta |
| Eseguire query sull'elemento in base all'ID |~2,5 unità richiesta |

Questa tabella mostra anche gli addebiti approssimativi delle unità richiesta per le query tipiche usate nell'applicazione:

| Query | Addebito delle unità richiesta | Numero di elementi restituiti |
| --- | --- | --- |
| Selezionare alimenti in base all'ID |~2,5 unità richiesta |1 |
| Selezionare alimenti in base al produttore |~7 unità richiesta |7 |
| Selezionare per gruppo di alimenti e ordinare in base al peso |~70 unità richiesta |100 |
| Selezionare i primi 10 alimenti in un gruppo di alimenti |~10 unità richiesta |10 |

> [!NOTE]
> Gli addebiti delle unità richiesta variano in base al numero di elementi restituiti.
> 
> 

Con queste informazioni è possibile stimare i requisiti relativi alle unità richiesta per questa applicazione, dato il numero di operazioni e query previste al secondo:

| Operazione/query | Numero stimato al secondo | Unità richiesta necessarie |
| --- | --- | --- |
| Creare elemento |10 |150 |
| Leggere l'elemento |100 |100 |
| Selezionare alimenti in base al produttore |25 |175 |
| Selezionare per gruppo di alimenti |10 |700 |
| Selezionare i primi 10 |15 |Totale 150 |

Si prevede in questo caso un requisito di velocità effettiva medio di 1.275 unità richiesta al secondo.  Arrotondando a 100 unità più vicine, si dovrà effettuare il provisioning di 1300 unità richiesta al secondo per il contenitore dell'applicazione.

## <a id="RequestRateTooLarge"></a> Superamento dei limiti della velocità effettiva riservata in Azure Cosmos DB
Tenere presente che il consumo delle unità richiesta è valutato in base a una frequenza al secondo. Per le applicazioni che superano la frequenza di unità richiesta con provisioning, la frequenza delle richieste verrà limitata fino al ritorno della frequenza sotto il livello di velocità effettiva con provisioning. Quando la frequenza di una richiesta viene limitata, il server termina preventivamente la richiesta con `RequestRateTooLargeException` (codice di stato HTTP 429) e restituisce l'intestazione `x-ms-retry-after-ms`, che indica la quantità di tempo, in millisecondi, che l'utente deve attendere prima di eseguire di nuovo la richiesta.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Se si usano le query LINQ e .NET SDK per client, non è quasi mai necessario gestire questa eccezione, perché la versione corrente di .NET SDK per client rileva la risposta in modo implicito, rispetta l'intestazione retry-after specificata dal server e ripete automaticamente la richiesta. A meno che all'account non accedano contemporaneamente più client, il tentativo successivo riuscirà.

Se più client operano collettivamente al di sopra della frequenza delle richieste, il comportamento di ripetizione dei tentativi predefinito potrebbe non essere sufficiente e il client genererà una `DocumentClientException` con codice di stato 429 per l'applicazione. In casi come questo, si può valutare la possibilità di gestire la logica e il comportamento di ripetizione dei tentativi nelle routine di gestione degli errori dell'applicazione o di aumentare la velocità effettiva con provisioning per il contenitore.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla velocità effettiva riservata con i database Azure Cosmos DB, vedere queste risorse:

* [Prezzi di Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/)
* [Partizionamento dei dati in Azure Cosmos DB](partition-data.md)

Per altre informazioni su Azure Cosmos DB, vedere la relativa [documentazione](https://azure.microsoft.com/documentation/services/cosmos-db/). 

Per iniziare a testare la scalabilità e le prestazioni con Azure Cosmos DB, vedere [Test delle prestazioni e della scalabilità con Azure Cosmos DB](performance-testing.md).

[2]: ./media/request-units/RUEstimatorUpload.png
[3]: ./media/request-units/RUEstimatorDocuments.png
[4]: ./media/request-units/RUEstimatorResults.png
[5]: ./media/request-units/RUCalculator2.png
