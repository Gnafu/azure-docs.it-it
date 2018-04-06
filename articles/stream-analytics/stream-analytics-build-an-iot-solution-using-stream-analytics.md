---
title: Compilare una soluzione IoT con Analisi di flusso | Microsoft Docs
description: Esercitazione introduttiva per la soluzione IoT di Analisi di flusso relativa allo scenario di un casello
keywords: soluzione IOT, funzioni finestra
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/21/2018
ms.author: sngun
ms.openlocfilehash: b36833a9fe35f14eba6d9e397eb0958b716b313b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2018
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Compilare una soluzione IoT con Analisi di flusso

## <a name="introduction"></a>Introduzione
Questa esercitazione illustra come usare Analisi di flusso di Azure per acquisire informazioni approfondite in tempo reale a partire dai dati. Gli sviluppatori possono combinare facilmente flussi di dati, come clickstream, log ed eventi generati da dispositivi, con record cronologici o dati di riferimento per ottenere informazioni aziendali approfondite. Analisi di flusso di Azure è un servizio di calcolo dei flussi in tempo reale completamente gestito, ospitato in Microsoft Azure, con caratteristiche di resilienza predefinita, bassa latenza e scalabilità che consentono la piena operatività in pochi minuti.

Dopo aver completato questa esercitazione, si sarà in grado di:

* Acquisire familiarità con il portale di Analisi di flusso di Azure.
* Configurare e distribuire un processo di flusso.
* Articolare problemi reali e risolverli con il linguaggio di query di Analisi di flusso.
* Sviluppare in tutta sicurezza soluzioni di streaming per i clienti usando Analisi di flusso.
* Usare l'esperienza di monitoraggio e registrazione per risolvere i problemi.

## <a name="prerequisites"></a>prerequisiti
Per completare questa esercitazione, sono necessari i prerequisiti seguenti:

