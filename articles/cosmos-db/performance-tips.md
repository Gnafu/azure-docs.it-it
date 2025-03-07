---
title: Suggerimenti sulle prestazioni di Azure Cosmos DB per .NET
description: Informazioni sulle opzioni di configurazione client per migliorare le prestazioni di Azure Cosmos database
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: sngun
ms.openlocfilehash: bdf81eb447596c8f580809eed99004186a81eacf
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065926"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Suggerimenti sulle prestazioni per Azure Cosmos DB e .NET

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB è un database distribuito veloce e flessibile, facilmente scalabile e con latenza e velocità effettiva garantite. Non è necessario apportare modifiche significative all'architettura o scrivere codice complesso per ridimensionare il database con Azure Cosmos DB. Aumentare o ridurre le prestazioni è semplice come eseguire una singola chiamata API. Per altre informazioni, vedere [Effettuare il provisioning della velocità effettiva per un contenitore](how-to-provision-container-throughput.md) oppure [Effettuare il provisioning della velocità effettiva per un database](how-to-provision-database-throughput.md). Dato che si accede ad Azure Cosmos DB tramite chiamate di rete, è tuttavia possibile apportare ottimizzazioni lato client per ottenere prestazioni ottimali se si usa l'[SQL .NET SDK](sql-api-sdk-dotnet-standard.md).

Se si vogliono migliorare le prestazioni del database, prendere in considerazione le opzioni seguenti:

## <a name="networking"></a>Rete
<a id="direct-connection"></a>

1. **Criteri di connessione: usare la modalità di connessione diretta**

    La modalità di connessione di un client ad Azure Cosmos DB influisce significativamente sulle prestazioni, in particolare in termini di latenza lato client osservata. Sono disponibili due impostazioni di configurazione chiave per la configurazione dei criteri di connessione client, ovvero la *modalità* di connessione e il *protocollo* di connessione.  Le due modalità disponibili sono:

   * Modalità gateway (predefinita)
      
     La modalità gateway è supportata in tutte le piattaforme SDK ed è l'impostazione predefinita configurata. Se l'applicazione è in esecuzione in una rete aziendale con limitazioni rigide del firewall, la modalità gateway è la scelta migliore, perché usa la porta HTTPS standard e un singolo endpoint. A livello di prestazioni, tuttavia, la modalità Gateway prevede un hop di rete aggiuntivo ogni volta che i dati vengono letti o scritti in Azure Cosmos DB. La modalità diretta offre quindi prestazioni migliori grazie al numero minore di hop di rete. La modalità di connessione gateway è consigliabile anche quando si eseguono applicazioni in ambienti con un numero limitato di connessioni socket, ad esempio quando si usa un piano a consumo o Funzioni di Azure. 

   * Modalità diretta

     La modalità diretta supporta la connettività tramite protocolli TCP e HTTPS. Se si usa la versione più recente di .NET SDK, la modalità di connettività diretta è supportata in .NET Standard 2,0 e .NET Framework. Quando si usa la modalità diretta, sono disponibili due opzioni del protocollo:

     * TCP
     * HTTPS

     In modalità Gateway, Cosmos DB usa la porta 443 e le porte 10250, 10255 e 10256 quando viene usata l'API di Azure Cosmos DB per MongoDB. La porta 10250 viene associata a un'istanza di MongoDB predefinita senza replica geografica e le porte 10255/10256 vengono associate all'istanza di MongoDB con funzionalità di replica geografica. Quando si usa TCP in modalità diretta, oltre alle porte gateway è necessario verificare che le porte nell'intervallo tra 10000 e 20000 siano aperte perché Azure Cosmos DB usa porte TCP dinamiche. Se queste porte non sono aperte e si tenta di usare TCP, si riceverà un errore 503 (Servizio non disponibile). La tabella seguente illustra le modalità di connettività disponibili per API diverse e l'utente delle porte di servizio per ogni API:

     |Modalità di connessione  |Protocollo supportato  |SDK supportati  |API/porta servizio  |
     |---------|---------|---------|---------|
     |Gateway  |   HTTPS    |  Tutti gli SDK    |   SQL(443), Mongo(10250, 10255, 10256), Table(443), Cassandra(10350), Graph(443)    |
     |Diretto    |    HTTPS     |  .NET e Java SDK    |   Porte nell'intervallo da 10.000 a 20.000    |
     |Diretto    |     TCP    |  .NET SDK    | Porte nell'intervallo da 10.000 a 20.000 |

     Azure Cosmos DB offre un modello di programmazione RESTful su HTTPS semplice e aperto. DocumentDB offre anche un protocollo TCP efficiente, con un modello di comunicazione di tipo RESTful disponibile tramite .NET SDK per client. Sia il protocollo TCP diretto che il protocollo HTTPS usano SSL per l'autenticazione iniziale e la crittografia del traffico. Per prestazioni ottimali, usare il protocollo TCP quando possibile.

     La modalità di connessione viene configurata durante la creazione dell'istanza di DocumentClient con il parametro ConnectionPolicy. Se si usa la modalità diretta, è possibile configurare il protocollo entro il parametro ConnectionPolicy.

     ```csharp
     var serviceEndpoint = new Uri("https://contoso.documents.net");
     var authKey = "your authKey from the Azure portal";
     DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
     new ConnectionPolicy
     {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp
     });
     ```

     Poiché TCP è supportato solo in modalità diretta, se si usa la modalità gateway viene usato sempre il protocollo HTTPS per comunicare con il gateway e il valore del protocollo in ConnectionPolicy viene ignorato.

     ![Illustrazione dei criteri di connessione di Azure Cosmos DB](./media/performance-tips/connection-policy.png)

