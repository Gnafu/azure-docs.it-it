---
title: Kernel per il notebook di Jupyter nei cluster Spark in Azure HDInsight
description: Informazioni sui kernel PySpark, PySpark3 e Spark per i notebook di Jupyter disponibili con i cluster Spark in Azure HDInsight.
keywords: notebook jupyter in spark, spark jupyter
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: hrasheed
ms.openlocfilehash: b2ae24c0449b009db6fcecdd8a1366ea5154629a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66257806"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>Kernel per il notebook di Jupyter nei cluster Apache Spark in Azure HDInsight 

I cluster HDInsight Spark forniscono kernel che è possibile usare con il notebook di Jupyter in [Apache Spark](https://spark.apache.org/) per testare le applicazioni. Un kernel è un programma che esegue e interpreta il codice. I tre kernel sono:

- **PySpark** (per le applicazioni scritte in Python2)
- **PySpark3** (per le applicazioni scritte in Python3)
- **Spark** (per le applicazioni scritte in Scala)

In questo articolo viene illustrato come usare questi kernel e i vantaggi che ne derivano.

## <a name="prerequisites"></a>Prerequisiti

Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Creare un notebook di Jupyter in HDInsight Spark

1. Dal [portale di Azure](https://portal.azure.com/), selezionare il cluster Spark.  Per le istruzioni vedere la sezione su come [elencare e visualizzare i cluster](../hdinsight-administer-use-portal-linux.md#showClusters). Il **Panoramica** verrà aperta la visualizzazione.

2. Dal **Overview** visualizzazione le **dashboard Cluster** , quindi selezionare **notebook di Jupyter**. Se richiesto, immettere le credenziali per il cluster.

    ![Notebook di Jupyter in Spark](./media/apache-spark-jupyter-notebook-kernels/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Notebook di Jupyter in Spark") 
  
   > [!NOTE]  
   > È anche possibile accedere al notebook di Jupyter nel cluster Spark aprendo l'URL seguente nel browser. Sostituire **CLUSTERNAME** con il nome del cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Selezionare **New**, quindi fare clic su **Pyspark**, **PySpark3**, oppure **Spark** per creare un notebook. Usare il kernel Spark per applicazioni Scala, il kernel PySpark per applicazioni Python2 e il kernel PySpark3 per applicazioni Python3.
   
    ![Kernel per il notebook di Jupyter in Spark](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "Kernel per il notebook di Jupyter in Spark") 

4. Verrà aperto un notebook con il kernel selezionato.

## <a name="benefits-of-using-the-kernels"></a>Vantaggi offerti dall'uso dei kernel

Ecco alcuni vantaggi associati all'uso dei nuovi kernel con il notebook di Jupyter nei cluster HDInsight Spark.

- **Contesti predefiniti**. Con i kernel **PySpark**, **PySpark3** e **Spark** non è necessario impostare i contesti Spark o Hive in modo esplicito prima di iniziare a usare le applicazioni. I contesti sono disponibili per impostazione predefinita. Questi contesti sono:
   
  * **sc** : per il contesto Spark
  * **sqlContext** : per il contesto Hive
   
    Quindi non è necessario eseguire istruzioni simili alle seguenti per impostare i contesti:
   
         sc = SparkContext('yarn-client')
         sqlContext = HiveContext(sc)
   
    È possibile invece usare direttamente i contesti preimpostati nell'applicazione.

- **Celle magic**. Il kernel PySpark offre alcuni "magic" predefiniti. Si tratta di comandi speciali che è possibile chiamare con `%%`, ad esempio `%%MAGIC` `<args>`. Il comando magic deve essere la prima parola di una cella di codice e consente di generare più righe di contenuto. La parola magic deve essere la prima della cella. L'aggiunta di qualsiasi elemento prima del comando magic, anche un commento, causa un errore.     Per altre informazioni sui magic, vedere [questa pagina](https://ipython.readthedocs.org/en/stable/interactive/magics.html).
   
    La tabella seguente elenca i diversi magic disponibili tramite i kernel.

   | Magic | Esempio | DESCRIZIONE |
   | --- | --- | --- |
   | help |`%%help` |Genera una tabella di tutti i magic disponibili con esempi e descrizioni |
   | info |`%%info` |Visualizza informazioni sulla sessione per l'endpoint Livy corrente |
   | CONFIGURA |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Configura i parametri per la creazione di una sessione. Il flag force (-f) è obbligatorio se è già stata creata una sessione, affinché tale sessione venga eliminata e ricreata. Visitare la pagina relativa al [corpo della richiesta POST/sessions di Livy](https://github.com/cloudera/livy#request-body) per un elenco dei parametri validi. I parametri devono essere passati come una stringa JSON e devono essere nella riga successiva al magic, come illustrato nella colonna di esempio. |
   | sql |`%%sql -o <variable name>`<br> `SHOW TABLES` |Esegue una query Hive su sqlContext. Se viene passato il parametro `-o` , il risultato della query viene salvato in modo permanente nel contesto Python %%local come frame di dati [Pandas](https://pandas.pydata.org/) . |
   | local |`%%local`<br>`a=1` |Tutto il codice presente nelle righe successive viene eseguito localmente. Deve trattarsi di codice Python2 valido indipendentemente dal kernel usato. In questo caso, anche se si seleziona **PySpark3** oppure **Spark** kernel durante la creazione del notebook, se si usa il `%%local` magic nella cella, tale cella deve contenere solo codice Python2 valido. |
   | logs |`%%logs` |Visualizza i log per la sessione Livy corrente. |
   | delete |`%%delete -f -s <session number>` |Elimina una sessione specifica dell'endpoint Livy corrente. Non è possibile eliminare la sessione avviata dal kernel stesso. |
   | cleanup |`%%cleanup -f` |Elimina tutte le sessioni per l'endpoint Livy corrente, inclusa quella del notebook. Il flag di forzatura -f è obbligatorio. |

   > [!NOTE]  
   > Oltre ai magic aggiunti dal kernel PySpark, è possibile anche usare i [magic IPython incorporati](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), tra cui `%%sh`. È possibile usare il magic `%%sh` per eseguire script e il blocco del codice nel nodo head del cluster.

- **Visualizzazione automatica**. Il kernel Pyspark Visualizza automaticamente l'output delle query Hive e SQL. È possibile scegliere tra diversi tipi di visualizzazione, inclusi Table, Pie, Line, Area e Bar.

## <a name="parameters-supported-with-the-sql-magic"></a>Parametri supportati con il magic %%sql
Il magic `%%sql` supporta parametri diversi che è possibile usare per controllare la tipologia di output che si riceve quando si eseguono query. La tabella seguente elenca l'output.

| Parametro | Esempio | DESCRIZIONE |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Usare questo parametro per salvare in modo permanente il risultato della query nel contesto Python %%local come frame di dati [Pandas](https://pandas.pydata.org/) . Il nome della variabile del frame di dati è il nome della variabile specificato. |
| -q |`-q` |Consente di disattivare le visualizzazioni per la cella. Se non desidera autovisualize il contenuto di una cella e si desidera solo acquisirlo come un frame di dati, quindi usare `-q -o <VARIABLE>`. Se si vogliono disattivare le visualizzazioni senza acquisire i risultati, ad esempio per l'esecuzione di una query SQL come un'istruzione `CREATE TABLE`, usare `-q` senza specificare un argomento `-o`. |
| -m |`-m <METHOD>` |Dove **METHOD** è **take** o **sample**. L'impostazione predefinita è **take**. Se il metodo è **take**, il kernel sceglie gli elementi dall'inizio del set di dati dei risultati specificato da MAXROWS, descritto più avanti in questa tabella. Se il metodo è **sample**, il kernel campiona in modo casuale gli elementi del set di dati in base al parametro `-r` descritto di seguito in questa tabella. |
| -r |`-r <FRACTION>` |Qui **FRACTION** è un numero a virgola mobile compreso tra 0,0 e 1,0. Se il metodo di campionamento per la query SQL è `sample`, il kernel campiona automaticamente in modo casuale la frazione specificata degli elementi del set di risultati. Se si esegue una query SQL con gli argomenti `-m sample -r 0.01`, ad esempio, viene campionato in modo casuale l'1% delle righe dei risultati. |
| -n |`-n <MAXROWS>` |**MAXROWS** è un valore intero. Il kernel limita il numero di righe di output a **MAXROWS**. Se **MAXROWS** è un numero negativo, ad esempio **-1**, il numero di righe nel set di risultati non è limitato. |

**Esempio:**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

L'istruzione precedente esegue le operazioni seguenti:

* Seleziona tutti i record da **hivesampletable**.
* Perché viene usato - q, disattiva autovisualization.
* Dal momento che si usa `-m sample -r 0.1 -n 500` , campiona in modo casuale il 10% delle righe di hivesampletable e limita la dimensione del set di risultati a 500 righe.
* Infine, poiché è stato usato `-o query2` , salva anche l'oputput in un frame di dati denominato **query2**.

## <a name="considerations-while-using-the-new-kernels"></a>Considerazioni per l'uso dei nuovi kernel

Indipendentemente dal kernel usato, se si lasciano i notebook in esecuzione vengono utilizzate risorse del cluster.  Con questi kernel, dato che i contesti sono preimpostati, uscendo semplicemente dal notebook non viene terminato il contesto e quindi le risorse del cluster restano in uso. Quando non è più necessario usare il notebook, è consigliabile scegliere l'opzione **Close and Halt** (Chiudi e interrompi) dal menu **File** del notebook, in modo da terminare il contesto e uscire quindi dal notebook.

## <a name="where-are-the-notebooks-stored"></a>Dove sono archiviati i notebook

Se il cluster usa Archiviazione di Azure come account di archiviazione predefinito, i notebook Jupyter vengono salvati nell'account di archiviazione nella cartella **/HdiNotebooks**.  I notebook, i file di testo e le cartelle che si creano da Jupyter sono accessibili dall'account di archiviazione.  Se si usa Jupyter per creare una cartella **myfolder** e un notebook **myfolder/mynotebook.ipynb**, ad esempio, è possibile accedere al notebook in `/HdiNotebooks/myfolder/mynotebook.ipynb` all'interno dell'account di archiviazione.  Analogamente, se si carica un notebook direttamente nell'account di archiviazione in `/HdiNotebooks/mynotebook1.ipynb`, il notebook è visibile anche da Jupyter.  I notebook vengono conservati nell'account di archiviazione anche dopo l'eliminazione del cluster.

> [!NOTE]  
> I cluster HDInsight con Azure Data Lake Storage come spazio di archiviazione predefinito non archiviano i notebook nello spazio di archiviazione associato.

La modalità di salvataggio dei notebook nell'account di archiviazione è compatibile con [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html). Se si usa SSH nel cluster, è quindi possibile usare comandi di gestione dei file come illustrato nel frammento seguente:

    hdfs dfs -ls /HdiNotebooks                            # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                   # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter

Indipendentemente dal fatto che il cluster usi Archiviazione di Azure o Azure Data Lake Storage come account di archiviazione predefinito, i notebook vengono salvati anche nel nodo head del cluster in `/var/lib/jupyter`.

## <a name="supported-browser"></a>Browser supportati

I notebook di Jupyter nei cluster HDInsight Spark sono supportati solo su Google Chrome.

## <a name="feedback"></a>Commenti e suggerimenti

I nuovi kernel sono ancora in una fase iniziale e si evolveranno nel tempo. Questo potrebbe comportare un cambiamento delle API con l'evoluzione dei kernel. Sono graditi commenti e suggerimenti in merito all'uso di questi nuovi kernel. Saranno molto utili per la progettazione della versione finale di questi kernel. È possibile lasciare commenti o suggerimenti sotto il **commenti e suggerimenti** nella parte inferiore di questo articolo.

## <a name="seealso"></a>Vedere anche
* [Panoramica: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenari
* [Apache Spark con BI: Eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](apache-spark-use-bi-tools.md)
* [Apache Spark con Machine Learning: usare Spark in HDInsight per analizzare la temperatura di un edificio con dati HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark con apprendimento automatico: usare Spark in HDInsight per stimare i risultati di controllo degli alimenti](apache-spark-machine-learning-mllib-ipython.md)
* [Analisi dei log del sito Web con Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni
* [Creare un'applicazione autonoma con Scala](apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni
* [Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark in Scala](apache-spark-intellij-tool-plugin.md)
* [Usare il plug-in Strumenti HDInsight per IntelliJ IDEA per eseguire il debug di applicazioni Apache Spark in remoto](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare i notebook di Apache Zeppelin con un cluster Apache Spark in HDInsight](apache-spark-zeppelin-notebook.md)
* [Usare pacchetti esterni con i notebook Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestire risorse
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)