* La versione più recente di [Azure PowerShell](/powershell/azure/overview)
* Visual Studio 2017, 2015 o la versione gratuita [Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)
* Una [sottoscrizione di Azure](https://azure.microsoft.com/pricing/free-trial/)
* Privilegi di amministratore nel computer
* Scaricare il codice sorgente per l'analisi dei dati del sensore TollApp dal [repository GitHub azure-stream-analytics](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp). Questo repository contiene dati e query di esempio che verranno usati nelle sezioni successive. 

## <a name="scenario-introduction-hello-toll"></a>Presentazione dello scenario: il casello
Un casello rappresenta una situazione piuttosto comune. Se ne incontrano sulle autostrade e su molti ponti e tunnel in tutto il mondo. Ogni barriera è costituita da più caselli. In quelli manuali ci si ferma per pagare il pedaggio a un addetto. In quelli automatizzati al passaggio attraverso il casello un sensore posto al di sopra di esso analizza una scheda RFID posizionata sul parabrezza del veicolo. È semplice visualizzare il passaggio dei veicoli nei caselli come un flusso di eventi, sui quali è possibile eseguire alcune operazioni interessanti.

![Immagine di automobili ai caselli](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>Dati di ingresso
Questa esercitazione utilizza due flussi di dati. Il primo flusso viene prodotto da sensori installati all'entrata e all'uscita del casello. Il secondo flusso è un set di dati di ricerca statico contenente dati di registrazione dei veicoli.

### <a name="entry-data-stream"></a>Flusso di dati di ingresso
Il flusso di dati di ingresso contiene informazioni sulle automobili che entrano nel casello.

| ID casello | Tempo ingresso | Targa | Stato | Assicurarsi | Modello | Tipo veicolo | Peso veicolo | Casello | Tag |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

Ecco una breve descrizione delle colonne:

| Colonna | DESCRIZIONE |
| --- | --- |
| ID casello |ID casello che identifica in modo univoco un casello |
| Tempo ingresso |Data e ora (UTC) di ingresso del veicolo nel casello |
| Targa |Numero di targa del veicolo |
| Stato |Stato degli Stati Uniti |
| Assicurarsi |Il produttore dell'automobile |
| Modello |Numero di modello dell'automobile |
| Tipo veicolo |1 per autovetture o 2 per veicoli commerciali |
| Peso veicolo |Peso del veicolo in tonnellate, 0 per veicoli passeggeri |
| Casello |Il valore del pedaggio in USD |
| Tag |e-Tag sull'automobile che permette di automatizzare il pagamento, lasciato vuoto quando il pagamento viene effettuato manualmente |

### <a name="exit-data-stream"></a>Flusso di dati di uscita
Il flusso di dati di uscita contiene informazioni sulle automobili che escono dal casello.

| **ID casello** | **Tempo ingresso** | **Targa** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.0000000Z |CDE 1007 |

Ecco una breve descrizione delle colonne:

| Colonna | DESCRIZIONE |
| --- | --- |
| ID casello |ID casello che identifica in modo univoco un casello |
| Tempo ingresso |Data e ora (UTC) di uscita del veicolo dal casello |
| Targa |Numero di targa del veicolo |

### <a name="commercial-vehicle-registration-data"></a>Dati di registrazione di veicoli commerciali
Questa esercitazione usa uno snapshot statico di un database di registrazione di veicoli commerciali.

| Targa | ID registrazione | Scaduto |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Ecco una breve descrizione delle colonne:

| Colonna | DESCRIZIONE |
| --- | --- |
| Targa |Numero di targa del veicolo |
| ID registrazione |ID registrazione del veicolo |
| Scaduto |Stato di registrazione del veicolo: 0 se la registrazione del veicolo è attiva, 1 se la registrazione è scaduta |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Configurare l'ambiente per Analisi di flusso di Azure
Per completare l'esercitazione, è necessaria una sottoscrizione di Microsoft Azure. Microsoft offre la possibilità di scegliere una versione di valutazione gratuita per i servizi Microsoft Azure.

Se non si ha un account Azure, è possibile [richiedere una versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/).

> [!NOTE]
> Per l'iscrizione a una versione di valutazione gratuita, è necessario avere un dispositivo mobile che possa ricevere messaggi di testo e una carta di credito valida.
> 
> 

Per poter usare al meglio il credito Azure gratuito, seguire la procedura riportata nella sezione "Eseguire la pulizia dell'account Azure" alla fine di questo articolo.

## <a name="provision-azure-resources-required-for-the-tutorial"></a>Eseguire il provisioning delle risorse di Azure necessarie per l'esercitazione
Questa esercitazione richiede due hub eventi per ricevere i flussi di dati *in ingresso* e *in uscita*. Il database SQL di Azure restituisce i risultati dei processi di Analisi di flusso. In Archiviazione di Azure vengono memorizzati i dati di riferimento di registrazione dei veicoli.

È possibile usare lo script Setup.ps1 nella cartella TollApp in GitHub per creare tutte le risorse necessarie. Per ottimizzare i tempi, si consiglia di eseguirlo. Per altre informazioni sulla configurazione di queste risorse nel portale di Azure, vedere l'appendice relativa alla configurazione delle risorse per l'esercitazione nel portale di Azure.

Scaricare e salvare la cartella e i file di supporto di [TollApp](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TollApp/TollApp.zip) .

Aprire una finestra di **Microsoft Azure PowerShell***come amministratore*. Se ancora non si ha Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview) per istruzioni su come installarlo.

Dato che Windows blocca automaticamente i file con estensione ps1, dll ed exe, è necessario impostare i criteri di esecuzione prima di eseguire lo script. Assicurarsi che la finestra di Azure PowerShell sia in esecuzione *come amministratore*. Eseguire **Set-ExecutionPolicy unrestricted**. Quando richiesto, digitare **Y**.

![Screenshot di "Set-ExecutionPolicy unrestricted" in esecuzione nella finestra di Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

Eseguire **Get-ExecutionPolicy** per assicurarsi che il comando sia stato eseguito correttamente.

![Screenshot di "Get-ExecutionPolicy" in esecuzione nella finestra di Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

Passare alla directory contenente gli script e l'applicazione di generazione.

![Screenshot di "cd .\TollApp\TollApp" in esecuzione nella finestra di Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

Digitare **.\\Setup.ps1** per configurare l'account Azure, creare e configurare tutte le risorse necessarie e quindi avviare la generazione di eventi. Lo script seleziona in modo casuale un'area per creare le risorse. Per specificare un'area in modo esplicito, è possibile passare il parametro **-location** come nell'esempio seguente:

**.\\Setup.ps1 -location "Stati Uniti centrali"**

![Screenshot della pagina di accesso di Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

Lo script apre quindi la pagina di **accesso** a Microsoft Azure. Immettere le credenziali dell'account.

> [!NOTE]
> Se l'account ha accesso a più sottoscrizioni, viene richiesto di immettere il nome della sottoscrizione che si vuole usare per l'esercitazione.
> 
> 

L'esecuzione dello script può richiedere qualche minuto. Al termine, l'output dovrebbe avere un aspetto simile allo screenshot seguente.

![Screenshot dell'output dello script nella finestra di Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

Viene visualizzata anche un'altra finestra simile allo screenshot seguente. Questa applicazione invia eventi a Hub eventi di Azure, che è quindi necessario per eseguire l'esercitazione. Non arrestare l'applicazione o chiudere la finestra prima di aver terminato l'esercitazione.

![Screenshot dell'invio dei dati dell'hub eventi](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

A questo punto le risorse dovrebbero essere visibili nel portale di Azure. Passare a <https://portal.azure.com> e accedere con le credenziali del proprio account. Si noti che attualmente alcune funzionalità usano il portale classico. Questi passaggi saranno indicati con chiarezza.

### <a name="azure-event-hubs"></a>Hub eventi di Azure

Dal portale di Azure fare clic su **Altri servizi** in basso nel riquadro di gestione a sinistra. Digitare **hub eventi** nel campo disponibile. Verrà visualizzato un nuovo spazio dei nomi di Hub eventi che inizia con **tolldata**. Questo spazio dei nomi viene creato dallo script Setup.ps1. Vengono visualizzati due Hub eventi denominati **entry** ed **exit** creati in questo spazio dei nomi.

### <a name="azure-storage-container"></a>Contenitore Archiviazione di Azure
Dal portale di Azure passare agli account di archiviazione. Dovrebbe essere visualizzato un account di archiviazione che inizia con **tolldata**. Fare clic sul contenitore **tolldata** per visualizzare il file JSON caricato con i dati di registrazione del veicolo.

### <a name="azure-sql-database"></a>database SQL di Azure
1. Tornare al portale di Azure nella prima scheda aperta nel browser. Fare clic su **DATABASE SQL** sulla sinistra del portale di Azure per visualizzare il database SQL che verrà usato nell'esercitazione e fare clic su **tolldatadb**.
   
    ![Screenshot del database SQL creato](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)
2. Copiare il nome del server senza il numero di porta, ad esempio*nomeserver*.database.windows.net.
    ![Screenshot del database SQL creato](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15a.png)

## <a name="connect-to-the-database-from-visual-studio"></a>Connettersi al database da Visual Studio
Usare Visual Studio per accedere ai risultati di query nel database di output.

Connettersi al database SQL di destinazione da Visual Studio:

1. Aprire Visual Studio e fare clic su **Strumenti** > **Connetti al database**.
2. Se richiesto, selezionare **Microsoft SQL Server** come origine dati.
   
    ![Finestra di dialogo Modifica origine dati](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)
3. Nel campo **Nome server** incollare il nome copiato nella sezione precedente dal portale di Azure, ovvero *nomeserver*.database.windows.net.
4. Selezionare **Usa autenticazione di SQL Server**.
5. Immettere **tolladmin** nel campo **Nome utente** e **123toll!** in the **Password** .
6. Fare clic su **Seleziona o immetti nome di database** e selezionare il database **TollDataDB**.
   
    ![Finestra di dialogo Aggiungi connessione](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)
7. Fare clic su **OK**.
8. Aprire Esplora server.
   
    ![Esplora server](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)
9. Nel database TollDataDB sono presenti quattro tabelle.
   
    ![Tabelle nel database TollDataDB](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)

## <a name="event-generator-tollapp-sample-project"></a>Generatore di eventi: progetto di esempio TollApp
Lo script di PowerShell avvia automaticamente l'invio di eventi tramite l'applicazione di esempio TollApp. Non è necessario eseguire ulteriori passaggi.

Per informazioni dettagliate sull'implementazione, il codice sorgente dell'applicazione TollApp è disponibile in [samples/TollApp](https://aka.ms/azure-stream-analytics-toll-source)in GitHub.

![Screenshot del codice di esempio visualizzato in Visual Studio](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

## <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso.
1. Nel portale di Azure fare clic sul segno di addizione verde nell'angolo superiore sinistro della pagina per creare un nuovo processo di analisi di flusso. Selezionare **Intelligence e analisi** e quindi fare clic su **Processo di Analisi di flusso**.
   
    ![New button](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)
2. Fornire un nome di processo, convalidare la correttezza della sottoscrizione e quindi creare un nuovo gruppo di risorse nella stessa area di archiviazione di hub eventi; il valore predefinito è Stati Uniti centro-meridionali per lo script.
3. Fare clic su **Aggiungi al dashboard** e quindi su **CREA** nella parte inferiore della pagina.
   
    ![Opzione Crea processo di Analisi di flusso](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## <a name="define-input-sources"></a>Definire le origini di input
1. Verrà creato il processo e verrà aperta la pagina del processo. In alternativa, è possibile fare clic sul processo di analisi creato nel dashboard del portale.

2. Fare clic sulla scheda **INPUT** per definire i dati di origine.
   
    ![Scheda Input](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.png)
3. Fare clic su **AGGIUNGERE UN INPUT**.
   
    ![Opzione Aggiungere un input](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)
4. Immettere **EntryStream** come **ALIAS DI INPUT**.
5. Il tipo di origine è **flusso di dati**
6. L'origine è **hub eventi**.
7. **Spazio dei nomi del bus di servizio** deve essere quello TollData nell'elenco a discesa.
8. **Nome hub eventi** deve essere impostato su **entry**.
9. **Nome criterio hub eventi* è **RootManageSharedAccessKey** (il valore predefinito).
10. Selezionare **JSON** per **FORMATO DI SERIALIZZAZIONE EVENTI** e **UTF8** per **CODIFICA**.
   
    Le impostazioni vengono visualizzate in questo modo:
   
    ![Impostazioni dell'hub eventi](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

10. Fare clic su **Crea** nella parte inferiore della pagina per terminare la procedura guidata.
    
    Dopo aver creato il flusso in ingresso, seguirà la stessa procedura per creare il flusso in uscita. Assicurarsi di immettere i valori come nello screenshot seguente.
    
    ![Impostazioni per il flusso in uscita](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)
    
    Sono stati definiti due flussi di input:
    
    ![Flussi di input definiti nel portale di Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.png)
    
    Successivamente, si aggiungeranno dati di riferimento per il file BLOB contenente i dati di registrazione dell'automobile.
11. Fare clic su **AGGIUNGI**, quindi seguire lo stesso processo per gli input di flusso ma selezionare **DATI DI RIFERIMENTO** anziché **Flusso dati** e **Alias di Input** è **Registrazione**.

12. account di archiviazione che inizia con **tolldata**. Il nome del contenitore deve essere **tolldata** e il **MODELLO PERCORSO** deve essere **registration.json**. Il nome file fa distinzione tra maiuscole e minuscole e deve contenere solo **lettere minuscole**.
    
    ![Impostazioni dell'archivio BLOB](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)
13. Fare clic su **Crea** per completare la procedura guidata.

Ora sono definiti tutti gli input.

## <a name="define-output"></a>Definire l'output
1. Nel riquadro di panoramica Processo di Analisi di flusso selezionare **OUTPUT**.
   
    ![Scheda Output e opzione "Aggiungere un output"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.png)
2. Fare clic su **Aggiungi**.
3. Impostare **Alias di output** su 'output' e quindi **Sink** su **Database SQL**.
3. Selezionare il nome del server usato nella sezione "Connettersi al database da Visual Studio" di questo articolo. Il nome database è **TollDataDB**.
4. Immettere **tolladmin** nel campo **NOME UTENTE**, **123toll!** nel campo **PASSWORD** e **TollDataRefJoin** nel campo **TABELLA**.
   
    ![Impostazioni del database SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.png)
5. Fare clic su **Crea**.

## <a name="azure-stream-analytics-query"></a>Query di analisi di flusso di Azure
La scheda **QUERY** contiene una query SQL che trasforma i dati in ingresso.

![Query aggiunta alla scheda Query](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.png)

Questa esercitazione ha lo scopo di rispondere a diverse domande aziendali relative ai dati dei caselli e costruire query di Analisi di flusso da poter usare in Analisi di flusso di Azure per fornire risposte pertinenti.

Prima di iniziare il primo processo di Analisi di flusso, verranno esaminati alcuni scenari e la sintassi delle query.

## <a name="introduction-to-azure-stream-analytics-query-language"></a>Introduzione al linguaggio di query di Analisi di flusso di Azure
- - -
Si supponga di dover contare il numero di veicoli che entra in un casello. Trattandosi di un flusso continuo di eventi, è necessario stabilire un "periodo di tempo". La domanda dovrà essere posta come segue: "Quanti veicoli entrano in un casello ogni tre minuti?". Questo tipo di conteggio viene detto a cascata.

Si osservi la query di Analisi di flusso di Azure che risponde a questa domanda:

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

Come si può notare, Analisi di flusso di Azure usa un linguaggio di query simile a SQL e aggiunge alcune estensioni per specificare gli aspetti temporali della query.

Per maggiori dettagli, vedere gli articoli di MSDN sui costrutti relativi alla [gestione del tempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) e alla [funzione di windowing](https://msdn.microsoft.com/library/azure/dn835019.aspx) usati nella query.

## <a name="testing-azure-stream-analytics-queries"></a>Eseguire i test delle query di analisi di flusso di Azure
Dopo aver scritto la prima query di Analisi di flusso di Azure, occorre testarla con file di dati di esempio disponibili nella cartella TollApp nel percorso seguente:

**..\\TollApp\\TollApp\\Data**

Questa cartella contiene i file seguenti:

* Entry.json
* Exit.json
* registration.json

## <a name="question-1-number-of-vehicles-entering-a-toll-booth"></a>Domanda 1: Numero di veicoli che entrano in un casello
1. Aprire il portale di Azure e passare al processo di Analisi di flusso di Azure creato. Fare clic sulla scheda **QUERY** e incollare la query dalla sezione precedente.

2. Per convalidare la query sui dati di esempio, caricare i dati nell'input EntryStream facendo clic sul simbolo ... e selezionando **Carica dati di esempio da file**.

    ![Screenshot del file Entry.json](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)
3. Nel riquadro visualizzato selezionare il file (Entry.json) nel computer locale e fare clic su **OK**. L'icona di **test** diventerà luminosa e selezionabile.
   
    ![Screenshot del file Entry.json](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.png)
3. Verificare che l'output della query sia come previsto:
   
    ![Risultati del test](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.png)

## <a name="question-2-report-total-time-for-each-car-to-pass-through-the-toll-booth"></a>Domanda 2: Report del tempo totale impiegato da ogni automobile per attraversare il casello
Il tempo medio necessario a un'automobile per passare attraverso il casello consente di valutare l'efficienza del processo e l'esperienza dell'utente.

Per trovare il tempo totale, è necessario creare un join tra il flusso EntryTime e il flusso ExitTime. Creare un join dei flussi nelle colonne TollId e LicencePlate. L'operatore **JOIN** richiede di specificare un margine temporale che descrive la differenza di tempo accettabile tra gli eventi uniti. La funzione **DATEDIFF** permette di specificare che la distanza tra un evento e l'altro non deve superare i 15 minuti. La funzione **DATEDIFF** viene applicata anche agli orari di uscita e di ingresso per calcolare il tempo effettivamente trascorso dall'automobile nel casello. Si noti il diverso uso di **DATEDIFF** in un'istruzione **SELECT** rispetto a una condizione **JOIN**.

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

1. Per testare questa query, aggiornarla nella scheda **QUERY** per il processo. Aggiungere il file di test per **ExitStream** esattamente come è stato immesso in precedenza **EntryStream**.
   
2. Fare clic su **Test**.

3. Selezionare la casella di controllo per testare la query e visualizzare l'output:
   
    ![Output del test](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## <a name="question-3-report-all-commercial-vehicles-with-expired-registration"></a>Domanda 3: Report di tutti i veicoli commerciali con registrazione scaduta
Analisi di flusso di Azure può usare snapshot statici dei dati da unire ai flussi di dati temporali. Per illustrare questa funzionalità, usare la domanda di esempio seguente.

Se un veicolo commerciale è registrato presso l'azienda che gestisce il casello, lo può attraversare senza essere fermato per un controllo. Usare una tabella di ricerca relativa alla registrazione dei veicoli commerciali per identificare tutti i veicoli commerciali con registrazione scaduta.

```
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

Per testare una query usando dati di riferimento, è necessario definire un'origine di input per i dati di riferimento, come è già stato fatto.

Per testare questa query, incollarla nella scheda **QUERY**, fare clic su **Test** e specificare le due origini di input e i dati di esempio di registrazione e fare clic su **Test**.  
   
![Output del test](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

## <a name="start-the-stream-analytics-job"></a>Avviare il processo di analisi di flusso
A questo punto è possibile terminare la configurazione e avviare il processo. Salvare la query della domanda 3. Verrà generato un output corrispondente allo schema della tabella di output **TollDataRefJoin**.

Passare al **DASHBOARD** del processo e fare clic su **AVVIA**.

![Screenshot del pulsante Avvia nel dashboard del processo](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.png)

Nella finestra di dialogo visualizzata modificare l'ora di **AVVIA OUTPUT** in **ORA PERSONALIZZATA**. Modificare l'ora impostandola su un'ora prima dell'ora corrente. In questo modo tutti gli eventi dall'hub eventi vengono elaborati da quando si è iniziato a generare eventi all'inizio dell'esercitazione. A questo punto fare clic sul pulsante **Start** per avviare il processo.

![Selezione dell'ora personalizzata](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

L'avvio del processo può richiedere alcuni minuti. Lo stato è disponibile nella pagina di livello principale di Analisi di flusso.

![Screenshot dello stato del processo](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.png)

## <a name="check-results-in-visual-studio"></a>Controllare i risultati in Visual Studio
1. Aprire Esplora server di Visual Studio e fare clic con il pulsante destro del mouse sulla tabella **TollDataRefJoin** .
2. Fare clic su **Mostra dati tabella** per visualizzare l'output del processo.
   
    ![Selezione di "Mostra dati tabella" in Esplora server](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## <a name="scale-out-azure-stream-analytics-jobs"></a>Scalabilità orizzontale dei processi di Analisi di flusso di Azure
Analisi di flusso di Azure è progettato per offrire elasticità di ridimensionamento e per gestire una grande quantità di dati. La query di analisi di flusso di Azure può usare la clausola **PARTITION BY** per comunicare al sistema che questo passaggio aumenterà il numero di istanze. **PartitionId** è una colonna speciale aggiunta dal sistema e corrispondente all'ID partizione dell'input, ovvero l'hub eventi.

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId

1. Arrestare il processo corrente, aggiornare la query nella scheda **QUERY** e aprire **Impostazioni** nel dashboard del processo. Fare clic su **Scale**.
   
    **UNITÀ DI STREAMING** definiscono la potenza di calcolo che il processo può ricevere.
2. Modificare l'elenco a discesa da 1 a 6.
   
    ![Screenshot della selezione di sei unità di streaming](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.png)
3. Passare alla scheda **OUTPUT** e modificare il nome della tabella SQL in **TollDataTumblingCountPartitioned**.

Se si avvia il processo a questo punto, Analisi di flusso di Azure può distribuire il carico di lavoro tra più risorse di calcolo e ottenere una maggiore produttività. Si noti che l'applicazione TollApp invia anche gli eventi partizionati in base a TollId.

## <a name="monitor"></a>Monitorare
L'area **MONITORAGGIO** contiene le statistiche relative al processo in esecuzione. La configurazione iniziale è necessaria per usare l'account di archiviazione nella stessa area (nominare il casello come il resto di questo documento).   

![Screenshot di monitoraggio](media/stream-analytics-build-an-iot-solution-using-stream-analytics/monitoring.png)

È possibile accedere a **Log attività** anche dall'area **Impostazioni** del dashboard del processo.


## <a name="conclusion"></a>Conclusioni
Questa esercitazione ha presentato il servizio Analisi di flusso di Azure. È stato illustrato come configurare input e output per il processo di Analisi di flusso. Usando lo scenario dei dati del casello, è stato possibile spiegare le tipologie più comuni di problemi che si verificano nello spazio dei dati in movimento e come risolverli con semplici query di tipo SQL in Analisi di flusso di Azure. Sono stati descritti i costrutti di estensioni SQL per l'uso di dati temporali. È stato illustrato come creare un join tra flussi di dati, come arricchire il flusso di dati con dati di riferimento statici e come aumentare il numero di istanze di una query per ottenere una maggiore produttività.

Anche se questa esercitazione offre una buona introduzione, non può ritenersi completa. Per altri modelli di query che usano il linguaggio SAQL, vedere [Esempi di query per modelli di uso comune di Analisi di flusso](stream-analytics-stream-analytics-query-patterns.md).
Per altre informazioni su Analisi di flusso di Azure, vedere la [documentazione online](https://azure.microsoft.com/documentation/services/stream-analytics/) .

## <a name="clean-up-your-azure-account"></a>Eseguire la pulizia dell'account Azure
1. Arrestare il processo di Analisi di flusso nel portale di Azure.
   
    Lo script Setup.ps1 crea due hub eventi e un database SQL. Le istruzioni seguenti permettono di eseguire la pulizia delle risorse al termine dell'esercitazione.
2. In una finestra di PowerShell digitare **.\\Cleanup.ps1** per avviare lo script che elimina le risorse usate nell'esercitazione.
   
   > [!NOTE]
   > Le risorse vengono identificate in base al nome. Assicurarsi di controllare attentamente ogni elemento prima di confermarne la rimozione.
   > 
   > 


