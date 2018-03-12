---
title: Introduzione a Hadoop e Hive in Azure HDInsight | Microsoft Docs
description: Informazioni su come creare cluster HDInsight ed eseguire query sui dati con Hive.
keywords: introduzione a Hadoop, Hadoop basato su Linux, guida introduttiva a Hadoop, introduzione a Hive, guida introduttiva a Hive
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6a12ed4c-9d49-4990-abf5-0a79fdfca459
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2018
ms.author: jgao
ms.openlocfilehash: 11c0a583ab671b27a5c5a65c4bb36032a8582466
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
# <a name="hadoop-tutorial-get-started-using-hadoop-in-hdinsight"></a>Esercitazione su Hadoop: Introduzione all'uso di Hadoop in HDInsight

Informazioni su come creare cluster [Hadoop](http://hadoop.apache.org/) in HDInsight e come eseguire processi Hive in HDInsight. [Apache Hive](https://hive.apache.org/) è il componente più diffuso dell'ecosistema Hadoop. HDInsight attualmente viene fornito con [sette diversi tipi di cluster](./apache-hadoop-introduction.md#cluster-types-in-hdinsight). Ogni tipo di cluster supporta un set diverso di componenti. Tutti i tipi di cluster supportano Hive. Per un elenco dei componenti supportati in HDInsight, vedere [Novità delle versioni cluster di Hadoop incluse in HDInsight](../hdinsight-component-versioning.md)  

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>prerequisiti
Prima di iniziare questa esercitazione, è necessario avere:

* **Sottoscrizione di Azure**: per creare un account di valutazione gratuito di un mese, passare ad [azure.microsoft.com/free](https://azure.microsoft.com/free).

## <a name="create-cluster"></a>Creare cluster

La maggior parte dei processi Hadoop è costituita da processi batch. Viene creato un cluster, si eseguono alcuni processi e quindi si elimina il cluster. In questa sezione viene creato un cluster Hadoop in HDInsight usando un [modello di Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md). Per questa esercitazione non è necessario conoscere il modello di Resource Manager. Per altri metodi di creazione di cluster e per informazioni sulle proprietà usate in questa esercitazione, vedere [Creare cluster HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

Il modello di Resource Manager usato in questa esercitazione è disponibile in [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/). 

1. Fare clic sull'immagine seguente per accedere ad Azure e aprire il modello di Resource Manager nel portale di Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Immettere o selezionare i valori seguenti:

    * **Sottoscrizione**: selezionare una sottoscrizione di Azure.
    * **Gruppo di risorse**: creare un gruppo di risorse o selezionarne uno esistente.  Un gruppo di risorse è un contenitore di componenti di Azure.  In questo caso, il gruppo di risorse contiene il cluster HDInsight e l'account di Archiviazione di Azure dipendente. 
    * **Località**: selezionare una località di Azure in cui si vuole creare il cluster.  Scegliere una località vicina all'utente per ottenere prestazioni migliori. 
    * **Tipo di cluster**: selezionare **hadoop** per questa esercitazione.
    * **Nome del cluster**: immettere un nome per il cluster Hadoop. Poiché tutti i cluster in HDInsight condividono lo stesso spazio dei nomi DNS, è necessario che questo nome sia univoco. Il nome può includere al massimo 59 caratteri, tra cui lettere, numeri e trattini. Si noti che il primo e l'ultimo carattere del nome non possono essere trattini.
    * **Cluster login name and password**: il nome dell'account di accesso predefinito è **admin**. La password deve avere una lunghezza minima di 10 caratteri e deve contenere almeno una cifra, una lettera maiuscola, una lettera minuscola e un carattere non alfanumerico, ad eccezione di ' " ` \). 
    * **SSH username and password**: il nome utente predefinito è **sshuser**.  È possibile rinominare il nome utente SSH.  Per la password utente SSH sono previsti gli stessi requisiti della password di accesso al cluster.   
   
    ![Modello iniziale di Resource Manager per HDInsight basato su Linux nel portale](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Distribuire cluster Hadoop in HDInsight usando il portale di Azure e un modello di gestione del gruppo di risorse").
     
    Alcune proprietà sono state impostate come hardcoded nel modello.  È possibile configurare questi valori dal modello.
    
    Ogni cluster ha una dipendenza da un [account di archiviazione di Azure](../hdinsight-hadoop-use-blob-storage.md) o da un [account Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). Viene indicato come account di archiviazione predefinito. Il cluster HDInsight e l'account di archiviazione predefinito devono avere un percorso condiviso nella stessa area di Azure. L'eliminazione dei cluster non comporta l'eliminazione dell'account di archiviazione. 
        
    Per una spiegazione più approfondita di queste proprietà, vedere l'articolo su come [create cluster Hadoop in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

3. Selezionare **Accetto le condizioni riportate sopra** e **Aggiungi al dashboard** e quindi fare clic su **Acquista**. Verrà visualizzato un nuovo riquadro denominato **Distribuzione di Distribuzione modello** nel dashboard del portale. La creazione di un cluster richiede circa 20 minuti. 

4. Dopo la creazione del cluster, la didascalia del riquadro viene cambiata nel nome del gruppo di risorse specificato. Fare clic sul riquadro per visualizzare il cluster e la risorsa di archiviazione predefinita per il cluster elencato.
   
    ![Gruppo di risorse iniziale in HDInsight basato su Linux](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-resource-group.png "Gruppo di risorse cluster in Azure HDInsight")

4. Fare clic sul nome del cluster per aprire il cluster.

   ![Impostazioni iniziali del cluster HDInsight basato su Linux](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-cluster-settings.png "Proprietà del cluster HDInsight")


## <a name="run-hive-queries"></a>Eseguire query Hive

[Apache Hive](hdinsight-use-hive.md) è il componente più diffuso usato in HDInsight. Esistono diversi modi per eseguire processi Hive in HDInsight. In questa esercitazione si usa la visualizzazione Hive di Ambari dal portale. Per altri metodi di esecuzione di processi Hive, vedere [Usare Hive in HDInsight](hdinsight-use-hive.md).

1. Per aprire Ambari, dalla schermata precedente fare clic su **Dashboard cluster** e quindi su **Dashboard cluster HDInsight**.  È inoltre possibile passare a **https://&lt;NomeCluster>.azurehdinsight.net**, dove &lt;NomeCluster> è il cluster creato nella sezione precedente.

2. Immettere il nome utente e la password Hadoop specificati durante la creazione del cluster. Il nome utente predefinito è **admin**.

3. Aprire la **visualizzazione Hive** come illustrato nella schermata seguente:
   
    ![Selezione delle visualizzazioni di Ambari](./media/apache-hadoop-linux-tutorial-get-started/selecthiveview.png "Menu di visualizzazione di Hive di HDInsight")

4. Nella scheda **QUERY** incollare le istruzioni HiveQL seguenti nel foglio di lavoro:
   
        SHOW TABLES;

    ![Visualizzazioni Hive di HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hiveview-1.png "Editor di query della visualizzazione Hive di HDInsight")
   
   > [!NOTE]
   > Il punto e virgola è obbligatorio per Hive.       
   > 
   > 

5. Fare clic su **Execute**. Viene visualizzata una scheda **RESULTS** (RISULTATI) sotto la scheda **QUERY** e vengono visualizzate informazioni sul processo. 
   
    Al termine dell'elaborazione della query, nella scheda **QUERY** vengono visualizzati i risultati dell'operazione. Verrà visualizzata una tabella denominata **hivesampletable**. Questa tabella Hive di esempio è disponibile in tutti i cluster HDInsight.
   
    ![Visualizzazioni Hive di HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hiveview.png "Editor di query della visualizzazione Hive di HDInsight")

6. Ripetere i passaggi 4 e 5 per eseguire questa query:
   
        SELECT * FROM hivesampletable;
   
7. È anche possibile salvare i risultati della query. Fare clic sul pulsante del menu a destra e specificare se si vogliono scaricare i risultati come file CSV o archiviarli nell'account di archiviazione associato al cluster.

    ![Salvare il risultato di una query Hive](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-hive-view-save-results.png "Salvare il risultato di una query Hive")



Dopo aver completato un processo Hive, è possibile [esportare i risultati in un database SQL di Azure o in un database di SQL Server](apache-hadoop-use-sqoop-mac-linux.md). È anche possibile [visualizzare i risultati in Excel](apache-hadoop-connect-excel-power-query.md). Per altre informazioni sull'uso di Hive in HDInsight, vedere [Usare Hive e HiveQL con Hadoop in HDInsight per analizzare un file Apache log4j di esempio](hdinsight-use-hive.md).

## <a name="clean-up-the-tutorial"></a>Eseguire la pulizia dell'esercitazione
Al termine dell'esercitazione, è consigliabile eliminare il cluster. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non viene usato. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, economicamente ha senso eliminare i cluster quando non vengono usati. 

> [!NOTE]
> [Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md) consente di creare cluster HDInsight su richiesta e di configurare un'impostazione TimeToLive per eliminare automaticamente i cluster. 
> 
> 

**Per eliminare il cluster e/o l'account di archiviazione predefinito**

1. Tornare alla scheda del browser in cui è visualizzato il portale di Azure. Occorre visualizzare la pagina di panoramica del cluster. È possibile fare clic su **Elimina** se si vuole solo eliminare il cluster ma conservare l'account di archiviazione predefinito:

    ![Eliminazione del cluster HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "Eliminazione del cluster HDInsight")
1. Fare clic a destra sul nome del gruppo di risorse, evidenziato nello screenshot precedente, per aprire la pagina del gruppo di risorse.
2. Fare clic su **Elimina gruppo di risorse** per eliminare il gruppo di risorse che include il cluster e l'account di archiviazione predefinito oppure fare clic sul nome del cluster nel riquadro **Risorse**e quindi su **Elimina**. Si noti che l'eliminazione del gruppo di risorse comporta l'eliminazione dell'account di archiviazione. Se si vuole mantenere l'account di archiviazione, scegliere di eliminare solo il cluster.

## <a name="troubleshoot"></a>Risolvere problemi

Se si verificano problemi di creazione dei cluster HDInsight, vedere i [requisiti dei controlli di accesso](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come creare un cluster HDInsight basato su Linux usando un modello di Resource Manager ed eseguire query Hive di base.

Per altre informazioni sull'analisi dei dati con HDInsight, vedere gli articoli seguenti:

* Per altre informazioni sull'uso di Hive con HDInsight, incluse le procedure per eseguire query Hive da Visual Studio, vedere [Usare Hive con HDInsight](hdinsight-use-hive.md).
* Per informazioni su Pig, un linguaggio usato per la trasformazione dei dati, vedere [Usare Pig con HDInsight](hdinsight-use-pig.md).
* Per altre informazioni su MapReduce, un framework software che consente di scrivere programmi per l'elaborazione dei dati in Hadoop, vedere [Usare MapReduce con HDInsight](hdinsight-use-mapreduce.md).
* Per altre informazioni sull'uso di HDInsight Tools per Visual Studio per analizzare i dati in HDInsight, vedere [Introduzione all'uso di Hadoop Tools per Visual Studio per HDInsight](apache-hadoop-visual-studio-tools-get-started.md).

Se si è pronti per iniziare a usare i dati, ma sono necessarie altre informazioni sulle modalità di archiviazione dei dati in HDInsight o sulle procedure di importazione dei dati in HDInsight, vedere gli articoli seguenti:

* Per informazioni sul modo in cui HDInsight usa Archiviazione di Azure, vedere [Usare Archiviazione di Azure con HDInsight](../hdinsight-hadoop-use-blob-storage.md).
* Per informazioni su come caricare i dati in HDInsight, vedere [Caricare dati in HDInsight](../hdinsight-upload-data.md).

Per altre informazioni sulla creazione o la gestione di un cluster HDInsight, vedere gli articoli seguenti:

* Per altre informazioni sulla gestione di cluster HDInsight basati su Linux, vedere [Gestire i cluster HDInsight tramite Ambari](../hdinsight-hadoop-manage-ambari.md).
* Per altre informazioni sulle opzioni che è possibile selezionare durante la creazione di un cluster HDInsight, vedere [Creare cluster Hadoop basati su Linux in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Se si ha familiarità con Linux e Hadoop ma si vogliono informazioni specifiche su Hadoop in HDInsight, vedere [Uso di HDInsight in Linux](../hdinsight-hadoop-linux-information.md). In questo articolo sono disponibili informazioni quali:
  
  * URL per i servizi ospitati nel cluster, ad esempio Ambari e WebHCat
  * Il percorso del file Hadoop e gli esempi nel file system locale
  * L'utilizzo di archiviazione di Azure (WASB) anziché di HDFS come archivio predefinito di dati

[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md


