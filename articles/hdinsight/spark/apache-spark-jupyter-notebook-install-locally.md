---
title: Installare Jupyter localmente e connetterlo a Spark in Azure HDInsight
description: Informazioni su come installare il notebook di Jupyter in locale nel computer e su come connetterlo a un cluster Apache Spark.
ms.service: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: hrasheed
ms.openlocfilehash: 6ce3ff8e00bc92911a7405de1bb0bb7286fb5c15
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70993773"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Installare un notebook di Jupyter in locale e connetterlo ad Apache Spark in HDInsight

Questo articolo illustra come installare Jupyter Notebook, insieme ai kernel personalizzati PySpark (per Python) e Apache Spark (per Scala) e al magic Spark, e come connettere l'applicazione a un cluster HDInsight. L'installazione di Jupyter nel computer locale può essere dettata da molti motivi e può anche presentare alcuni problemi. Per altre informazioni, vedere la sezione [Perché installare Jupyter nel computer locale](#why-should-i-install-jupyter-on-my-computer) alla fine di questo articolo.

Sono necessari quattro passaggi principali per l'installazione di Jupyter e la connessione a Apache Spark in HDInsight.

* Configurare il cluster Spark.
* Installare Jupyter Notebook.
* Installare i kernel PySpark e Spark con il magic Spark.
* Configurare il magic Spark per l'accesso al cluster Spark in HDInsight.

Per altre informazioni sui kernel personalizzati e su Spark magic disponibili per Jupyter Notebook con il cluster HDInsight, vedere [Kernel disponibili per Jupyter Notebook con cluster HDInsight Spark Linux su HDInsight](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Prerequisiti

I prerequisiti elencati di seguito non riguardano l'installazione di Jupyter. Riguardano invece la connessione di Jupyter Notebook a un cluster HDInsight dopo l'installazione.

* Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Installare Jupyter Notebook nel computer

Prima di installare i notebook Jupyter è necessario installare Python. La [distribuzione anaconda](https://www.anaconda.com/download/) installerà sia, Python che Jupyter notebook.

Scaricare il [programma di installazione di Anaconda](https://www.anaconda.com/download/) per la piattaforma in uso ed eseguirlo. Quando si esegue l'installazione guidata, assicurarsi di selezionare l'opzione per l'aggiunta di Anaconda alla variabile PATH.  Vedere anche [installazione di Jupyter con Anaconda](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-spark-magic"></a>Installare Spark Magic

1. Immettere uno dei comandi seguenti per installare Spark Magic. Vedere anche la [documentazione di sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation).

    |Versione cluster | Comando di installazione |
    |---|---|
    |v 3.6 e v 3.5 |`pip install sparkmagic==0.12.7`|
    |v 3.4|`pip install sparkmagic==0.2.3`|

1. Assicurarsi `ipywidgets` che sia installato correttamente eseguendo il comando seguente:

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>Installare i kernel PySpark e Spark

1. Identificare la `sparkmagic` posizione in cui è installato immettendo il comando seguente:

    ```cmd
    pip show sparkmagic
    ```

    Modificare quindi la directory di lavoro nel percorso identificato con il comando precedente.

1. Dalla nuova directory di lavoro, immettere uno o più dei comandi seguenti per installare i kernel desiderati:

    |Kernel | Comando |
    |---|---|
    |Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |SparkR|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. facoltativo. Immettere il comando seguente per abilitare l'estensione del server:

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Configurare il magic Spark per la connessione al cluster HDInsight Spark

In questa sezione viene configurata la magia di Spark installata in precedenza per connettersi a un cluster Apache Spark.

1. Avviare la shell di Python con il comando seguente:

    ```cmd
    python
    ```

2. Le informazioni di configurazione di Jupyter sono in genere archiviate nella home directory dell'utente. Immettere il comando seguente per identificare la home directory e creare una cartella denominata **. sparkmagic**.  Il percorso completo sarà output.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. All'interno della `.sparkmagic`cartella, creare un file denominato **config. JSON** e aggiungere il frammento di codice JSON seguente al suo interno.  

    ```json
    {
      "kernel_python_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "kernel_scala_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. Apportare le modifiche seguenti al file:

    |Valore del modello | Nuovo valore |
    |---|---|
    |NOME UTENTE|Accesso al cluster. il `admin`valore predefinito è.|
    |{CLUSTERDNSNAME}|Nome cluster|
    |{BASE64ENCODEDPASSWORD}|Password con codifica Base64 per la password effettiva.  È possibile generare una password base64 all' [https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/)indirizzo.|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Usare se si `sparkmagic 0.12.7` USA (cluster v 3.5 e v 3.6).  Se si `sparkmagic 0.2.3` USA (cluster v 3.4), sostituire con `"should_heartbeat": true`.|

    È possibile visualizzare un file di esempio completo in [config. JSON di esempio](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

   > [!TIP]  
   > Gli heartbeat vengono inviati per assicurare che le sessioni non vengano perse. Quando un computer va in sospensione o viene arrestato, l'heartbeat non verrà inviato e la sessione verrà quindi eliminata. Per disabilitare questo comportamento per i cluster v3.4, è possibile impostare la configurazione di Livy `livy.server.interactive.heartbeat.timeout` su `0` dall'interfaccia utente di Ambari. Per i cluster v3.5, se non si imposta la configurazione 3.5 precedente, la sessione non verrà eliminata.

5. Avviare Jupyter. Usare il comando seguente dal prompt dei comandi.

    ```cmd
    jupyter notebook
    ```

6. Verificare che sia possibile usare il Magic Spark disponibile con i kernel. Eseguire i passaggi seguenti.

    a. Creare un nuovo notebook. Nell'angolo destro selezionare **nuovo**. Verranno visualizzati il kernel predefinito **Python 2** o **Python 3** e i kernel installati. I valori effettivi possono variare a seconda delle scelte di installazione.  Selezionare **PySpark**.

    ![Kernel nel notebook di Jupyter](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png "Kernel nel notebook di Jupyter")

    > [!IMPORTANT]  
    > Dopo aver selezionato **nuovo** verificare la Shell per individuare eventuali errori.  Se viene visualizzato l'errore `TypeError: __init__() got an unexpected keyword argument 'io_loop'` è possibile che si verifichi un problema noto con determinate versioni di tornado.  In tal caso, arrestare il kernel e quindi eseguire il downgrade dell'installazione di Tornado con `pip install tornado==4.5.3`il comando seguente:.

    b. Eseguire il frammento di codice seguente.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Se è stato possibile recuperare l'output, viene verificata la connessione al cluster HDInsight.

    Se si vuole aggiornare la configurazione del notebook per connettersi a un cluster diverso, aggiornare il file config. JSON con il nuovo set di valori, come illustrato nel passaggio 3, sopra.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Perché installare Jupyter nel computer locale

Esistono diversi motivi per cui è consigliabile installare Jupyter nel computer in uso e quindi connetterlo a un cluster Apache Spark in HDInsight.

* Anche se i notebook Jupyter sono già disponibili nel cluster Spark in Azure HDInsight, l'installazione di Jupyter nel computer offre la possibilità di creare i notebook in locale, testare l'applicazione con un cluster in esecuzione e quindi caricare i notebook nel cluster. È possibile caricare i notebook nel cluster usando Jupyter Notebook già in esecuzione sul cluster oppure salvare i notebook nella cartella /HdiNotebooks nell'account di archiviazione associato al cluster. Per altre informazioni sul modo in cui i notebook vengono archiviati nel cluster, vedere la sezione [Dove sono archiviati i notebook?](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)
* Con i notebook disponibili in locale è possibile connettersi a cluster Spark diversi in base alle esigenze dell'applicazione.
* È possibile usare GitHub per implementare un sistema di controllo del codice sorgente e usare il controllo della versione per il notebook. È anche possibile avere a disposizione un ambiente di collaborazione in cui più utenti possono lavorare allo stesso notebook.
* È possibile lavorare con i notebook in locale anche senza avere un cluster. È necessario avere un cluster solo per eseguire i test dei notebook, non per gestire manualmente i notebook o un ambiente di sviluppo.
* Può essere più semplice configurare il proprio ambiente di sviluppo locale che configurare l'installazione di Jupyter nel cluster.  È possibile sfruttare tutto il software installato localmente senza configurare uno o più cluster remoti.

> [!WARNING]  
> Con Jupyter installato nel computer locale più utenti possono eseguire contemporaneamente lo stesso notebook nello stesso cluster Spark. In questo caso, vengono create più sessioni di Livy. Se si verifica un problema e si vuole eseguire il debug, tenere traccia della sessione di Livy che appartiene l'utente sarà un'attività complessa.  

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica: Apache Spark in Azure HDInsight](apache-spark-overview.md)
* [Apache Spark con BI: Eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](apache-spark-use-bi-tools.md)
* [Apache Spark con Machine Learning: usare Spark in HDInsight per analizzare la temperatura di un edificio con dati HVAC](apache-spark-ipython-notebook-machine-learning.md)