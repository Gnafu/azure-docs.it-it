---
title: 'Azione script: installare pacchetti Python con Jupyter in Azure HDInsight'
description: Istruzioni dettagliate su come usare l’azione script e configurare notebook di Jupyter disponibili con cluster HDInsight Spark per l'uso di pacchetti Python esterni.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: ce5dc7e17020e1e4564ebe1f531645f7329718dc
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900711"
---
# <a name="script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-on-hdinsight"></a>Azione script per installare pacchetti Python esterni per notebook di Jupyter in Apache Spark in HDInsight

> [!div class="op_single_selector"]
> * [Uso di comandi Magic nelle celle](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Uso di azioni script](apache-spark-python-package-installation.md)

Informazioni su come usare le azioni script per configurare un cluster [Apache Spark](https://spark.apache.org/) in HDInsight per l'uso di pacchetti **Python** esterni creati dalla community che non sono inclusi nel cluster.

> [!NOTE]  
> È anche possibile configurare un notebook di Jupyter con il comando Magic `%%configure` per usare pacchetti esterni. Per istruzioni, vedere [Usare pacchetti esterni con notebook di Jupyter nei cluster Apache Spark in HDInsight](apache-spark-jupyter-notebook-use-external-packages.md).

Per un elenco completo dei pacchetti disponibili, è possibile eseguire una ricerca nell'[indice di pacchetto](https://pypi.python.org/pypi). È anche possibile ottenere un elenco dei pacchetti disponibili da altre origini. Per esempio, è possibile installare pacchetti resi disponibili tramite [conda-forge](https://conda-forge.org/feedstocks/).

Questo articolo descrive come installare il pacchetto [TensorFlow](https://www.tensorflow.org/) usando azioni script nel cluster e come usarlo ad esempio tramite notebook Jupyter.

## <a name="prerequisites"></a>Prerequisiti
È necessario disporre di quanto segue:

* Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]  
   > Se non dispone di un cluster Spark in HDInsight Linux, è possibile eseguire le azioni script durante la creazione del cluster. Vedere la documentazione su [come usare azioni script personalizzate](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).
   
## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Supporto per software open source usato nei cluster HDInsight

Il servizio Microsoft Azure HDInsight usa un ecosistema di tecnologie open source ispirate ad Apache Hadoop. Microsoft Azure offre un livello di supporto generale per le tecnologie open source. Per altre informazioni, vedere la sezione **Ambito del supporto** nel [sito Web delle domande frequenti sul supporto tecnico di Azure](https://azure.microsoft.com/support/faq/). Il servizio HDInsight offre un livello di supporto aggiuntivo per i componenti predefiniti.

Nel servizio HDInsight sono disponibili due tipi di componenti open source:

* **Componenti predefiniti** - Questi componenti sono preinstallati nei cluster HDInsight e forniscono la funzionalità di base del cluster. Per esempio, a questa categoria appartengono Apache Hadoop YARN ResourceManager, il linguaggio di query Apache Hive (HiveQL) e la libreria Mahout. L'elenco completo dei componenti del cluster è disponibile in [Novità delle versioni del cluster Apache Hadoop incluse in HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).
* **Componenti personalizzati** - Un utente del cluster può installare o usare nel carico di lavoro qualsiasi componente disponibile nella community o creato da lui stesso.

> [!IMPORTANT]   
> I componenti forniti con il cluster HDInsight sono completamente supportati. Il supporto tecnico Microsoft aiuta a isolare e risolvere i problemi legati a tali componenti.
>
> I componenti personalizzati ricevono supporto commercialmente ragionevole per semplificare la risoluzione dei problemi. Il supporto tecnico Microsoft potrebbe essere in grado di risolvere il problema OPPURE richiedere di usare i canali disponibili per le tecnologie open source, in cui è possibile ottenere supporto estremamente competente per la tecnologia specifica. Per esempio, è possibile ricorrere a molti siti di community, come: il [forum MSDN per HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Anche per i progetti Apache sono disponibili siti specifici in [https://apache.org](https://apache.org), per esempio: [Hadoop](https://hadoop.apache.org/).


## <a name="use-external-packages-with-jupyter-notebooks"></a>Usare pacchetti esterni con i notebook Jupyter

1. Dal [portale di Azure](https://portal.azure.com/)passare al cluster.  

2. Con il cluster selezionato, nel riquadro sinistro in **Impostazioni**selezionare **azioni script**.

3. Selezionare **+ Invia nuovo**.

4. Immettere i valori seguenti per la finestra **Invia azione script** :  


    |Parametro | Value |
    |---|---|
    |Tipo di script | Selezionare **- Custom** dall'elenco a discesa.|
    |NOME |Immettere `tensorflow` nella casella di testo.|
    |URI script Bash |Immettere `https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh` nella casella di testo. |
    |Tipo/i di nodo | Selezionare le caselle di controllo **Head**e **Worker** . |

    `tensorflowinstall.sh`contiene i comandi seguenti:

    ```bash
    #!/usr/bin/env bash
    /usr/bin/anaconda/bin/conda install --yes tensorflow
    ```

5. Selezionare **Create**.  Vedere la documentazione su [come usare azioni script personalizzate](../hdinsight-hadoop-customize-cluster-linux.md).

6. Attendere il completamento dello script.  Il riquadro **azioni script** determinerà l' **invio di nuove azioni script al termine dell'operazione del cluster corrente** durante l'esecuzione dello script.  È possibile visualizzare un indicatore di stato dalla finestra **delle operazioni in background** dell'interfaccia utente di Ambari.

7. Aprire un notebook di Jupyter PySpark.  Per la procedura, vedere [creare un notebook di Jupyter in Spark HDInsight](./apache-spark-jupyter-notebook-kernels.md#create-a-jupyter-notebook-on-spark-hdinsight) .

    ![Creare un nuovo notebook Jupyter](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "Creare un nuovo notebook Jupyter")

8. A questo punto verranno eseguiti il codice relativo a `import tensorflow` e l'esempio di tipo hello world. Immettere il codice seguente:

    ```
    import tensorflow as tf
    hello = tf.constant('Hello, TensorFlow!')
    sess = tf.Session()
    print(sess.run(hello))
    ```

    Il risultato sarà analogo a questo:
    
    ![Esecuzione del codice TensorFlow](./media/apache-spark-python-package-installation/tensorflow-execution.png "Esecuzione del codice TensorFlow")

> [!NOTE]  
> Nel cluster sono presenti due installazioni di Python. Spark userà l'installazione di Anaconda python che si trova in `/usr/bin/anaconda/bin` e l'impostazione predefinita per l'ambiente Python 2.7. Per usare Python 3.x e installare i pacchetti nel kernel di PySpark3, usare il percorso all'`conda`eseguibile per tale ambiente e usare il parametro `-n` per specificare l'ambiente. Ad esempio, il comando `/usr/bin/anaconda/envs/py35/bin/conda install -c conda-forge ggplot -n py35`, consente di installare il pacchetto `ggplot` nell'ambiente Python 3.5 usando il canale `conda-forge`.

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
* [Usare pacchetti esterni con notebook di Jupyter nei cluster Apache Spark in HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark in Scala](apache-spark-intellij-tool-plugin.md)
* [Usare il plug-in Strumenti HDInsight per IntelliJ IDEA per eseguire il debug di applicazioni Apache Spark in remoto](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare i notebook di Apache Zeppelin con un cluster Apache Spark in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Apache Spark per HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestire risorse
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)
