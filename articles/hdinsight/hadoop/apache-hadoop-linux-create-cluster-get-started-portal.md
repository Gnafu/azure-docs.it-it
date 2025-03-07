---
title: 'Guida introduttiva: Apache Hadoop e Apache Hive in HDInsight con il portale di Azure'
description: In questo argomento di avvio rapido si userà il portale di Azure per creare un cluster Hadoop in HDInsight.
keywords: introduzione a Hadoop, Hadoop basato su Linux, guida introduttiva a Hadoop, introduzione a Hive, guida introduttiva a Hive
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: quickstart
ms.date: 07/02/2019
ms.author: hrasheed
ms.openlocfilehash: be7ff67f07ed8eaeb3f04a15c6185191bf107cf2
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918454"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Guida introduttiva: Creare cluster Apache Hadoop in Azure HDInsight usando il portale di Azure

Questo articolo illustra come creare cluster [Apache Hadoop](https://hadoop.apache.org/) in HDInsight con il portale di Azure e quindi eseguire processi Apache Hive in HDInsight. La maggior parte dei processi Hadoop è costituita da processi batch. Viene creato un cluster, si eseguono alcuni processi e quindi si elimina il cluster. In questo articolo vengono eseguite tutte e tre le attività.

In questa guida di avvio rapido si userà il portale di Azure per creare un cluster Hadoop in HDInsight. È possibile creare un cluster usando il [modello di Azure Resource Manager](apache-hadoop-linux-tutorial-get-started.md).

HDInsight attualmente viene fornito con [sette diversi tipi di cluster](../hdinsight-overview.md#cluster-types-in-hdinsight). Ogni tipo di cluster supporta un set diverso di componenti. Tutti i tipi di cluster supportano Hive. Per un elenco dei componenti supportati in HDInsight, vedere [Novità delle versioni cluster di Apache Hadoop incluse in HDInsight](../hdinsight-component-versioning.md)  

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-an-apache-hadoop-cluster"></a>Creare un cluster Apache Hadoop

In questa sezione viene creato un cluster Hadoop in HDInsight usando il portale di Azure.

1. Accedere al [Portale di Azure](https://portal.azure.com).

1. Dal portale di Azure passare a **Crea una risorsa** > **Analisi** > **HDInsight**.

    ![Databricks nel portale di Azure](./media/apache-hadoop-linux-create-cluster-get-started-portal/create-hdinsight-cluster.png "Databricks nel portale di Azure")

1. In **HDInsight** > **Creazione rapida** > **Impostazioni di base** immettere o selezionare i valori seguenti:

    |Proprietà  |DESCRIZIONE  |
    |---------|---------|
    |Nome cluster   | Immettere un nome per il cluster Hadoop. Poiché tutti i cluster in HDInsight condividono lo stesso spazio dei nomi DNS, è necessario che questo nome sia univoco. Il nome può includere al massimo 59 caratteri, tra cui lettere, numeri e trattini. Si noti che il primo e l'ultimo carattere del nome non possono essere trattini. |
    |Subscription    |  Selezionare la sottoscrizione di Azure. |
    |Tipo di cluster     | Ignora per ora. I valori saranno inseriti nel passaggio successivo di questa procedura.|
    |Nome utente e password di accesso del cluster    | Il nome di accesso predefinito è **admin**. La password deve avere una lunghezza minima di 10 caratteri e deve contenere almeno una cifra, una lettera maiuscola, una lettera minuscola e un carattere non alfanumerico, ad eccezione di ' " ` \). Assicurarsi di **non fornire** password comuni, ad esempio "Pass@word1".|
    |Nome utente Secure Shell (SSH) | Il nome utente predefinito è **sshuser**.  È possibile fornire un altro nome come nome utente SSH. |
    |Usare la password di accesso del cluster per SSH| Selezionare questa casella di controllo se si vuole usare per l'utente SSH la stessa password fornita per l'utente di accesso del cluster.|
    |Resource group     | Creare un gruppo di risorse o selezionarne uno esistente.  Un gruppo di risorse è un contenitore di componenti di Azure.  In questo caso, il gruppo di risorse contiene il cluster HDInsight e l'account di Archiviazione di Azure dipendente. |
    |Location    | Selezionare una posizione di Azure in cui si vuole creare il cluster.  Scegliere una località vicina all'utente per ottenere prestazioni migliori. |

    ![HDInsight Linux guida introduttiva fornisce i valori di base per il cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/quick-create-basics1.png "Fornire i valori di base per la creazione di un cluster HDInsight")

1. Selezionare **Tipo di cluster** per aprire la pagina **Configurazione cluster** e quindi specificare i valori seguenti:

    |Proprietà  |DESCRIZIONE  |
    |---------|---------|
    |Tipo di cluster     | Selezionare **Hadoop** |
    |Versione     | Selezionare **Hadoop 2.7.3 (HDI 3.6)**|

    ![Introduzione di HDInsight Linux alla configurazione del cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-configuration-hadoop.png "Introduzione di HDInsight Linux alla configurazione del cluster")

    Fare clic su **Seleziona** e quindi scegliere **Avanti** per passare alle impostazioni di archiviazione.

1. Nella scheda **Archiviazione** specificare i valori seguenti:

    |Proprietà  |DESCRIZIONE  |
    |---------|---------|
    |Tipo di archiviazione primario    | Per questo articolo selezionare Archiviazione di Azure per BLOB del servizio di archiviazione di Azure come account di archiviazione predefinito. Si può anche usare Azure Data Lake Storage come risorsa di archiviazione predefinita. |
    |Metodo di selezione     |  Per questo articolo selezionare **Sottoscrizioni personali** per usare un account di archiviazione dalla sottoscrizione di Azure. Per usare un account di archiviazione di altre sottoscrizioni, selezionare **Chiave di accesso** e quindi specificare la chiave di accesso per quell'account. |
    |Selezionare un account di archiviazione   | Fare clic su **Selezionare un account di archiviazione** per scegliere un account di archiviazione esistente oppure su **Crea nuovo**. Se si crea un nuovo account, il nome deve avere una lunghezza compresa tra 3 e 24 caratteri e può contenere solo numeri e lettere minuscole.|

    Accettare tutti gli altri valori predefiniti e quindi scegliere **Avanti** per passare alla pagina di riepilogo.

    ![HDInsight Linux guida introduttiva fornisce i valori di archiviazione per il cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/quick-create-storage.png "Fornire i valori di archiviazione per la creazione di un cluster HDInsight")

1. Nella scheda **Riepilogo** verificare i valori selezionati nei passaggi precedenti.

    ![HDInsight Linux guida introduttiva riepilogo del cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/quick-create-summary.png "HDInsight Linux guida introduttiva riepilogo del cluster")

1. Selezionare **Create** (Crea). La creazione di un cluster richiede circa 20 minuti.

1. Dopo la creazione del cluster, compare la pagina di panoramica cluster nel portale di Azure.

    ![Impostazioni iniziali del cluster HDInsight basato su Linux](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-settings-overview.png "Proprietà del cluster HDInsight")    

    Ogni cluster ha una dipendenza da un [account di archiviazione di Azure](../hdinsight-hadoop-use-blob-storage.md) o da un [account Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). Viene indicato come account di archiviazione predefinito. Il cluster HDInsight e l'account di archiviazione predefinito devono avere un percorso condiviso nella stessa area di Azure. L'eliminazione dei cluster non comporta l'eliminazione dell'account di archiviazione.

    > [!NOTE]  
    > Per altri metodi di creazione di cluster e per informazioni sulle proprietà usate in questo argomento di avvio rapido, vedere [Creare cluster HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="run-apache-hive-queries"></a>Eseguire query Apache Hive

[Apache Hive](hdinsight-use-hive.md) è il componente più diffuso usato in HDInsight. Esistono diversi modi per eseguire processi Hive in HDInsight. In questo argomento di avvio rapido si usa la visualizzazione Hive di Ambari dal portale. Per altri metodi di esecuzione di processi Hive, vedere [Usare Hive in HDInsight](hdinsight-use-hive.md).

1. Per aprire Ambari, nello screenshot precedente selezionare **Dashboard cluster**.  È anche possibile accedere a `https://ClusterName.azurehdinsight.net`, dove `ClusterName` è il cluster creato nella sezione precedente.

    ![Guida introduttiva di HDInsight Linux al dashboard del cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-open-cluster-dashboard.png "Guida introduttiva di HDInsight Linux al dashboard del cluster")

2. Immettere il nome utente e la password Hadoop specificati durante la creazione del cluster. Il nome utente predefinito è **admin**.

3. Aprire la **visualizzazione Hive** come illustrato nella schermata seguente:

    ![Selezione delle visualizzazioni di Ambari](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-select-hive-view.png "Menu di visualizzazione di Hive di HDInsight")

4. Nella scheda **QUERY** incollare le istruzioni HiveQL seguenti nel foglio di lavoro:

    ```sql
    SHOW TABLES;
    ```

    ![Visualizzazioni Hive di HDInsight](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-apache-hive-view1.png "Editor di query della visualizzazione Hive di HDInsight")

5. Scegliere **Execute**(Esegui). Viene visualizzata una scheda **RESULTS** (RISULTATI) sotto la scheda **QUERY** e vengono visualizzate informazioni sul processo. 

    Al termine dell'elaborazione della query, nella scheda **QUERY** vengono visualizzati i risultati dell'operazione. Verrà visualizzata una tabella denominata **hivesampletable**. Questa tabella Hive di esempio è disponibile in tutti i cluster HDInsight.

    ![Visualizzazioni dei risultati di Hive di HDInsight](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-hive-views.png "Visualizzazione dei risultati di Hive di HDInsight")

6. Ripetere i passaggi 4 e 5 per eseguire questa query:

    ```sql
    SELECT * FROM hivesampletable;
    ```

7. È anche possibile salvare i risultati della query. Selezionare il pulsante del menu a destra e specificare se si vuole scaricare i risultati come file CSV o archiviarli nell'account di archiviazione associato al cluster.

    ![Salvare il risultato di una query Hive](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-hive-view-save-results.png "Salvare il risultato di una query Hive")

Dopo aver completato un processo Hive, è possibile [esportare i risultati in un database SQL di Azure o in un database di SQL Server](apache-hadoop-use-sqoop-mac-linux.md). È anche possibile [visualizzare i risultati in Excel](apache-hadoop-connect-excel-power-query.md). Per altre informazioni sull'uso di Hive in HDInsight, vedere [Usare Apache Hive e HiveQL con Apache Hadoop in HDInsight per analizzare un file Apache log4j di esempio](hdinsight-use-hive.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'argomento di avvio rapido, può essere opportuno eliminare il cluster. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non viene usato. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, economicamente ha senso eliminare i cluster quando non vengono usati.

> [!NOTE]  
> Se si procede *subito* con l'articolo successivo per imparare come eseguire le operazioni ETL mediante Hadoop in HDInsight, è possibile mantenere il cluster in esecuzione, poiché nell'esercitazione è necessario creare nuovamente un cluster Hadoop. Se invece non si prevede di passare subito all'articolo successivo, è necessario eliminare il cluster ora.

### <a name="to-delete-the-cluster-andor-the-default-storage-account"></a>Per eliminare il cluster e/o l'account di archiviazione predefinito

1. Tornare alla scheda del browser in cui è visualizzato il portale di Azure. Occorre visualizzare la pagina di panoramica del cluster. Se si vuole solo eliminare il cluster ma conservare l'account di archiviazione predefinito, scegliere **Elimina**.

    ![Eliminazione del cluster HDInsight](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-delete-cluster.png "Eliminazione del cluster HDInsight")

2. Se si intende eliminare il cluster, nonché l'account di archiviazione predefinito, selezionare il nome del gruppo di risorse (evidenziato nello screenshot precedente) per aprire la pagina di gruppo di risorse.

3. Selezionare **Elimina gruppo di risorse** per eliminare il gruppo di risorse che contiene il cluster e l'account di archiviazione predefinito. Si noti che l'eliminazione del gruppo di risorse comporta l'eliminazione dell'account di archiviazione. Se si vuole mantenere l'account di archiviazione, scegliere di eliminare solo il cluster.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come creare un cluster HDInsight basato su Linux usando un modello di Resource Manager ed eseguire query Hive di base. Passare all'articolo successivo per informazioni su come eseguire un'operazione di estrazione, trasformazione e caricamento (ETL) usando Hadoop in HDInsight.

> [!div class="nextstepaction"]
>[Estrarre, trasformare e caricare dati usando Interactive Query su HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