2. **Chiamare OpenAsync per evitare la latenza di avvio alla prima richiesta**

    Per impostazione predefinita, la prima richiesta ha una latenza più elevata perché deve recuperare la tabella di routing degli indirizzi. Per evitare questa latenza di avvio alla prima richiesta, è necessario chiamare OpenAsync() una volta durante l'inizializzazione, come indicato di seguito.

        await client.OpenAsync();
   <a id="same-region"></a>
3. **Collocare i client nella stessa area di Azure per ottenere prestazioni migliori**

    Quando possibile, inserire tutte le applicazioni che chiamano Azure Cosmos DB nella stessa area del database di Azure Cosmos. Per un confronto approssimativo, le chiamate ad Azure Cosmos DB eseguite nella stessa area vengono completate entro 1-2 millisecondi, mentre la latenza tra la costa occidentale e quella orientale degli Stati Uniti è > 50 millisecondi. Questa latenza può variare da richiesta a richiesta, in base alla route seguita dalla richiesta durante il passaggio dal client al limite del data center di Azure. È possibile ottenere la latenza più bassa possibile assicurandosi che l'applicazione chiamante si trovi nella stessa area di Azure in cui si trova l'endpoint di Azure Cosmos DB con provisioning. Per un elenco delle aree disponibili, vedere [Aree di Azure](https://azure.microsoft.com/regions/#services).

    ![Illustrazione dei criteri di connessione di Azure Cosmos DB](./media/performance-tips/same-region.png)
   <a id="increase-threads"></a>
4. **Aumentare il numero di thread/attività**

    Dato che le chiamate ad Azure Cosmos DB vengono eseguite sulla rete, può essere necessario modificare il grado di parallelismo delle richieste in modo che i tempi di attesa dell'applicazione client tra le richieste siano molto ridotti. Se si usa [Task Parallel Library](https://msdn.microsoft.com//library/dd460717.aspx) di .NET, ad esempio, creare centinaia di attività di lettura o scrittura in Azure Cosmos DB.

5. **Abilita rete accelerata**

   Per ridurre la latenza e la jitter della CPU, è consigliabile che le macchine virtuali client siano abilitate per la rete accelerata. Vedere gli articoli [creare una macchina virtuale Windows con rete accelerata](../virtual-network/create-vm-accelerated-networking-powershell.md) o [creare una macchina virtuale Linux con rete](../virtual-network/create-vm-accelerated-networking-cli.md) accelerata per abilitare la rete accelerata.


## <a name="sdk-usage"></a>Uso dell'SDK
1. **Installare l'SDK più recente**

    Agli SDK di Azure Cosmos DB vengono apportati continui miglioramenti per offrire prestazioni ottimali. Per determinare la versione di SDK più recente e verificare i miglioramenti, vedere le pagine relative agli [SDK di Azure Cosmos DB](sql-api-sdk-dotnet-standard.md).
2. **Usare un client Azure Cosmos DB singleton per la durata dell'applicazione**

    Ogni istanza di DocumentClient è thread-safe ed esegue la gestione efficiente delle connessioni e la memorizzazione nella cache degli indirizzi quando viene usata la modalità diretta. Per consentire una gestione efficiente delle connessioni e prestazioni migliori da parte di DocumentClient, è consigliabile usare una singola istanza di DocumentClient per ogni AppDomain per la durata dell'applicazione.

   <a id="max-connection"></a>
3. **Aumentare il valore di System.Net MaxConnections per host se si usa la modalità Gateway**.

    Quando si usa la modalità Gateway, le richieste di Azure Cosmos DB vengono eseguite su HTTPS/REST e sono soggette ai limiti di connessione predefiniti per ogni nome host o indirizzo IP. Può essere necessario impostare MaxConnections su un valore più alto (100-1000) in modo che la libreria client possa usare più connessioni simultanee a Azure Cosmos DB. In .NET SDK 1.8.0 e versioni successive il valore predefinito per [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) è 50 e per modificare il valore è possibile impostare [Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) su un valore più elevato.   
4. **Ottimizzazione delle query parallele per le raccolte partizionate**

     SQL .NET SDK versione 1.9.0 e versioni successive supportano le query parallele, che consentono di eseguire query su una raccolta partizionata in parallelo. Per altre informazioni, vedere [esempi di codice](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) correlati all'utilizzo con gli SDK. Le query parallele sono state concepite per migliorare la velocità e la latenza delle query sulle loro controparti seriali. Mettono a disposizione due parametri che gli utenti possono ottimizzare in funzione dei requisiti, ovvero (a) MaxDegreeOfParallelism per definire il numero massimo di partizioni sulle quali è possibile eseguire query in parallelo e (b) MaxBufferedItemCount per definire il numero di risultati di prelettura.

    (a) ***Ottimizzazione di MaxDegreeOfParallelism\:*** la query parallela funziona eseguendo query su più partizioni in parallelo. I dati di una singola raccolta partizionata vengono recuperati in modo seriale per quanto riguarda la query. L'impostazione di MaxDegreeOfParallelism sul numero di partizioni offre quindi la massima probabilità di ottenere la query più efficiente, se tutte le altre condizioni del sistema rimangono invariate. Se non si conosce il numero di partizioni, è possibile impostare il valore di MaxDegreeOfParallelism su un numero elevato; il sistema sceglie il numero minimo (numero di partizioni, input specificato dall'utente) come valore di MaxDegreeOfParallelism.

    È importante notare che le query parallele producono i vantaggi migliori se i dati sono distribuiti uniformemente tra tutte le partizioni per quanto riguarda la query. Se la raccolta è partizionata in modo tale che tutti o la maggior parte dei dati restituiti da una query siano concentrati in alcune partizioni (una sola partizione nel peggiore dei casi), le prestazioni della query potrebbero essere limitate da tali partizioni.

    (b) ***Ottimizzazione di MaxBufferedItemCount\:*** la query parallela è progettata per la prelettura dei risultati mentre il client elabora il batch di risultati corrente. La prelettura consente il miglioramento complessivo della latenza di una query. MaxBufferedItemCount è il parametro che consente di limitare il numero di risultati di prelettura. L'impostazione di MaxBufferedItemCount sul numero previsto di risultati restituiti (o un numero più alto) consente alla query di ottenere il massimo vantaggio dalla prelettura.

    La prelettura funziona allo stesso modo indipendentemente dall'impostazione di MaxDegreeOfParallelism e dalla presenza di un solo buffer per i dati di tutte le partizioni.  
