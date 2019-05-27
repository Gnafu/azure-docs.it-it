---
title: Eseguire una query Apache Hive tramite il driver JDBC - Azure HDInsight
description: Usare il driver JDBC da un'applicazione Java per inviare query Apache Hive a Hadoop in HDInsight. Connettersi a livello di codice e dal client SQuirrel SQL.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: hrasheed
ms.openlocfilehash: 2e0c17b07f70d9b05ff9ea6c3af2e8dc26127cae
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65906526"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Eseguire una query Apache Hive tramite il driver JDBC in HDInsight

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Informazioni su come usare il driver JDBC da un'applicazione Java per inviare query Apache Hive ad Apache Hadoop in Azure HDInsight. Le informazioni contenute in questo documento illustra come connettersi a livello di programmazione e dal client SQuirreL SQL.

Per altre informazioni sull'interfaccia Hive JDBC, vedere [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Prerequisiti

* Un cluster in HDInsight Hadoop. Per crearne uno, vedere [Introduzione all'uso di Hadoop in HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* Il [Java Developer Kit (JDK) versione 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) o versione successiva.
* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL è un'applicazione client JDBC.


## <a name="jdbc-connection-string"></a>Stringa di connessione JDBC

Le connessioni JDBC a un cluster HDInsight in Azure vengono stabilite sulla porta 443 e il traffico viene protetto usando SSL. Il gateway pubblico dietro cui si trovano i cluster reindirizza il traffico alla porta su cui HiveServer2 è attualmente in ascolto. La stringa di connessione seguente mostra il formato da usare per HDInsight:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Sostituire `CLUSTERNAME` con il nome del cluster HDInsight.

## <a name="authentication"></a>Authentication

Quando si stabilisce la connessione, è necessario usare il nome amministratore e la password del cluster HDInsight per l'autenticazione nel gateway cluster. Durante la connessione dai client JDBC come SQuirreL SQL, è necessario immettere il nome amministratore e la password nelle impostazioni del client.

Da un'applicazione Java è necessario usare il nome e la password quando viene stabilita una connessione. Ad esempio, il codice Java seguente apre una nuova connessione usando la stringa di connessione, il nome amministratore e la password:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Connettersi con il client SQuirreL SQL

SQuirreL SQL è un client JDBC che può essere usato per eseguire in modalità remota query Hive con il cluster HDInsight. I passaggi seguenti presuppongono che sia già stato installato SQuirreL SQL.

1. Creare una directory per contenere alcuni file da copiare dal cluster.

2. Nello script seguente, sostituire `sshuser` con il nome dell'account utente SSH per il cluster.  Sostituire `CLUSTERNAME` con il nome del cluster HDInsight.  Dalla riga di comando, immettere il comando seguente per copiare i file da un cluster HDInsight:

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/hadoop-auth.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/hadoop-common.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/lib/log4j-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/lib/slf4j-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/commons-codec*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/commons-logging-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/hive-*-1.2*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/httpclient-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/httpcore-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/libfb*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/libthrift-*.jar .
    ```

3. Avviare l'applicazione SQuirreL SQL. Nella parte sinistra della finestra selezionare **Drivers** (Driver).

    ![Scheda Drivers sul lato sinistro della finestra](./media/apache-hadoop-connect-hive-jdbc-driver/squirreldrivers.png)

4. Nella parte superiore della finestra di dialogo **Drivers** (Driver) selezionare l'icona **+** per creare un driver.

    ![Icone Drivers](./media/apache-hadoop-connect-hive-jdbc-driver/driversicons.png)

5. Nella finestra di dialogo di aggiunta del driver aggiungere le informazioni seguenti:

    * **Name** (Nome): Hive
    * **Example URL** (URL di esempio): `jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`
    * **Extra Class Path** (Percorso classe aggiuntiva): usare il pulsante Add (Aggiungi) per aggiungere tutti i file con estensione jar scaricati in precedenza
    * **Class Name** (Nome classe): org.apache.hive.jdbc.HiveDriver

   ![finestra di dialogo add driver](./media/apache-hadoop-connect-hive-jdbc-driver/adddriver.png)

   Selezionare **OK** per salvare queste impostazioni.

6. Nella parte sinistra della finestra di SQL SQuirreL selezionare **Aliases** (Alias). Quindi selezionare il **+** icona per creare un alias di connessione.

    ![aggiungere un nuovo alias](./media/apache-hadoop-connect-hive-jdbc-driver/aliases.png)

7. Usare i valori seguenti per la finestra di dialogo **Add Alias** (Aggiungi alias).

    * **Name** (Nome): Hive in HDInsight

    * **Driver**: Usare l'elenco a discesa per selezionare i **Hive** driver

    * **URL**: `jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`

        Sostituire **CLUSTERNAME** con il nome del cluster HDInsight.

    * **User Name** (Nome utente): nome dell'account di accesso per il cluster HDInsight. Il valore predefinito è `admin`.

    * **Password**: password per l'account di accesso del cluster.

   ![finestra di dialogo add alias](./media/apache-hadoop-connect-hive-jdbc-driver/addalias.png)

    > [!IMPORTANT] 
    > Usare il pulsante **Test** per verificare il funzionamento della connessione. Quando viene visualizzata la finestra di dialogo **Connect to: Hive o HDInsight** (Connetti a: Hive in HDInsight), selezionare **Connect** (Connetti) per eseguire il test. Se il test ha esito positivo, verrà visualizzata la finestra di dialogo **Connection successful** (Connessione riuscita). Se si verifica un errore, vedere [Risoluzione dei problemi](#troubleshooting).

    Usare il pulsante **OK** nella parte inferiore della finestra di dialogo **Add Alias** (Aggiungi alias) per salvare l'alias di connessione.

8. Nell'elenco a discesa **Connect to** (Connetti a) nella parte superiore di SQL SQuirreL selezionare **Hive on HDInsight** (Hive in HDInsight). Quando richiesto, selezionare **Connect** (Connetti).

    ![finestra di dialogo di connessione](./media/apache-hadoop-connect-hive-jdbc-driver/connect.png)

9. Una volta connessi, immettere la query seguente nella finestra di dialogo di query SQL e quindi selezionare il **eseguire** icona (una persona in esecuzione). Nell'area dei risultati dovrebbero comparire i risultati della query.

    ```hql
    select * from hivesampletable limit 10;
    ```

    ![finestra della query sql, con i risultati](./media/apache-hadoop-connect-hive-jdbc-driver/sqlquery.png)

## <a name="connect-from-an-example-java-application"></a>Connettersi da un'applicazione Java di esempio

Un esempio dell'uso di un client Java per query Hive in HDInsight è disponibile all'indirizzo [ https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Seguire le istruzioni del repository per compilare ed eseguire l'esempio.

## <a name="troubleshooting"></a>risoluzione dei problemi

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Errore imprevisto durante il tentativo di aprire una connessione SQL

**Sintomi**: quando ci si connette a un cluster HDInsight versione 3.3 o superiore, è possibile ricevere la segnalazione di un errore imprevisto. L'analisi dello stack dell'errore inizia con le righe seguenti:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Causa**: questo errore è causato da una versione precedente del file commons-codec.jar incluso con SQuirreL.

**Risoluzione**: per risolvere l'errore seguire questa procedura.

1. Uscire da SQuirreL e passare alla directory in cui è installato SQuirreL nel sistema. Nella directory `lib` della directory di SquirreL sostituire il file commons-codec.jar esistente con quello scaricato dal cluster HDInsight.

2. Riavviare SQuirreL. L'errore non dovrebbe più verificarsi quando ci si connette a Hive in HDInsight.

## <a name="next-steps"></a>Passaggi successivi

Dopo avere appreso come usare JDBC con Hive, vedere i collegamenti seguenti per scoprire altre modalità di utilizzo di Azure HDInsight.

* [Visualizzare i dati Apache Hive con Microsoft Power BI in Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualize Interactive Query Hive data with Power BI in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) (Visualizzare i dati Hive di Interactive Query con Power BI in Azure HDInsight).
* [Usare Apache Zeppelin per eseguire query Apache Hive in Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Connettere Excel ad Apache Hadoop mediante Power Query](apache-hadoop-connect-excel-power-query.md).
* [Connettersi ad Azure HDInsight ed eseguire query Apache Hive usando Strumenti Data Lake per Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Usare gli strumenti di Azure HDInsight per Visual Studio Code](../hdinsight-for-vscode.md).
* [Caricare dati in HDInsight](../hdinsight-upload-data.md)
* [Usare Apache Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)
* [Usare processi MapReduce con HDInsight](hdinsight-use-mapreduce.md)
