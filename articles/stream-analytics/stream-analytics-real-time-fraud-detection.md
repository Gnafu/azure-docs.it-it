---
title: Rilevamento di illeciti in tempo reale con Analisi di flusso di Azure
description: Informazioni su come creare una soluzione per il rilevamento di illeciti in tempo reale con Analisi di flusso. Utilizzare un hub eventi per l'elaborazione di eventi in tempo reale.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 19c9448b6a743302eb81bb208444336d6435f114
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947050"
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Introduzione all'uso di Analisi di flusso di Azure: Rilevamento delle frodi in tempo reale

Questa esercitazione offre una spiegazione completa sull'uso di Analisi di flusso di Azure. Si apprenderà come: 

* Introdurre eventi di streaming in un'istanza degli hub eventi di Azure. In questa esercitazione si userà un'app che simula un flusso di record di metadati di un telefono cellulare.

* Scrivere query di Analisi di flusso simili a SQL per trasformare i dati, aggregando informazioni o cercando modelli. Viene illustrato come usare una query per esaminare il flusso in ingresso e individuare chiamate potenzialmente fraudolente.

* Inviare i risultati a un sink di output (archiviazione) che è possibile analizzare per dettagli aggiuntivi. In questo caso i dati della chiamata sospetta verranno inviati ad Archiviazione BLOB di Azure.

In questa esercitazione si userà l'esempio di rilevamento delle frodi in tempo reale in base ai dati della chiamata telefonica. La tecnica che verrà illustrata è applicabile anche ad altri tipi di rilevamenti di frodi, ad esempio quelle relative alle carte di credito o al furto di identità. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Scenario: telecomunicazioni e rilevamento di illecito relativo alle SIM in tempo reale

Un'azienda di telecomunicazioni dispone di un volume di dati elevato relativamente alle chiamate in ingresso. L'azienda intende rilevare chiamate fraudolente in tempo reale, in modo da poter inviare una notifica ai clienti o arrestare il servizio per un numero specifico. Un tipo di frode SIM riguarda più chiamate dalla stessa identità, più o meno alla stessa ora, ma in luoghi geograficamente diversi. Per rilevare questo tipo di illecito, è necessario che l'azienda esamini i record del telefono in arrivo e cerchi modelli specifici, in questo caso, per le chiamate eseguite nello stesso momento in paesi o aree geografiche diverse. Qualsiasi record telefonico che rientri in questa categoria viene scritto nell'archiviazione per analisi successive.

## <a name="prerequisites"></a>Prerequisiti

In questa esercitazione si simuleranno i dati di una chiamata telefonica usando un'app client che genera metadati di esempio di chiamate. Alcuni record prodotti dall'app hanno l'aspetto di chiamate fraudolente. 

Prima di iniziare, verificare di disporre degli elementi seguenti:

* Un account Azure.
* App generatore degli eventi di chiamata, [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip), che può essere scaricata dal Microsoft Download Center. Decomprimere il pacchetto in una cartella nel computer. Per visualizzare il codice sorgente ed eseguire l'app in un debugger, è possibile ottenere il codice sorgente dell'app da [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator). 

    >[!NOTE]
    >Windows potrebbe bloccare il file con estensione zip scaricato. Se non è possibile decomprimerlo, fare clic con il pulsante destro del mouse sul file e scegliere **Proprietà**. Se viene visualizzato il messaggio "Il file proviene da un altro computer. Per facilitare la protezione del computer, potrebbe essere bloccato", selezionare l'opzione **Sblocca** e quindi scegliere **Applica**.