5. **Attivare GC sul lato server**

    In alcuni casi, può essere utile ridurre la frequenza di Garbage Collection. In .NET impostare [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) su true.
6. **Implementare il backoff in corrispondenza di intervalli RetryAfter**

    Durante il test delle prestazioni, è necessario aumentare il carico fino a limitare un numero ridotto di richieste. Se limitata, l'applicazione client deve eseguire il backoff sulla limitazione per l'intervallo tra tentativi specificato dal server. Rispettando il backoff si garantiscono tempi di attesa minimi tra i tentativi. Il supporto dei criteri di ripetizione dei tentativi è incluso nella versione 1.8.0 e versioni successive degli SDK [.NET](sql-api-sdk-dotnet.md) e [Java](sql-api-sdk-java.md), nella versione 1.9.0 e versioni successive degli SDK [Node.js](sql-api-sdk-node.md) e [Python](sql-api-sdk-python.md) e in tutte le versioni supportate degli SDK [.NET Core](sql-api-sdk-dotnet-core.md) di SQL. Per altre informazioni, [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
    Con la versione 1.19 e successive di .NET SDK c'è un meccanismo per registrare le informazioni di diagnostica aggiuntive e risolvere i problemi di latenza come mostrato nell'esempio seguente. È possibile registrare la stringa di diagnostica per le richieste che hanno una latenza di lettura più elevata. La stringa di diagnostica acquisita consente di capire il numero di volte che è stato osservato l'errore 429 per una determinata richiesta.
    ```csharp
    ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
    readDocument.RequestDiagnosticsString 
    ```
    
7. **Aumentare il carico di lavoro client**

    Se si sta eseguendo il test a livelli di velocità effettiva elevati (> 50.000 UR/sec), l'applicazione client può diventare un collo di bottiglia a causa della limitazione di utilizzo della CPU o della rete. In questo caso, è possibile continuare a effettuare il push dell'account Azure Cosmos DB aumentando il numero di istanze delle applicazioni client in più server.
8. **Memorizzare nella cache gli URI dei documenti per una minore latenza di lettura**

    Memorizzare nella cache gli URI dei documenti quando possibile per ottenere prestazioni di lettura ottimali. È necessario definire la logica per memorizzare nella cache il valore ResourceId quando si crea la risorsa. Le ricerche basate sul valore ResourceId sono più veloci rispetto alle ricerche basate sul nome, per cui la memorizzazione nella cache di questi valori migliora le prestazioni. 

   <a id="tune-page-size"></a>
1. **Modificare le dimensioni di pagina per le query o i feed di lettura per ottenere prestazioni migliori**

   Quando si esegue una lettura in blocco di documenti usando la funzionalità dei feed di lettura, ad esempio ReadDocumentFeedAsync, oppure quando si emette una query SQL, i risultati vengono restituiti in modo segmentato se il set di risultati è troppo grande. Per impostazione predefinita, i risultati vengono restituiti in blocchi di 100 elementi o 1 MB, a seconda del limite che viene raggiunto prima.

   Per ridurre il numero di round trip di rete necessari per recuperare tutti i risultati applicabili, è possibile aumentare le dimensioni di pagina usando l'intestazione di richiesta [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) fino a 1000. Nei casi in cui è necessario visualizzare solo alcuni risultati, ad esempio se l'interfaccia utente o l'API dell'applicazione restituisce solo 10 risultati alla volta, è anche possibile ridurre le dimensioni di pagina a 10 in modo da ridurre la velocità effettiva usata per le letture e le query.

   > [!NOTE] 
   > La proprietà maxItemCount non deve essere usata solo per finalità di impaginazione. Si tratta di un utilizzo principale per migliorare le prestazioni delle query riducendo il numero massimo di elementi restituiti in una singola pagina.  

   È anche possibile impostare le dimensioni della pagina usando gli SDK Azure Cosmos DB disponibili. La proprietà [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet) in FeedOptions consente di impostare il numero massimo di elementi da restituire nell'operazione di applicazione. Quando `maxItemCount` è impostato su-1, l'SDK rileva automaticamente il valore ottimale a seconda delle dimensioni del documento. Ad esempio:
    
   ```csharp
    IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
   ```
    
   Quando viene eseguita una query, i dati risultanti vengono inviati all'interno di un pacchetto TCP. Se si specifica un valore troppo basso `maxItemCount`per, il numero di corse necessarie per inviare i dati all'interno del pacchetto TCP è elevato, che influisca negativamente sulle prestazioni. Se non si è certi del valore da impostare per `maxItemCount` la proprietà, è consigliabile impostarlo su-1 e consentire all'SDK di scegliere il valore predefinito. 

10. **Aumentare il numero di thread/attività**

    Vedere [Aumentare il numero di thread/attività](#increase-threads) nella sezione Rete.

11. **Usare processi host a 64 bit**

    L'SDK di SQL funziona in un processo host a 32 bit quando si usa SQL .NET SDK versione 1.11.4 e versioni successive. Tuttavia, se si usano query tra partizioni, è consigliabile usare l'elaborazione dell'host a 64 bit per migliorare le prestazioni. I tipi seguenti di applicazioni dispongono di un processo host a 32 bit per impostazione predefinita; per passare a un processo a 64 bit, seguire questi passaggi in base al tipo di applicazione:

    - Per le applicazioni eseguibili è possibile, a tale scopo, deselezionare l'opzione **Preferisci 32 bit** nella scheda **Compila** della finestra **Proprietà progetto**.

    - Per progetti di test basati su VSTest questa operazione può essere eseguita selezionando **Test**->**Impostazioni test**->**Default Processor Architecture as X64** (Imposta architettura processore X64 come predefinita) dall'opzione **Visual Studio Test**.

    - Per le applicazioni Web ASP.NET distribuite in locale questa operazione può essere eseguita selezionando **Utilizzare la versione a 64 bit di IIS Express per progetti e siti Web** in **Strumenti**->**Opzioni**->**Progetti e soluzioni**->**Progetti Web**.

    - Per le applicazioni Web ASP.NET distribuite in Azure questa operazione può essere eseguita selezionando **Platform as 64-bit** (Piattaforma 64 bit) in **Impostazioni applicazione** nel Portale di Azure.

## <a name="indexing-policy"></a>Criteri di indicizzazione
 
1. **Escludere i percorsi non usati dall'indicizzazione per scritture più veloci**

    I criteri di indicizzazione di Cosmos DB consentono anche di specificare i percorsi dei documenti da includere o escludere dall'indicizzazione sfruttando i percorsi di indicizzazione (IndexingPolicy.IncludedPaths e IndexingPolicy.ExcludedPaths). L'uso dei percorsi di indicizzazione può consentire di ottenere prestazioni migliori e di ridurre le risorse di archiviazione dell'indice per gli scenari in cui i modelli di query sono noti in anticipo, poiché i costi dell'indicizzazione sono correlati direttamente al numero di percorsi univoci indicizzati.  Il codice seguente, ad esempio, illustra come escludere un'intera sezione dei documenti (un sottoalbero) dall'indicizzazione usando il carattere jolly "*".

    ```csharp
    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
    ```

    Per altre informazioni, vedere l'articolo relativo ai [criteri di indicizzazione di Azure Cosmos DB](index-policy.md).

## <a name="throughput"></a>Velocità effettiva
<a id="measure-rus"></a>

1. **Misurare e ottimizzare per ottenere un utilizzo minore di unità richiesta al secondo**

    Azure Cosmos DB offre un'ampia gamma di operazioni di database, incluse le query relazionali e gerarchiche con funzioni definite dall'utente, stored procedure e trigger, operative nei documenti in una raccolta di database. Il costo associato a ognuna di queste operazioni dipende da CPU, I/O e memoria necessari per il completamento dell'operazione. Invece di occuparsi della pianificazione e della gestione delle risorse hardware, sarà possibile usare un'unità di richiesta come misura singola per le risorse necessarie per eseguire diverse operazioni di database e rispondere a una richiesta dell'applicazione.

    Viene eseguito il provisioning della velocità effettiva in base al numero di [unità richiesta](request-units.md) impostato per ogni contenitore. Il consumo delle unità di richiesta è valutato in base alla frequenza al secondo. Le applicazioni che superano la frequenza di unità richiesta con provisioning previsto per il contenitore sono limitate fino al ritorno della frequenza sotto il valore riservato per il contenitore. Se l'applicazione necessita di un livello superiore di velocità effettiva, sarà possibile aumentare la velocità effettiva eseguendo il provisioning di unità di richiesta aggiuntive. 

    La complessità di una query influisce sulla quantità di unità richiesta utilizzate per un'operazione. Il numero di predicati, la natura dei predicati, il numero di funzioni definite dall'utente e le dimensioni del set di dati di origine sono tutti fattori che incidono sul costo delle operazioni di query.

    Per misurare l'overhead di qualsiasi operazione (create, Update o DELETE), esaminare l'intestazione [x-ms-request-charge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (o la proprietà RequestCharge equivalente in ResourceResponse\<t > o FeedResponse\<t > in .NET SDK) per misurare il numero di unità richiesta utilizzate da queste operazioni.

    ```csharp
    // Measure the performance (request units) of writes
    ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
    Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
    // Measure the performance (request units) of queries
    IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
    while (queryable.HasMoreResults)
         {
              FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
              Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
         }
    ```             

    L'addebito richiesta restituito in questa intestazione è una frazione della velocità effettiva con provisioning, ad esempio 2000 UR/secondo. Se, ad esempio, la query precedente restituisce 1000 documenti da 1 KB, il costo dell'operazione è 1000. Entro un secondo, il server rispetterà quindi solo due richieste di questo tipo prima di limitare la velocità delle richieste successive. Per altre informazioni, vedere [Unità richiesta](request-units.md) e il [calcolatore di unità richiesta](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Gestire la limitazione della frequenza o una frequenza di richieste troppo elevata**

    Quando un client prova a superare la velocità effettiva riservata per un account, non si verifica alcun calo delle prestazioni del server e l'uso della capacità della velocità effettiva non supera il livello riservato. Il server termina preventivamente la richiesta con RequestRateTooLarge (codice di stato HTTP 429) e restituisce l'intestazione [x-ms-retry-after-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers), che indica la quantità di tempo, in millisecondi, che l'utente deve attendere prima di eseguire di nuovo la richiesta.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Tutti gli SDK intercettano implicitamente questa risposta, rispettano l'intestazione retry-after specificata dal server e ripetono la richiesta. A meno che all'account non accedano contemporaneamente più client, il tentativo successivo riuscirà.

    Se più client operano collettivamente in modo costante al di sopra della frequenza delle richieste, il numero di ripetizioni dei tentativi predefinito attualmente impostato su 9 internamente dal client potrebbe non essere sufficiente. In questo caso, il client genererà una DocumentClientException con codice di stato 429 per l'applicazione. Il numero di ripetizioni dei tentativi predefinito può essere modificato impostando RetryOptions nell'istanza di ConnectionPolicy. Per impostazione predefinita, l'eccezione DocumentClientException con codice di stato 429 viene restituita dopo un tempo di attesa cumulativo di 30 secondi, se la richiesta continua a funzionare al di sopra della frequenza delle richieste. Ciò si verifica anche quando il numero di ripetizioni dei tentativi corrente è inferiore al numero massimo di tentativi, indipendentemente dal fatto che si tratti del valore predefinito 9 o di un valore definito dall'utente.

    Benché il comportamento automatizzato per la ripetizione dei tentativi consenta di migliorare la resilienza e l'usabilità per la maggior parte delle applicazioni, è possibile che provochi conflitti durante l'esecuzione dei benchmark delle prestazioni, in particolare durante la misurazione della latenza.  La latenza osservata dal client presenterà dei picchi se l'esperimento raggiunge il limite del server e fa in modo che l'SDK client ripeta automaticamente i tentativi. Per evitare i picchi di latenza durante gli esperimenti relativi alle prestazioni, misurare l'addebito restituito da ogni operazione e assicurarsi che le richieste operino al di sotto della frequenza delle richieste riservata. Per altre informazioni, vedere [Unità richiesta](request-units.md).
3. **Progettare documenti di dimensioni minori per ottenere una velocità effettiva maggiore**

    L'addebito per le richieste, ovvero il costo di elaborazione delle richieste, per un'operazione specifica è correlato direttamente alle dimensioni del documento. Le operazioni sui documenti di grandi dimensioni sono più costose rispetto alle operazioni per i documenti di piccole dimensioni.

## <a name="next-steps"></a>Passaggi successivi
Per un'applicazione di esempio usata per valutare Azure Cosmos DB per scenari a prestazioni elevate su un numero ridotto di computer client, vedere [Test delle prestazioni e della scalabilità con Azure Cosmos DB](performance-testing.md).

Per altre informazioni sulla progettazione dell'applicazione per scalabilità e prestazioni elevate, vedere l'articolo relativo a [partizionamento e scalabilità in Azure Cosmos DB](partition-data.md).
