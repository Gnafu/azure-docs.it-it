---
title: 'Guida introduttiva: Creare un cluster Spark in HDInsight usando il portale di Azure'
description: Questa guida introduttiva illustra come usare il portale di Azure per creare un cluster Apache Spark in HDInsight di Azure ed eseguire Spark SQL.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/12/2019
ms.custom: mvc
ms.openlocfilehash: 7e6c8bb10f0442cdb4e53e26d8d7a57d7bcb29d7
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814133"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-portal"></a>Guida introduttiva: Creare cluster Apache Spark in Azure HDInsight usando il portale di Azure

Informazioni su come creare un cluster Apache Spark in Azure HDInsight ed eseguire query Spark SQL sulle tabelle Hive. Apache Spark consente cluster computing e analisi dei dati veloci grazie all'elaborazione in memoria. Per informazioni su Spark in HDInsight, vedere [Panoramica: Apache Spark in Azure HDInsight](apache-spark-overview.md).

In questa guida introduttiva si userà il portale di Azure per creare un cluster Spark in HDInsight. Il cluster usa i BLOB del servizio di archiviazione di Azure come archiviazione cluster. Per altre informazioni sull'uso di Data Lake Storage Gen2, vedi [Avvio rapido: Impostazione dei cluster in HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)

> [!IMPORTANT]  
> La fatturazione dei cluster HDInsight viene calcolata al minuto, indipendentemente dal fatto che siano in uso o meno. Assicurarsi di eliminare il cluster dopo aver finito di usarlo. Per altre informazioni, vedere la sezione [Pulire le risorse](#clean-up-resources) di questo articolo.

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-an-hdinsight-spark-cluster"></a>Creare un cluster HDInsight Spark

1. Nel portale di Azure selezionare **Crea una risorsa** > **Analisi** > **HDInsight**. 

    ![HDInsight nel portale di Azure](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-hdinsight-spark-cluster.png "HDInsight nel portale di Azure")
2. In **Informazioni di base** specificare i valori seguenti:
     
    |Proprietà  |DESCRIZIONE  |
    |---------|---------|
    |**Nome cluster**     | Assegnare un nome al cluster HDInsight Spark. Il nome del cluster usato per questa guida introduttiva è **myspark20180403**.|
    |**Sottoscrizione**     | Nell'elenco a discesa selezionare la sottoscrizione di Azure usata per questo cluster. La sottoscrizione di Azure usata in questa guida introduttiva è **&lt;Azure subscription**. |
    |**Tipo di cluster**| Espandere l'elemento e quindi selezionare **Spark** come tipo di cluster e specificare la versione del cluster Spark. <br/> <img src="./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-hdinsight-spark-cluster-type.png" alt = "Select HDInsight cluster type" /> |
    |**Nome utente dell'account di accesso del cluster**| Immettere il nome utente dell'account di accesso del cluster.  Il nome predefinito è *admin*. Si userà questo account per accedere al notebook di Jupyter più avanti nella guida introduttiva. |
    |**Password di accesso al cluster**| Immettere la password di accesso del cluster. |
    |**Nome utente Secure Shell (SSH)**| Immettere il nome utente SSH. Il nome utente SSH usato per questa guida introduttiva è **sshuser**. Per impostazione predefinita, questo account condivide la stessa password dell'account *Nome utente dell'account di accesso del cluster*. |
    |**Gruppo di risorse**     | Specificare se si vuole creare un nuovo gruppo di risorse o usarne uno esistente. Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. Il nome del gruppo di risorse usato per questa guida introduttiva è **myspark20180403rg**. |
    |**Posizione**     | Selezionare una posizione per il gruppo di risorse. Il modello usa questa posizione per la creazione del cluster e per l'archiviazione del cluster predefinita. La posizione usata in questa guida introduttiva è **Stati Uniti orientali 2**. |

    ![Creare le configurazioni di base del cluster HDInsight Spark](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-hdinsight-spark-cluster-basic2.png "Creare le configurazioni di base del cluster HDInsight Spark")

    Selezionare **Avanti** per passare alla pagina **Archiviazione**.
3. In **Archiviazione** specificare i valori seguenti:

   - **Selezionare un account di archiviazione**: selezionare **Crea nuovo** e quindi assegnare un nome al nuovo account di archiviazione. Il nome dell'account di archiviazione usato per questa guida introduttiva è **myspark20180403store**.

     ![Creare le configurazioni di archiviazione del cluster HDInsight Spark](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-hdinsight-spark-cluster-storage.png "Creare le configurazioni archiviazione del cluster HDInsight Spark")

     > [!NOTE]  
     > Nello screenshot è visualizzato **Seleziona esistente**. Il collegamento alterna tra **Crea nuovo** e **Seleziona esistente**.

     Il **contenitore predefinito** ha un nome predefinito.  Se si vuole, è possibile cambiare il nome.

     Selezionare **Avanti** per passare alla pagina **Riepilogo**. 


3. In **Riepilogo** selezionare **Crea**. La creazione del cluster richiede circa 20 minuti. Prima di procedere con la sessione successiva, è necessario creare il cluster.

Se si verifica un problema con la creazione di cluster HDInsight, è possibile che non si abbiano le autorizzazioni necessarie per questa operazione. Per altre informazioni, vedere [Requisiti di controllo di accesso](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="create-a-jupyter-notebook"></a>Creare un notebook Jupyter

Jupyter Notebook è un ambiente notebook interattivo che supporta diversi linguaggi di programmazione. Il notebook consente di interagire con i dati, combinare codice e testo Markdown ed eseguire visualizzazioni semplici. 

1. Aprire il [portale di Azure](https://portal.azure.com).
2. Selezionare **cluster HDInsight** e quindi selezionare il cluster creato.

    ![Aprire il cluster HDInsight nel portale di Azure](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. Nel portale fare clic su **Dashboard cluster** e quindi su **Jupyter Notebook**. Se richiesto, immettere le credenziali di accesso del cluster.

   ![Aprire Jupyter Notebook per eseguire la query Spark SQL interattiva](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Aprire Jupyter Notebook per eseguire la query Spark SQL interattiva")

4. Per creare un notebook selezionare **Nuovo** > **PySpark**. 

   ![Creare un oggetto Jupyter Notebook per eseguire una query Spark SQL interattiva](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Creare un oggetto Jupyter Notebook per eseguire una query Spark SQL interattiva")

   Un nuovo notebook verrà creato e aperto con il nome Untitled (Untitled.pynb).


## <a name="run-spark-sql-statements"></a>Eseguire le istruzioni Spark SQL

SQL (Structured Query Language) è il linguaggio più diffuso e più usato per l'esecuzione di query e la definizione dei dati. Spark SQL funziona come estensione di Apache Spark per l'elaborazione dei dati strutturati, usando la nota sintassi SQL.

1. Verificare che il kernel sia pronto. Il kernel è pronto quando accanto al relativo nome nel notebook viene visualizzato un cerchio vuoto. Un cerchio pieno indica che il kernel è occupato.

    ![Query Hive in HDInsight Spark1](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Query Hive in HDInsight Spark1")

    Quando si avvia il notebook per la prima volta, il kernel esegue alcune attività in background. Attendere che il kernel sia pronto. 
2. Incollare il codice seguente in una cella vuota e quindi premere **MAIUSC + INVIO** per eseguire il codice. Il comando elenca le tabelle Hive sul cluster:

    ```PySpark
    %%sql
    SHOW TABLES
    ```
    Quando si usa un oggetto Jupyter Notebook con il cluster HDInsight Spark, si ottiene un elemento `sqlContext` predefinito che può essere usato per eseguire query Hive con Spark SQL. `%%sql` indica a Jupyter Notebook di usare l'elemento `sqlContext` predefinito per eseguire la query Hive. La query recupera le prime 10 righe di una tabella Hive (**hivesampletable**) disponibile per impostazione predefinita in tutti i cluster HDInsight. Per ottenere i risultati sono necessari circa 30 secondi. L'output è simile al seguente: 

    ![Query Hive in HDInsight Spark2](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Query Hive in HDInsight Spark2")

    Ogni volta che si esegue una query in Jupyter, il titolo della finestra del Web browser visualizza lo stato **(Occupato)** accanto al titolo del notebook. È anche visibile un cerchio pieno accanto al testo **PySpark** nell'angolo in alto a destra.
    
2. Eseguire un'altra query per visualizzare i dati in `hivesampletable`.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```
    
    La schermata si aggiornerà per visualizzare l'output della query.

    ![Output della query Hive in HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Output della query Hive in HDInsight Spark")

2. Nel menu **File** del notebook fare clic su **Close and Halt** (Chiudi e interrompi). Quando il notebook viene arrestato, le risorse del cluster vengono rilasciate.

## <a name="clean-up-resources"></a>Pulire le risorse
HDInsight salva i dati in Archiviazione di Azure o in Azure Data Lake Storage in modo che sia possibile eliminare senza problemi un cluster quando non viene usato. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, economicamente ha senso eliminare i cluster quando non vengono usati. Se si prevede di svolgere subito l'esercitazione elencata nei [passaggi successivi](#next-steps), si può mantenere il cluster.

Tornare al portale di Azure e selezionare **Elimina**.

![Eliminare un cluster HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "Eliminare cluster HDInsight")

È anche possibile selezionare il nome del gruppo di risorse per aprire la pagina del gruppo di risorse e quindi selezionare **Elimina gruppo di risorse**. Eliminando il gruppo di risorse, si elimina sia il cluster HDInsight Spark che l'account di archiviazione predefinito.

## <a name="next-steps"></a>Passaggi successivi 

In questa guida introduttiva si è appreso come creare un cluster HDInsight Spark ed eseguire una query Spark SQL di base. Passare all'esercitazione successiva per imparare come usare un cluster HDInsight Spark per eseguire query interattive su dati di esempio.

> [!div class="nextstepaction"]
>[Eseguire query interattive su Apache Spark](./apache-spark-load-data-run-query.md)