Per esaminare i risultati del processo di Analisi di flusso, è anche necessario uno strumento per la visualizzazione del contenuto di un contenitore di Archiviazione BLOB di Azure. Se si usa Visual Studio, è possibile usare [Strumenti di Microsoft Azure per Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) o [Visual Studio Cloud Explorer](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer). In alternativa, è possibile installare strumenti autonomi come [Azure Storage Explorer](https://storageexplorer.com/) o [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>Creare hub eventi di Azure per inserire eventi

Per analizzare un flusso di dati, *inserirlo* in Azure. Un modo comune per inserire i dati consiste nell'usare gli [hub eventi di Azure](../event-hubs/event-hubs-what-is-event-hubs.md), che consentono di inserire milioni di eventi al secondo e quindi elaborare e archiviare le relative informazioni. Per questa esercitazione si creerà un hub eventi e quindi l'app generatore degli eventi di chiamata invierà i dati a tale hub. Per altre informazioni sugli hub eventi, vedere la [documentazione del bus di servizio di Azure](https://docs.microsoft.com/azure/service-bus/).

>[!NOTE]
>Per una versione più dettagliata di questa procedura, vedere [Creare uno spazio dei nomi di Hub eventi e un hub eventi usando il Portale di Azure](../event-hubs/event-hubs-create.md). 

### <a name="create-a-namespace-and-event-hub"></a>Creare uno spazio dei nomi e un hub eventi
In questa procedura si creerà uno spazio dei nomi dell'hub eventi e quindi si aggiungerà un hub eventi a tale spazio. Gli spazi dei nomi degli hub eventi consentono di raggruppare in modo logico le istanze dei bus di eventi correlate. 

1. Accedere al portale di Azure e fare clic su **Crea una risorsa** > **Internet delle cose** > **Hub eventi**. 

2. Nel riquadro **Crea spazio dei nomi** immettere un nome per lo spazio dei nomi, ad esempio `<yourname>-eh-ns-demo`. È possibile usare qualsiasi nome per lo spazio dei nomi, a condizione che sia valido per un URL e univoco in Azure. 
    
3. Selezionare una sottoscrizione, creare o scegliere un gruppo di risorse e quindi fare clic su **Crea**.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png" alt="Create event hub namespace in Azure portal" width="300px"/>

4. Al termine della distribuzione, lo spazio dei nomi dell'hub eventi è disponibile nell'elenco delle risorse di Azure. 

5. Scegliere il nuovo spazio dei nomi e, nel relativo riquadro, fare clic su **Hub eventi**.

   ![Pulsante Aggiungi hub eventi per creare un nuovo hub eventi](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
6. Assegnare il nome `asa-eh-frauddetection-demo` al nuovo hub eventi. È possibile usare un nome diverso. In questo caso, tenerne traccia, poiché sarà necessario in un secondo momento. Per ora non è necessario impostare altre opzioni per l'hub eventi.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png" alt="Name event hub in Azure portal" width="400px"/>
    
 
7. Fare clic su **Create**(Crea).

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Concedere l'accesso all'hub eventi e ottenere una stringa di connessione

Prima che un processo possa inviare dati a un hub eventi, è necessario che per l'hub siano configurati criteri che consentano l'accesso appropriato. I criteri di accesso generano una stringa di connessione che include informazioni di autorizzazione.

1.  Nel riquadro dello spazio dei nomi dell'evento fare clic su **Hub eventi** e quindi sul nome del nuovo hub eventi.

2.  Nel riquadro dell'hub eventi fare clic su **Criteri di accesso condiviso** e quindi su **+&nbsp;Aggiungi**.

    >[!NOTE]
    >Verificare di usare l'hub eventi e non lo spazio dei nomi.

3.  Aggiungere criteri denominati `sa-policy-manage-demo` e per **Attestazione** selezionare **Gestisci**.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png" alt="Create shared access policy for Stream Analytics" width="300px"/>
 
4.  Fare clic su **Create**(Crea).

5.  Dopo aver distribuito i criteri, fare clic nell'elenco dei criteri di accesso condiviso.

6.  Individuare la casella con l'etichetta **CONNECTION STRING-PRIMARY KEY** e fare clic sul pulsante Copia accanto alla stringa di connessione. 

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png" alt="Stream Analytics shared access policy" width="300px"/>
 
7.  Incollare la stringa di connessione in un editor di testo. Sarà necessario usare questa stringa nella sezione successiva, dopo avervi apportato alcune piccole modifiche.

    La stringa di connessione ha un aspetto simile al seguente:

        Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=asa-eh-frauddetection-demo

    Si noti che la stringa di connessione contiene più coppie chiave-valore, separate da punti e virgola: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey` e `EntityPath`.  

## <a name="configure-and-start-the-event-generator-application"></a>Configurare e avviare l'applicazione di generazione di eventi

Prima di avviare l'app TelcoGenerator è necessario configurarla per l'invio di record delle chiamate all'hub eventi creato.

### <a name="configure-the-telcogenerator-app"></a>Configurare l'app TelcoGenerator

1. Nell'editor in cui è stata copiata la stringa di connessione annotare il valore `EntityPath` e quindi rimuovere la coppia `EntityPath` (non dimenticare di rimuovere il punto e virgola che la precede). 

2. Nella cartella in cui è stato decompresso il file TelcoGenerator.zip aprire il file telcodatagen.exe.config in un editor. È presente più di un file con estensione config, di conseguenza è necessario assicurarsi di aprire quello giusto.

3. Nell'elemento `<appSettings>`:

   * Impostare il valore della chiave `EventHubName` sul nome dell'hub eventi (ovvero il valore del percorso dell'entità).
   * Impostare il valore della chiave `Microsoft.ServiceBus.ConnectionString` sulla stringa di connessione. 

   La sezione `<appSettings>` dovrebbe essere simile all'esempio seguente. Per maggiore chiarezza è stato eseguito il wrapping delle righe e alcuni caratteri sono stati rimossi dal token di autorizzazione.

   ![File di configurazione TelcoGenerator con la stringa di connessione e il nome dell'hub eventi](./media/stream-analytics-real-time-fraud-detection/stream-analytics-telcogenerator-config-file-app-settings.png)
 
4. Salvare il file. 

### <a name="start-the-app"></a>Avviare l'app
1.  Aprire una finestra di comando e passare alla cartella in cui è stata decompressa l'app TelcoGenerator.

2.  Immettere il comando seguente:

   ```cmd
   telcodatagen.exe 1000 0.2 2
   ```

   I parametri sono i seguenti: 

   * Numero di CDR all'ora. 
   * Probabilità di frode della scheda SIM: frequenza, in percentuale di tutte le chiamate, con cui l'app dovrebbe simulare una chiamata fraudolenta. Il valore 0,2 significa che circa il 20% dei record delle chiamate apparirà come fraudolento.
   * Durata in ore. Numero di ore di esecuzione dell'app. È anche possibile arrestare l'app in qualsiasi momento premendo CTRL+C nella riga di comando.

   Dopo alcuni secondi, l'app inizierà a visualizzare i record delle chiamate mentre ne esegue l'invio all'hub eventi.

Di seguito sono riportati alcuni campi chiave che verranno usati in questa applicazione di rilevamento di frodi in tempo reale:

|**Record**|**Definizione**|
|----------|--------------|
|`CallrecTime`|Timestamp dell'ora di inizio della chiamata. |
|`SwitchNum`|Commutatore telefonico usato per la connessione della chiamata. Per questo esempio i commutatori sono stringhe che rappresentano il paese/area di origine (Stati Uniti, Cina, Regno Unito, Germania o Australia). |
|`CallingNum`|Numero di telefono del chiamante. |
|`CallingIMSI`|Codice IMSI (International Mobile Subscriber Identity). Si tratta dell'identificatore univoco del chiamante. |
|`CalledNum`|Numero di telefono del destinatario della chiamata. |
|`CalledIMSI`|Codice IMSI (International Mobile Subscriber Identity). Si tratta dell'identificatore univoco del destinatario della chiamata. |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>Creare un processo di Analisi di flusso per gestire i dati di streaming

Dopo aver creato un flusso di eventi di chiamata, sarà possibile configurare un processo di Analisi di flusso. Il processo leggerà i dati dall'hub eventi configurato. 

### <a name="create-the-job"></a>Creare il processo 

1. Nel portale di Azure fare clic su **Crea una risorsa** > **Internet delle cose** > **Processo di Analisi di flusso**.

2. Denominare il processo `asa_frauddetection_job_demo`, specificare una sottoscrizione, un gruppo di risorse e un percorso.

    È consigliabile posizionare il processo e l'hub eventi nella stessa area per ottenere prestazioni ottimali ed evitare di pagare il trasferimento dei dati tra aree.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png" alt="Create Stream Analytics job in portal" width="300px"/>

3. Fare clic su **Create**(Crea).

    Verrà creato il processo e il portale visualizzerà i relativi dettagli. Il processo non è ancora in esecuzione: è necessario configurarlo prima di poterlo avviare.

### <a name="configure-job-input"></a>Configurare l'input del processo

1. Nel dashboard o nel riquadro **Tutte le risorse** trovare e selezionare il processo di Analisi di flusso `asa_frauddetection_job_demo`. 
2. Nella sezione **Panoramica** del riquadro del processo di Analisi di flusso selezionare la casella **Input**.

   ![Casella di input in Topologia nel riquadro del processo di Analisi di flusso](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. Fare clic su **Aggiungi input del flusso** e selezionare **Hub eventi**. Compilare quindi la nuova pagina di input con le informazioni seguenti:

   |**Impostazione**  |**Valore consigliato**  |**Descrizione**  |
   |---------|---------|---------|
   |Alias di input  |  CallStream   |  Immettere un nome per identificare l'input del processo.   |
   |Sottoscrizione   |  \<Sottoscrizione in uso\> |  Selezionare la sottoscrizione di Azure in cui è stato creato l'hub eventi.   |
   |Spazio dei nomi dell'hub eventi  |  asa-eh-ns-demo |  Inserire il nome dello spazio dei nomi dell'hub eventi.   |
   |Nome dell'hub eventi  | asa-eh-frauddetection-demo | Selezionare il nome dell'hub eventi.   |
   |Nome criteri hub eventi  | asa-policy-manage-demo | Selezionare i criteri di accesso creati in precedenza.   |

    </br>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png" alt="Create Stream Analytics input in portal" width="300px"/>


4. Fare clic su **Create**(Crea).

## <a name="create-queries-to-transform-real-time-data"></a>Creare query per trasformare i dati in tempo reale

A questo punto il processo di Analisi di flusso è configurato per la lettura di un flusso di dati in ingresso. Il passaggio successivo consiste nel creare una query che analizzi i dati in tempo reale. Analisi di flusso supporta un semplice modello di query dichiarative che descrive le trasformazioni per l'elaborazione in tempo reale. Le query usano un linguaggio simile a SQL che include alcune estensioni specifiche di Analisi di flusso. 

Una query semplice potrebbe solamente leggere tutti i dati in ingresso. Tuttavia, spesso si creano query che cercano dati specifici o relazioni nei dati. In questa sezione dell'esercitazione si creeranno e testeranno varie query per apprendere alcuni modi in cui trasformare un flusso di input per l'analisi. 

Le query qui create visualizzeranno semplicemente i dati trasformati. In una sezione successiva si configureranno un sink di output e una query che scrive in tale sink i dati trasformati.

Per altre informazioni sul linguaggio, vedere le [Informazioni di riferimento sul linguaggio di query di analisi dei flussi di Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

### <a name="get-sample-data-for-testing-queries"></a>Ottenere dati di esempio per testare le query

L'app TelcoGenerator invia i record delle chiamate all'hub eventi e il processo di Analisi di flusso viene configurato per la lettura dall'hub eventi. È possibile usare una query per testare il processo e verificare che esegua correttamente le operazioni di lettura. Per testare una query nella console di Azure sono necessari dati di esempio. Per questa procedura dettagliata si estrarranno dati di esempio dal flusso in arrivo nell'hub eventi.

1. Verificare che l'app TelcoGenerator sia in esecuzione e generi record delle chiamate.
2. Nel portale tornare al riquadro del processo di Analisi di flusso. Se il riquadro è stato chiuso, cercare `asa_frauddetection_job_demo` nel riquadro **Tutte le risorse**.
3. Fare clic sulla casella **Query**. Azure elenca gli input e gli output configurati per il processo e consente di creare una query per trasformare il flusso di input nel momento in cui viene inviato all'output.
4. Nel riquadro **Query** fare clic sui puntini di sospensione accanto all'input `CallStream` e quindi selezionare **Campiona dati da input**.

   ![Voci di menu per usare dati di esempio per la voce del processo di Analisi di flusso, con l'opzione "Campiona dati da input" selezionata](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)


5. Impostare **Minuti** su 3 e quindi fare clic su **OK**. 
    
   ![Opzioni per il campionamento del flusso di input, con l'opzione 3 minuti selezionata](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    Azure campiona i dati dal flusso di input per una durata di tre minuti e invia una notifica quando i dati di esempio sono pronti. Questa operazione richiede un breve tempo. 

I dati di esempio vengono archiviati temporaneamente e sono disponibili finché rimane aperta la finestra di query. Se si chiude la finestra di query, i dati di esempio verranno rimossi e sarà necessario creare un nuovo set di dati. 

In alternativa, è possibile ottenere un file con estensione json che contiene dati di esempio [da GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) e quindi caricare il file con estensione json da usare come dati di esempio per l'input `CallStream`. 

### <a name="test-using-a-pass-through-query"></a>Test mediante una query pass-through

Per archiviare ogni evento, è possibile usare una query pass-through per leggere tutti i campi nel payload dell'evento.

1. Nella finestra di query immettere la query seguente:
        
   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >Come con SQL, le parole chiave non effettuano distinzione tra maiuscole e minuscole e gli spazi non sono rilevanti.

    In questa query `CallStream` è l'alias specificato quando è stato creato l'input. In presenza di un alias diverso, usare questo nome.

2. Fare clic su **Test**.

    Il processo di Analisi di flusso esegue la query a fronte dei dati di esempio e visualizza l'output nella parte inferiore della finestra, a indicare che l'hub eventi e il processo di Analisi di flusso sono configurati correttamente. Come già indicato, in seguito si creerà un sink di output in cui la query potrà scrivere dati.

   ![Output del processo di Analisi di flusso con 73 record generati](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    Il numero esatto di record visualizzati dipenderà dal numero di record acquisiti nell'esempio di 3 minuti.
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Ridurre il numero di campi usando una proiezione di colonna

In molti casi, l'analisi non necessita di tutte le colonne dal flusso di input. È possibile usare una query per proiettare un set di campi restituiti ridotto rispetto alla query pass-through.

1. Modificare la query nell'editor di codice nel modo seguente:

   ```SQL
   SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
   FROM 
       CallStream
   ```

2. Fare di nuovo clic su **Test**. 

   ![Output del processo di Analisi di flusso per la proiezione con 25 record](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Numero di chiamate in ingresso per area: finestra a cascata con aggregazione

Si supponga di voler contare il numero di chiamate in ingresso per ogni area. Nei dati di streaming, se si vuole eseguire funzioni di aggregazione come il conteggio, è necessario segmentare il flusso in unità temporali (poiché il flusso di dati stesso è in effetti infinito). A tale scopo, usare una [funzione finestra](stream-analytics-window-functions.md) di Analisi di flusso. Sarà quindi possibile usare i dati all'interno di tale finestra come unità.

Per questa trasformazione si intende creare una sequenza di finestre temporali che non si sovrappongano. Ogni finestra includerà un set distinto di dati che è possibile raggruppare e aggregare. A questo tipo di finestra si fa riferimento con la locuzione *finestra a cascata*. All'interno della finestra a cascata è possibile ottenere un conteggio delle chiamate in ingresso raggruppate per `SwitchNum`, che rappresenta il paese/area geografica in cui ha avuto origine la chiamata. 

1. Modificare la query nell'editor di codice nel modo seguente:

        ```SQL
        SELECT 
            System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
        FROM
            CallStream TIMESTAMP BY CallRecTime 
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
        ```

    Questa query usa la parola chiave `Timestamp By` nella clausola `FROM` per specificare il campo del timestamp nel flusso di input da usare per definire la finestra a cascata. In questo caso la finestra divide i dati in segmenti per il campo `CallRecTime` in ogni record. Se non è specificato alcun campo, l'operazione di windowing usa l'ora in cui ogni evento arriva all'hub. Vedere la sezione relativa a tempo di arrivo e tempo di applicazione in [Informazioni di riferimento sul linguaggio di query per l'analisi di flusso](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). 

    La proiezione include `System.Timestamp`, che restituisce un timestamp per la fine di ogni finestra. 

    Per specificare che si desidera utilizzare una finestra a cascata, utilizzare la funzione [TUMBLINGWINDOW](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) nella `GROUP BY` clausola. Specificare nella funzione un'unità di tempo (da un microsecondo a un giorno) e una dimensione della finestra (numero di unità). In questo esempio, la finestra a cascata è costituita da intervalli di 5 secondi, quindi si otterrà un conteggio per paese/area geografica ogni 5 secondi per le chiamate.

2. Fare di nuovo clic su **Test**. Si noti nei risultati che i timestamp in **WindowEnd** sono in incrementi di 5 secondi.

   ![Output del processo di Analisi di flusso per l'aggregazione con 13 record](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>Rilevare una frode SIM mediante self-join

Per questo esempio, considerare come uso fraudolento le chiamate che provengono dallo stesso utente ma in posizioni diverse, a 5 secondi l'una dall'altra. Ad esempio, lo stesso utente non può eseguire legittimamente una chiamata dagli Stati Uniti e dall'Australia nello stesso momento. 

Per verificare questi casi, è possibile usare un self-join dei dati di streaming per creare un join del flusso a se stesso in base al valore `CallRecTime`. È quindi possibile cercare i record delle chiamate in `CallingIMSI` cui il valore (numero di origine) è lo stesso, ma `SwitchNum` il valore (paese/regione di origine) non è lo stesso.

Quando si usa un join con i dati di streaming, il join deve garantire alcuni limiti per la distanza di separazione delle righe corrispondenti nel tempo. Come indicato in precedenza, i dati di streaming sono effettivamente infiniti. I limiti di tempo per la relazione sono specificati all'interno della clausola `ON` del join usando la funzione `DATEDIFF`. In questo caso il join è basato su un intervallo di 5 secondi di dati di chiamata.

1. Modificare la query nell'editor di codice nel modo seguente: 

        ```SQL
        SELECT  System.Timestamp as Time, 
            CS1.CallingIMSI, 
            CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, 
            CS1.SwitchNum as Switch1, 
            CS2.SwitchNum as Switch2 
        FROM CallStream CS1 TIMESTAMP BY CallRecTime 
            JOIN CallStream CS2 TIMESTAMP BY CallRecTime 
            ON CS1.CallingIMSI = CS2.CallingIMSI 
            AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5 
        WHERE CS1.SwitchNum != CS2.SwitchNum
        ```

    Questa query è simile a un join SQL, ad eccezione della funzione `DATEDIFF` nel join. Si tratta di una versione di `DATEDIFF` specifica di Analisi di flusso che deve apparire nella clausola `ON...BETWEEN`. I parametri sono un'unità di tempo (secondi in questo esempio) e gli alias delle due origini per il join. Si tratta di una differenza rispetto alla funzione `DATEDIFF` SQL standard.

    La clausola `WHERE` include la condizione che contrassegna la chiamata fraudolenta: i commutatori di origine non sono uguali. 

2. Fare di nuovo clic su **Test**. 

   ![Output del processo di Analisi di flusso per il self-join con 6 record generati](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. Fare clic su **Salva** per salvare la query di self-join nell'ambito del processo di Analisi di flusso. I dati di esempio non vengono salvati.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png" alt="Save Stream Analytics query in portal" width="300px"/>

## <a name="create-an-output-sink-to-store-transformed-data"></a>Creare un sink di output per archiviare i dati trasformati

Sono stati definiti un flusso di eventi, un input dell'hub eventi per inserire gli eventi e una query per eseguire una trasformazione nel flusso. L'ultimo passaggio consiste nel definire un sink di output per il processo, ovvero un percorso in cui scrivere il flusso trasformato. 

È possibile usare molte risorse come sink di output: un database di SQL Server, un'archiviazione tabelle, un'archiviazione Data Lake, Power BI e addirittura un altro hub eventi. Per questa esercitazione si scriverà il flusso in Archiviazione BLOB di Azure, una scelta tipica per raccogliere informazioni di eventi per l'analisi successiva, in quanto include dati non strutturati.

Se esiste già un account di archiviazione BLOB, è possibile usarlo. In questa esercitazione si apprenderà come creare un nuovo account di archiviazione.

### <a name="create-an-azure-blob-storage-account"></a>Creare un account Archiviazione BLOB di Azure

1. Nell'angolo superiore sinistro del portale di Azure selezionare **Crea risorsa** > **Archiviazione** > **Account di archiviazione**. Compilare la pagina del processo dell'account di archiviazione impostando **Nome** su "asaehstorage", **Località** su "Stati Uniti orientali", **Gruppo di risorse** su "asa-eh-ns-rg" (ospitare l'account di archiviazione nello stesso gruppo di risorse del processo di streaming per ottenere prestazioni migliori). Per le altre impostazioni è possibile lasciare i valori predefiniti.  

   ![Creare un account di archiviazione nel portale di Azure](./media/stream-analytics-real-time-fraud-detection/stream-analytics-storage-account-create.png)

2. Nel portale di Azure tornare al riquadro del processo di Analisi di flusso. Se il riquadro è stato chiuso, cercare `asa_frauddetection_job_demo` nel riquadro **Tutte le risorse**.

3. Nella sezione **Topologia processo** fare clic sulla casella **Output**.

4. Nel riquadro **Output**, fare clic su **Aggiungi**, quindi selezionare **Archiviazione BLOB**. Compilare quindi la nuova pagina di output con le informazioni seguenti:

   |**Impostazione**  |**Valore consigliato**  |**Descrizione**  |
   |---------|---------|---------|
   |Alias di output  |  CallStream-FraudulentCalls   |  Immettere un nome per identificare l'output del processo.   |
   |Sottoscrizione   |  \<Sottoscrizione in uso\> |  Selezionare la sottoscrizione di Azure che include l'account di archiviazione creato. L'account di archiviazione può essere incluso nella stessa sottoscrizione o in una diversa. Questo esempio presuppone che l'account di archiviazione sia stato creato all'interno della stessa sottoscrizione. |
   |Account di archiviazione  |  asaehstorage |  Immettere il nome dell'account di archiviazione creato. |
   |Contenitore  | asa-fraudulentcalls-demo | Scegliere Crea nuovo e immettere un nome di contenitore. |

    <br/>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png" alt="Create blob output for Stream Analytics job" width="300px"/>
    
5. Fare clic su **Save**. 


## <a name="start-the-streaming-analytics-job"></a>Avviare il processo di Analisi di flusso

Ora il processo è configurato. Sono stati specificati un input (l'hub eventi), una trasformazione (la query per individuare le chiamate fraudolente) e un output (archiviazione BLOB). È ora possibile avviare il processo. 

1. Verificare che l'app TelcoGenerator sia in esecuzione.

2. Nel riquadro del processo fare clic su **Avvia**. Nel riquadro **Avvia processo**, per Ora di inizio dell'output del processo, selezionare **Ora**. 

   ![Avviare il processo di analisi di flusso](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start.png)



## <a name="examine-the-transformed-data"></a>Esaminare i dati trasformati

È stato creato un processo di Analisi di flusso completo. Il processo esamina un flusso di metadati di chiamate telefoniche per individuare le chiamate fraudolente in tempo reale e scrivere informazioni su queste chiamate nell'archiviazione. 

Per completare questa esercitazione, è consigliabile osservare i dati acquisiti dal processo di Analisi di flusso. I dati vengono scritti in Archiviazione BLOB di Azure in blocchi (file). È possibile usare qualsiasi strumento che legga Archiviazione BLOB di Azure. Come indicato nella sezione Prerequisiti, è possibile usare le estensioni di Azure in Visual Studio oppure uno strumento come [Azure Storage Explorer](https://storageexplorer.com/) or [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

Quando si esamina il contenuto di un file nell'archiviazione BLOB, viene visualizzato quanto segue:

   ![Archiviazione BLOB di Azure con output di Analisi di flusso](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>Pulire le risorse

Sono disponibili altri articoli che approfondiscono lo scenario di rilevamento di frodi e usano le risorse create in questa esercitazione. Per procedere, vedere i suggerimenti in **Passaggi successivi**.

Se tuttavia non si desidera proseguire e le risorse create non sono necessarie, è possibile eliminarle per non incorrere in inutili addebiti da parte di Azure. In tal caso, è consigliabile procedere nel modo seguente:

1. Arrestare il processo di Analisi di flusso. Nel riquadro **Processi** fare clic su **Arresta** nella parte superiore.
2. Arrestare l'app TelcoGenerator. Nella finestra di comando in cui è stato avviata l'applicazione premere CTRL+C.
3. Se è stato creato un nuovo account di archiviazione BLOB solo per questa esercitazione, eliminarlo. 
4. Eliminare il processo di Analisi di flusso.
5. Eliminare l'hub eventi.
6. Eliminare lo spazio dei nomi dell'hub eventi.

## <a name="get-support"></a>Supporto

Per assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi

È possibile continuare questa esercitazione con l'articolo seguente:

* [Analisi di flusso e Power BI: un dashboard di analisi in tempo reale per il flusso di dati](stream-analytics-power-bi-dashboard.md). Questo articolo illustra come inviare l'output di telecomunicazioni del processo di Analisi di flusso a Power BI per l'analisi e la visualizzazione in tempo reale.

Per altre informazioni generiche su Analisi di flusso, vedere questi articoli:

* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
