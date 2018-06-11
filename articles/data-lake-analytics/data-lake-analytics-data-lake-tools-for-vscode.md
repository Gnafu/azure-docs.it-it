---
title: Usare gli Strumenti Azure Data Lake per Visual Studio Code
description: Informazioni su come usare gli Strumenti Azure Data Lake per Visual Studio Code per creare, testare ed eseguire gli script U-SQL.
services: data-lake-analytics
author: Jejiang
ms.author: jejiang
manager: kfile
editor: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: 4f5d51a2a34e89223f51f456f2c730835b1e2451
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34735188"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Usare gli Strumenti Azure Data Lake per Visual Studio Code

Informazioni su Strumenti Azure Data Lake per Visual Studio Code (VS Code) per creare, testare ed eseguire gli script U-SQL. Le informazioni sono disponibili anche nel video seguente:

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>prerequisiti

Gli Strumenti Azure Data Lake per Visual Studio Code supportano Windows, Linux e MacOS.  

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).

Per MacOS e Linux:
- [.NET Core SDK 2.0](https://www.microsoft.com/net/download/core). 
- [Mono 5.2.x](http://www.mono-project.com/download/).

## <a name="install-azure-data-lake-tools"></a>Installare Azure Data Lake Tools

Dopo avere installato i prerequisiti, è possibile installare Strumenti Azure Data Lake per VS Code.

**Per installare Strumenti Azure Data Lake**

1. Aprire Visual Studio Code.
2. Fare clic su **Estensioni** nel riquadro sinistro. Immettere **Strumenti Azure Data Lake** nella casella di ricerca.
3. Fare clic su **Installa** accanto a **Strumenti Azure Data Lake**. Dopo alcuni secondi, il pulsante **Installa** verrà sostituito dal pulsante **Ricarica**.
4. Fare clic su **Ricarica** per attivare l'estensione **Strumenti Azure Data Lake**.
5. Fare clic su **Ricarica finestra** per confermare. La voce **Strumenti Azure Data Lake** sarà visualizzata nel riquadro delle estensioni.

    ![Riquadro Estensioni di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

 
## <a name="activate-azure-data-lake-tools"></a>Attivare Strumenti Azure Data Lake
Creare un nuovo file .usql o aprire un file .usql esistente per attivare l'estensione. 


## <a name="work-with-u-sql"></a>Uso di U-SQL

Per usare U-SQL, è necessario aprire una cartella o un file U-SQL.

**Per aprire lo script di esempio**

Usare il riquadro comandi (CTRL+MAIUSC+P) e immettere **ADL: Open Sample Script** (ADL: Apri script di esempio). Viene aperta un'altra istanza di questo esempio. Anche in questa istanza è possibile modificare, configurare e inviare lo script.

**Aprire una cartella per il progetto U-SQL**

1. Da Visual Studio Code selezionare il menu **File** e quindi selezionare **Apri cartella**.
2. Specificare una cartella e quindi scegliere **Seleziona cartella**.
3. Selezionare il menu **File** e quindi scegliere **Nuovo**. Un file Untilted-1 viene aggiunto al progetto.
4. Immettere il codice seguente nel file Untitled-1:

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO "/Output/departments.csv"
        USING Outputters.Csv();

    Lo script crea un file departments.csv con alcuni dati inclusi nella cartella /output.

5. Salvare il file come **myUSQL.usql** nella cartella aperta.

**Compilare uno script U-SQL**

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi. 
2. Immettere **ADL: Compile Script**. I risultati di compilazione vengono visualizzati nella finestra **Output**. È anche possibile fare clic con il pulsante destro del mouse su un file di script e selezionare **ADL: Compile Script** (ADL: Compila script) per eseguire la compilazione di un processo U-SQL. Il risultato della compilazione viene visualizzato nel riquadro **Output**.
 
**Inviare uno script U-SQL**

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi. 
2. Immettere **ADL: Submit Job**.  È anche possibile fare clic con il pulsante destro del mouse su un file di script e selezionare **ADL: Invia processo**. 

 Dopo aver inviato un processo U-SQL, i log di invio vengono visualizzati nella finestra **Output** in VS Code. Il riquadro di destra contiene la visualizzazione del processo. Se l'invio ha esito positivo, viene visualizzato anche l'URL del processo. È possibile aprire l'URL del processo in un Web browser per monitorare lo stato del processo in tempo reale. Nella scheda Summary (Riepilogo) di Job View (Visualizzazione processo) è possibile vedere i dettagli del processo. Le funzioni principali includono uno script di reinvio, uno script di duplicazione e l'apertura nel portale. Nella scheda Data (Dati) di Job View (Visualizzazione processo) è possibile fare riferimento a file di input, di output e di risorse. I file possono essere scaricati nel computer locale.

   ![File di configurazione degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

   ![File di configurazione degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

**Set Default Context** (Imposta contesto predefinito)

 È possibile impostare un contesto predefinito per applicare le stesse impostazioni a tutti i file di script, se non sono stati impostati parametri specifici per ogni file.

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi. 
2. Immettere **ADL: Set Default Context** (ADL: Imposta contesto predefinito).
3. In alternativa, fare clic con il pulsante destro del mouse sull'editor di script, selezionare **ADL: Set Default Context** (ADL: Imposta contesto predefinito) e quindi scegliere l'account, il database e lo schema che si vogliono usare. L'impostazione viene salvata nel file di configurazione xxx_settings.json.

    ![File di configurazione degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

**Set Script Parameters** (Imposta parametri di script)

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi. 
2. Immettere **ADL: Set Script Parameters** (ADL: Imposta parametri di script).
3. Viene aperto il file xxx_settings.json con le proprietà seguenti:

  - Account: account di Data Lake Analytics nella sottoscrizione di Azure, necessario per compilare ed eseguire processi U-SQL. È quindi necessario configurare l'account del computer prima di compilare ed eseguire i processi U-SQL.
    - Database: un database nel proprio account. Il database predefinito è **master**.
    - Schema: uno schema nel database. Lo schema è **dbo**.
    - Impostazioni facoltative:
        - Priorità: L'intervallo di priorità va da 1 a 1000 dove la priorità più alta corrisponde al valore 1. Il valore predefinito è **1000**.
        - Parallelismo: l'intervallo di parallelismo va da 1 a 150. Il valore predefinito è il parallelismo massimo consentito nell'account Azure Data Lake Analytics. 

        ![File di configurazione degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)
      
        > [!NOTE] 
        > Dopo il salvataggio della configurazione, se non è stato configurato un contesto predefinito, le informazioni relative ad account, database e schema vengono visualizzate sulla barra di stato nell'angolo inferiore sinistro del file con estensione USQL corrispondente.

**Set Git Ignore** (Imposta Git Ignore)

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi. 
2. Immettere **ADL: Set Git Ignore** (ADL: Imposta Git Ignore).

    - Se nella cartella di lavoro di Visual Studio Code non è presente un file con estensione **gitIgnore**, nella cartella viene creato un file denominato **.gitIgnor**. Per impostazione predefinita, nel file vengono aggiunti quattro elementi (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache** e **obj**). Se necessario, è possibile eseguire altri aggiornamenti.
    - Se nella cartella di lavoro di Visual Studio Code è già presente un file con estensione**gitIgnore** e in questo file non sono presenti gli elementi **usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache** e **obj**, lo strumento aggiunge questi quattro elementi nel file con estensione **gitIgnore**.

    ![File di configurazione degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)


## <a name="work-with-code-behind-file-csharp-python-and-r"></a>Usare file code-behind: CSharp, Python e R

Gli Strumenti Azure Data Lake supportano diversi tipi di codice personalizzato. Per istruzioni, vedere [Eseguire lo sviluppo U-SQL con Python, R e CSharp per Azure Data Lake Analytics in VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Usare gli assembly

Per informazioni sullo sviluppo di assembly, vedere [Develop U-SQL assemblies for Azure Data Lake Analytics jobs](data-lake-analytics-u-sql-develop-assemblies.md) (Sviluppare assembly U-SQL per i processi di Azure Data Lake Analytics).

È possibile usare Strumenti Data Lake per registrare gli assembly di codice personalizzato nel catalogo di Data Lake Analytics.

**Registrare un assembly**

È possibile registrare l'assembly tramite il comando **ADL: Register Assembly** (ADL: Registra assembly) o **ADL: Register Assembly (Advanced)** (ADL: Registra assembly (avanzato)).

**Per eseguire la registrazione mediante il comando ADL: Register Assembly** (ADL: Registra assembly)
1.  Premere CTRL+MAIUSC+P per aprire il riquadro comandi.
2.  Immettere **ADL: Register Assembly** (ADL: Registra assembly). 
3.  Specificare il percorso dell'assembly locale. 
4.  Selezionare un account di Data Lake Analytics.
5.  Selezionare un database.

Risultati: il portale viene aperto nel browser e visualizza il processo di registrazione dell'assembly.  

Un altro modo pratico per attivare il comando **ADL: Register Assembly** (ADL: Registra assembly) consiste nel fare clic con il pulsante destro del mouse sul file .dll in Esplora file. 

**Per eseguire la registrazione mediante il comando ADL: Register Assembly (Advanced)** (ADL: Registra assembly (avanzato))
1.  Premere CTRL+MAIUSC+P per aprire il riquadro comandi.
2.  Immettere **ADL: Register Assembly (Advanced)** (ADL: Registra assembly (avanzato)). 
3.  Specificare il percorso dell'assembly locale. 
4.  Verrà visualizzato il file JSON. Esaminare e modificare le dipendenze dell'assembly e i parametri delle risorse, se necessario. Le istruzioni sono visualizzate nella finestra **Output**. Salvare (CTRL+S) il file JSON per procedere con la registrazione dell'assembly.

    ![Code-behind di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
    
   >[!NOTE]
   >- Dipendenze dell'assembly: Strumenti Azure Data Lake rileva automaticamente se la DLL include dipendenze. Le dipendenze vengono visualizzate nel file JSON dopo che sono state rilevate. 
   >- Risorse: è possibile caricare le risorse DLL (ad esempio file con estensione txt, png e csv) nel corso della registrazione dell'assembly. 

Un altro modo per attivare il comando **ADL: Register Assembly (Advanced)** (ADL: Registra assembly (avanzato)) consiste nel fare clic con il pulsante destro del mouse sul file con estensione dll in Esplora file. 

Il codice U-SQL seguente illustra come chiamare un assembly. Nell'esempio il nome dell'assembly è *test*.


        REFERENCE ASSEMBLY [test];

        @a = 
            EXTRACT 
                Iid int,
            Starts DateTime,
            Region string,
            Query string,
            DwellTime int,
            Results string,
            ClickedUrls string 
            FROM @"Sample/SearchLog.txt" 
            USING Extractors.Tsv();

        @d =
            SELECT DISTINCT Region 
            FROM @a;

        @d1 = 
            PROCESS @d
            PRODUCE 
                Region string,
            Mkt string
            USING new USQLApplication_codebehind.MyProcessor();

        OUTPUT @d1 
            TO @"Sample/SearchLogtest.txt" 
            USING Outputters.Tsv();


## <a name="u-sql-local-run-and-local-debug-for-windows-users"></a>Esecuzione e debug locali di U-SQL per utenti Windows
L'esecuzione locale di U-SQL verifica i dati locali e convalida lo script localmente prima che il codice venga pubblicato in Data Lake Analytics. La funzionalità di debug locale consente di completare le seguenti operazioni prima che il codice venga inviato a Data Lake Analytics: 
- Eseguire il debug del code-behind di C#. 
- Eseguire il codice passo per passo. 
- Convalidare lo script in locale.

Per istruzioni sull'esecuzione e il debug locali, vedere [Esecuzione locale e debug locale di U-SQL con Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).


## <a name="connect-to-azure"></a>Connect to Azure

Prima di compilare ed eseguire gli script U-SQL in Data Lake Analytics, è necessario connettersi al proprio account di Azure.

<b id="sign-in-by-command">Per connettersi ad Azure tramite un comando</b>

1.  Premere CTRL+MAIUSC+P per aprire il riquadro comandi. 
2.  Immettere **ADL: Login** (ADL: Accedi). Le informazioni di accesso vengono visualizzate in basso a destra.

    ![Riquadro comandi degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

    ![Informazioni di accesso del dispositivo di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3.  Fare clic su **Copy & Open** (Copia e apri) per aprire la pagina Web di accesso con URL https://aka.ms/devicelogin. Incollare il codice nella casella di testo e quindi selezionare **Continua**.

    ![Codice di accesso da incollare di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )  
     
4.  Seguire le istruzioni per accedere dalla pagina Web. Quando si è connessi viene visualizzato il nome di account di Azure sulla barra di stato nell'angolo inferiore sinistro della finestra **VS Code**. 

    > [!NOTE] 
    >- Se si accede a Strumenti Data Lake e non ci si disconnette, la volta successiva si accederà automaticamente.
    >- Se l'account ha due fattori abilitati, è consigliabile usare l'autenticazione telefonica anziché un PIN.


Per disconnettersi immettere il comando **ADL: Logout** (ADL: Disconnetti).

**Per connettersi ad Azure da Explorer**

Per accedere da Explorer, espandere **AZURE DATA LAKE**, fare clic su **Accedi ad Azure** e quindi seguire i passaggi 3 e 4 della sezione [**Per connettersi ad Azure tramite un comando**](#sign-in-by-command).

![Connettersi ad Azure da Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Non è possibile disconnettersi da Explorer. Per disconnettersi, vedere la sezione [**Per connettersi ad Azure tramite un comando**](#sign-in-by-command).


## <a name="create-extract-script"></a>Creare uno script EXTRACT 
È possibile creare un script EXTRACT per file CSV, TSV e TXT usando il comando **ADL: Crea script EXTRACT** o da **Azure Data Lake Explorer**.

**Per creare uno script EXTRACT tramite un comando**

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi e immettere **ADL: Crea script EXTRACT**.
2. Specificare il percorso completo di un file di archiviazione di Azure e premere **INVIO**.
3. Selezionare un account.
4. Per il file TXT, selezionare un delimitatore per estrarre il file. 

    ![Processo di creazione dello script EXTRACT](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

Lo script EXTRACT viene generato in base ai valori immessi. Per uno script che non è in grado di rilevare le colonne, scegliere una tra le due opzioni. In caso contrario, verrà generato un solo script.

![Risultato della creazione dello script EXTRACT](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

**Per creare lo script EXTRACT da Explorer**

È possibile creare lo script EXTRACT anche tramite il menu di scelta rapida che è possibile visualizzare facendo clic con il pulsante destro del mouse sul file CSV, TSV o TXT nell'archiviazione di Data Lake o nell'archiviazione BLOB.

![Creare lo script EXTRACT dal menu di scelta rapida](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-command"></a>Eseguire l'integrazione con Azure Data Lake Analytics tramite un comando

È possibile accedere alle risorse di Azure Data Lake Analytics ed eseguire operazioni come elencare gli account, accedere ai metadati e visualizzare i processi di analisi. 

**Per elencare gli account Azure Data Lake Analytics della sottoscrizione di Azure**

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi.
2. Immettere **ADL: List Accounts** (ADL: Elenca gli account). L'account viene visualizzato nel riquadro **Output**.

**Per accedere ai metadati di Azure Data Lake Analytics**

1.  Premere CTRL+MAIUSC+P e digitare **ADL: List Tables** (ADL: Elenca tabelle).
2.  Selezionare uno degli account di Data Lake Analytics.
3.  Selezionare uno dei database di Data Lake Analytics.
4.  Selezionare uno degli schemi. È possibile visualizzare l'elenco delle tabelle.

**Per visualizzare i processi di Azure Data Lake Analytics**
1.  Aprire il riquadro comandi (CTRL+MAIUSC+P) e selezionare **ADL: Show Jobs** (ADL: Mostra processi). 
2.  Selezionare un account di Data Lake Analytics o locale. 
3.  Attendere che venga visualizzato l'elenco dei processi per l'account.
4.  Selezionare un processo dall'elenco dei processi. Strumenti Data Lake aprirà la visualizzazione dei processi nel riquadro di destra e visualizzerà alcune informazioni in **OUTPUT** di Visual Studio Code.

    ![Tipi di oggetto IntelliSense degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-storage-through-command"></a>Eseguire l'integrazione con l'archiviazione di Azure Data Lake tramite un comando

È possibile usare i comandi correlati all'archiviazione di Azure Data Lake per:
 - Esplorare le risorse di archiviazione di Azure Data Lake. [Elencare il percorso di archiviazione](#list-the-storage-path). 
 - Visualizzare in anteprima il file di archiviazione di Azure Data Lake. [Visualizzare in anteprima il file di archiviazione](#preview-the-storage-file). 
 - Caricare il file direttamente nell'archiviazione di Azure Data Lake in VS Code. [Caricare un file o una cartella](#upload-file-or-folder).
 - Scaricare il file direttamente dall'archiviazione di Azure Data Lake in VS Code. [Scaricare il file](#download-file).

### <a name="list-the-storage-path"></a>Elencare il percorso di archiviazione 

**Per elencare il percorso di archiviazione tramite il riquadro comandi**

Fare clic con il pulsante destro del mouse sull'editor di script e scegliere **ADL: List Path** (ADL: Elenca percorso).

Scegliere la cartella nell'elenco o fare clic su **Enter Path** (Immetti percorso) o su **Browse from Root** (Sfoglia da radice). Nella figura seguente viene selezionata l'opzione "Enter a path" (Immetti percorso) a titolo di esempio. -> Selezionare il proprio **account ADLA**. -> Passare al percorso della cartella di archiviazione o immetterlo manualmente, ad esempio /output/. -> Il riquadro comandi elenca le informazioni sul percorso in base all'input.

![Risultato dell'inserimento del percorso di archiviazione di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Un modo più pratico per elencare il percorso relativo consiste nell'usare il menu di scelta rapida.

**Per elencare il percorso di archiviazione facendo clic con il pulsante destro del mouse**

Fare clic con il pulsante destro del mouse sulla stringa del percorso e scegliere **List Path** (Elenca percorso) per continuare.

![Menu di scelta rapida di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


### <a name="preview-the-storage-file"></a>Visualizzare in anteprima il file di archiviazione

Fare clic con il pulsante destro del mouse sull'editor di script e scegliere **ADL: Preview File** (ADL: Anteprima file).

Selezionare il proprio **account ADLA**. -> Immettere un percorso di file di archiviazione di Azure, ad esempio /output/SearchLog.txt. -> Risultato: il file viene aperto in VSCode.

   ![Risultato del file di anteprima in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Un altro modo per visualizzare l'anteprima del file è fare clic con il pulsante destro del mouse sul percorso completo o sul percorso relativo del file nell'editor di script. 

### <a name="upload-file-or-folder"></a>Caricare un file o una cartella

1. Fare clic con il pulsante destro del mouse sull'editor di script e scegliere **Upload File** (Carica file) o **Upload Folder** (Carica cartella).

2. Scegliere uno o più file se è stata selezionata l'opzione per il caricamento dei file oppure scegliere l'intera cartella se è stata selezionata l'opzione di caricamento della cartella e quindi fare clic su **Upload** (Carica). -> Scegliere la cartella di archiviazione nell'elenco o fare clic su **Enter Path** (Immetti percorso) o su **Browse from Root** (Sfoglia da radice). Come esempio è stata selezionata l'opzione "Enter a path" (Immetti percorso). -> Selezionare il proprio **account ADLA**. -> Passare al percorso della cartella di archiviazione o immetterlo manualmente, ad esempio /output/. -> Fare clic su **Choose Current Folder** (Scegli cartella corrente) per specificare la destinazione di caricamento.

   ![Stato di caricamento in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    


   Un altro modo per caricare file da archiviare è fare clic con il pulsante destro del mouse sul percorso completo del file o sul percorso relativo del file nell'editor di script.

È contemporaneamente possibile monitorare lo [stato di caricamento](#check-storage-tasks-status).


### <a name="download-file"></a>Scaricare il file 
È possibile scaricare i file usando i comandi **ADL: Scarica file** o **ADL: Download File (Advanced)** (ADL: Scarica file - Opzioni avanzate).

**Per scaricare i file tramite il comando ADL: Download file (Advanced) (ADL: Scarica file - Opzioni avanzate)**
1. Fare clic con il pulsante destro del mouse sull'editor di script e scegliere **Download file (Advanced)** (Scarica file - Opzioni avanzate).
2. VS Code visualizza un file JSON. È possibile immettere i percorsi di file e scaricare più file contemporaneamente. Le istruzioni sono visualizzate nella finestra **Output**. Salvare (CTRL+S) il file JSON per procedere con lo scaricamento del file.

    ![Scaricamento di file in Strumenti Azure Data Lake per Visual Studio Code da configurazione](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

3.  Risultati: la finestra **Output** mostra lo stato di caricamento del file.

    ![Risultato dello scaricamento di più file in Strumenti Azure Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

È contemporaneamente possibile monitorare lo [stato di scaricamento](#check-storage-tasks-status).

**Per scaricare i file tramite il comando ADL: Download file (ADL: Scarica file)**

1. Fare clic con il pulsante destro del mouse sull'editor di script, scegliere **Download File** (Scarica File) e quindi selezionare la cartella di destinazione nella finestra di dialogo **Select Folder** (Seleziona cartella).

2. Scegliere la cartella nell'elenco o fare clic su **Enter Path** (Immetti percorso) o su **Browse from Root** (Sfoglia da radice). Nella figura seguente viene selezionata l'opzione "Enter a path" (Immetti percorso) a titolo di esempio. -> Selezionare il proprio **account ADLA**. -> Passare al percorso della cartella di archiviazione o immetterlo manualmente, ad esempio /output/ -> Scegliere un file da scaricare.

   ![Stato di scaricamento in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

   
   Un altro modo per scaricare file di archiviazione è fare clic con il pulsante destro del mouse sul percorso completo del file o sul percorso relativo del file nell'editor di script.

È contemporaneamente possibile monitorare lo [stato di scaricamento](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Controllare lo stato delle attività di archiviazione
Lo stato viene visualizzato nella parte inferiore della barra di stato al termine delle operazioni di scaricamento e caricamento.
1. Fare clic sulla barra di stato per visualizzare lo stato di caricamento e scaricamento nel riquadro **OUTPUT**.

   ![Verifica dello stato di archiviazione in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)


## <a name="integrate-with-azure-data-lake-analytics-from-explorer"></a>Eseguire l'integrazione con Azure Data Lake Analytics da Explorer

- Dopo l'accesso, tutte le sottoscrizioni dell'account Azure sono elencate nel riquadro sinistro di **AZURE DATALAKE**. 

   ![Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

**Spostamento nei metadati ADLA**

- Espandendo la sottoscrizione di Azure, è possibile spostarsi nel database U-SQL e visualizzare **schemi**, **credenziali**, **assembly**, **tabelle**, **indici**e così via all'interno del nodo U-SQL Databases.

**Gestione delle entità metadati ADLA**

- Espandendo **U-SQL Databases**, è possibile creare database, schemi, tabelle, tipi di tabella, indici e statistiche facendo clic con il pulsante destro del mouse sul menu di scelta rapida **Script to Create** (Script da creare) del nodo corrispondente. Nella pagina dello script aperto modificare lo script in base alle esigenze e quindi inviare il processo facendo clic con il pulsante destro del mouse sul menu di scelta rapida **ADL: Submit Job** (ADL: Invia processo). Al termine dell'operazione di creazione, fare clic sul menu di scelta rapida **Refresh** (Aggiorna) per visualizzare il nuovo elemento creato. È anche possibile eliminare l'elemento facendo clic con il pulsante destro del mouse sul menu di scelta rapida **Delete** (Elimina).

   ![Menu di creazione di un nuovo elemento di DataLake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

   ![Script di creazione di un nuovo elemento di DataLake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

**Registrazione dell'assembly ADLA**

 - È possibile **registrare un assembly** nel database corrispondente facendo clic con il pulsante destro del mouse sul nodo **Assemblies** (Assembly).

    ![Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-storage-from-explorer"></a>Eseguire l'integrazione con l'archiviazione di Azure Data Lake da Explorer

Passare a **Data Lake Store**

 - Nel nodo della cartella è possibile scegliere i comandi **Aggiorna**, **Elimina**, **Carica**, **Upload Folder** (Carica cartella), **Copia percorso relativo** e **Copia percorso completo** dal menu di scelta rapida.

   ![Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- Nel nodo del file è possibile scegliere i comandi **Anteprima**, **Scarica**, **Elimina**, **Crea script EXTRACT** (disponibile solo per i file CSV, TSV e TXT), oltre che **Copia percorso relativo** e **Copia percorso completo**, dal menu di scelta rapida.

    ![Data Lake Explorer - Estrazione](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-explorer"></a>Eseguire l'integrazione con Archiviazione BLOB di Azure da Explorer

Passare ad **Archiviazione BLOB**

- Nel nodo del contenitore BLOB è possibile scegliere i comandi **Aggiorna**, **Elimina contenitore BLOB** e **Carica BLOB** dal menu di scelta rapida.

    ![Nodo contenitori BLOB di Archiviazione BLOB](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Dal nodo della cartella è possibile scegliere i comandi **Aggiorna** e **Carica BLOB** dal menu di scelta rapida.

    ![Nodo cartelle di Archiviazione BLOB](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Nel nodo del file è possibile scegliere i comandi **Anteprima/Modifica**, **Scarica**, **Elimina**, **Crea script EXTRACT** (disponibile solo per i file CSV, TSV e TXT), oltre che **Copia percorso relativo** e **Copia percorso completo**, dal menu di scelta rapida.

    ![Creare lo script EXTRACT dal menu di scelta rapida](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-adl-storage-explorer-in-portal"></a>Aprire ADL Storage Explorer nel portale
1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi.
2. Immettere **Open Web Azure Storage Explorer** (Apri Web Azure Storage Explorer) o fare clic con il pulsante destro del mouse su un percorso relativo o un percorso completo nell'editor di script e quindi scegliere **Open Web Azure Storage Explorer** (Apri Web Azure Storage Explorer).
3. Selezionare un account di Data Lake Analytics.

Strumenti Data Lake apre il percorso di archiviazione nel portale di Azure. È possibile trovare il percorso e visualizzare in anteprima il file dal Web.

## <a name="additional-features"></a>Funzionalità aggiuntive

Strumenti Data Lake per VS Code supporta le funzionalità seguenti:

-   Completamento automatico di IntelliSense: vengono visualizzati suggerimenti in finestre popup intorno agli elementi, ad esempio parole chiave, metodi e variabili. Le icone differenti rappresentano tipi di oggetti diversi:

    - Tipo di dati Scala
    - Tipo di dati complesso
    - UDT integrati
    - Raccolta e classi .NET
    - Espressioni C#
    - UDF, UDO e UDAAG di C# integrati 
    - Funzioni di U-SQL
    - Funzione di windowing di U-SQL
 
    ![Tipi di oggetto IntelliSense degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   Completamento automatico IntelliSense per i metadati di Data Lake Analytics: Strumanti Data Lake scarica le informazioni di metadati di Data Lake Analytics localmente. La funzionalità IntelliSense popola automaticamente gli oggetti, tra cui database, schemi, tabelle, visualizzazione, funzione con valori di tabella, procedure e assembly C#, dai metadati di Data Lake Analytics.
 
    ![Metadati IntelliSense degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   Indicatore di errore di IntelliSense: Strumenti Data Lake sottolinea gli errori di modifica per U-SQL e C#. 
-   Evidenziazione della sintassi: Strumenti Data Lake usa colori diversi per distinguere gli elementi, ad esempio variabili, parole chiave, tipo di dati e funzioni. 

    ![Sintassi in evidenza degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

   > [!NOTE]
   > Microsoft consiglia di eseguire l'aggiornamento a Strumenti Azure Data Lake per Visual Studio versione 2.3.3000.4 o successiva. Le versioni precedenti non sono più disponibili per il download e sono deprecate.  
   
## <a name="next-steps"></a>Passaggi successivi
- [Eseguire lo sviluppo U-SQL con Python, R e CSharp per Azure Data Lake Analytics in VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Esecuzione locale e debug locale di U-SQL per Windows con Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Esercitazione: Introduzione ad Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Esercitazione: Sviluppare script U-SQL tramite Strumenti Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
