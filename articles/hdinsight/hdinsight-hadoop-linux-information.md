---
title: Suggerimenti per l'uso di Hadoop su HDInsight basato su Linux - Azure
description: Ottenere suggerimenti di implementazione per l’uso di cluster HDInsight (Hadoop) basati su Linux in un ambiente Linux familiare, in esecuzione nel cloud di Azure.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 1904ab07a188e4e877a4fb2f2b7682d923c08fb2
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68441987"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Informazioni sull'uso di HDInsight in Linux

I cluster Azure HDInsight mettono a disposizione Apache Hadoop in un ambiente Linux familiare, in esecuzione nel cloud di Azure. Per la maggior parte delle operazioni, dovrebbe funzionare esattamente come qualsiasi altra installazione di Hadoop in Linux. Questo documento indica le differenze specifiche che è opportuno conoscere.

## <a name="prerequisites"></a>Prerequisiti

In molti passaggi di questo documento vengono usate le utilità seguenti che devono essere installate nel sistema.

* [cURL](https://curl.haxx.se/): consente di comunicare con servizi basati su Web.
* **JQ**, un processore JSON da riga di comando.  Vedere [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli): consente di gestire in remoto i servizi di Azure.
* **Un client SSH**. Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="users"></a>Utenti

A meno che non sia [aggiunto al dominio](./domain-joined/hdinsight-security-overview.md), HDInsight deve essere considerato un sistema a **utente singolo**. Con il cluster viene creato un singolo account utente SSH, con autorizzazioni a livello di amministratore. Possono essere creati altri account SSH, che avranno sempre l'accesso di amministratore al cluster.

HDInsight aggiunto al dominio offre il supporto per più utenti e impostazioni di autorizzazioni e ruoli più granulari. Per altre informazioni, vedere [Manage Domain-joined HDInsight clusters](./domain-joined/apache-domain-joined-manage.md) (Gestire cluster HDInsight aggiunti al dominio).

## <a name="domain-names"></a>Nomi di dominio

Il nome di dominio completo (FQDN) da usare per la connessione al cluster da Internet è `CLUSTERNAME.azurehdinsight.net` o `CLUSTERNAME-ssh.azurehdinsight.net` (solo per SSH).

Internamente, ogni nodo del cluster ha un nome assegnato durante la configurazione del cluster. Per individuare i nomi del cluster, vedere la pagina **Host** nell'interfaccia utente Web Ambari. È anche possibile usare il codice seguente per restituire un elenco di host dall'API REST Ambari:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Sostituire `CLUSTERNAME` con il nome del cluster. Quando richiesto, immettere la password per l'account amministratore. Questo comando restituisce un documento JSON che contiene un elenco degli host nel cluster. [JQ](https://stedolan.github.io/jq/) viene utilizzato per estrarre il `host_name` valore dell'elemento per ogni host.

Se è necessario trovare il nome del nodo per un servizio specifico, è possibile eseguire una query in Ambari per tale componente. Ad esempio, per trovare gli host per il nodo con nome HDFS, usare il comando seguente:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Questo comando restituisce un documento JSON che descrive il servizio e quindi [JQ](https://stedolan.github.io/jq/) estrae solo il `host_name` valore per gli host.

## <a name="remote-access-to-services"></a>Accesso remoto ai servizi

* **Ambari (Web)**  - https://CLUSTERNAME.azurehdinsight.net

    Eseguire l'autenticazione usando l'utente e la password dell'amministratore del cluster e quindi accedere a Ambari.

    L'autenticazione è in testo non crittografato. Usare sempre HTTPS per garantire che la connessione sia protetta.

    > [!IMPORTANT]  
    > Alcune delle interfacce utente Web disponibili tramite Ambari hanno accesso ai nodi tramite un nome di dominio interno. I nomi di dominio interno non sono accessibili al pubblico da Internet. È possibile ricevere errori di "server non trovato" se si tenta di accedere ad alcune funzionalità tramite Internet.
    >
    > Per usare le funzionalità complete dell'interfaccia utente Web di Ambari, usare un tunnel SSH per inoltrare il traffico Web al nodo head del cluster. Vedere [Usare il tunneling SSH per accedere all'interfaccia Web di Apache Ambari, ResourceManager, JobHistory, NameNode, Oozie e altre interfacce Web](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)**  - https://CLUSTERNAME.azurehdinsight.net/ambari

    > [!NOTE]  
    > Eseguire l'autenticazione usando il nome utente e la password di amministratore cluster.
    >
    > L'autenticazione è in testo non crittografato. Usare sempre HTTPS per garantire che la connessione sia protetta.

* **WebHCat (Templeton)**  - https://CLUSTERNAME.azurehdinsight.net/templeton

    > [!NOTE]  
    > Eseguire l'autenticazione usando il nome utente e la password di amministratore cluster.
    >
    > L'autenticazione è in testo non crittografato. Usare sempre HTTPS per garantire che la connessione sia protetta.

* **SSH** -clustername-SSH.azurehdinsight.NET sulla porta 22 o 23. La porta 22 viene utilizzata per connettersi al nodo head primario, mentre la porta 23 viene utilizzata per connettersi a quello secondario. Per maggiori informazioni sui nodi head, vedere [Disponibilità e affidabilità dei cluster Apache Hadoop in HDInsight](hdinsight-high-availability-linux.md).

    > [!NOTE]  
    > È possibile accedere al nodo head del cluster solo tramite SSH da un computer client. Una volta connessi, è quindi possibile accedere ai nodi di lavoro mediante SSH da un nodo head.

Per altre informazioni, vedere il documento [Porte usate dai servizi Apache Hadoop su HDInsight](hdinsight-hadoop-port-settings-for-services.md).

## <a name="file-locations"></a>Percorsi dei file

I file relativi ad Hadoop si trovano nei nodi del cluster in `/usr/hdp`. La directory contiene le sottodirectory seguenti:

* **2.6.5.3006-29**: il nome della directory è la versione di Hortonworks Data Platform usata da HDInsight. Il numero nel cluster può essere diverso da quello elencato di seguito.
* **current**: Questa directory contiene collegamenti a sottodirectory nella directory **2.6.5.3006-29** . Questa directory esiste in modo da non dover ricordare il numero di versione.

I dati di esempio e i file con estensione jar sono disponibili nel file system Hadoop Distributed File System (HDFS) in `/example` e `/HdiSamples`.

## <a name="hdfs-azure-storage-and-data-lake-storage"></a>HDFS, Archiviazione di Azure e Data Lake Storage

Nella maggior parte delle distribuzioni di Hadoop, i dati vengono archiviati in HDFS che è supportato dall'archiviazione locale nei computer del cluster. L'uso di sistema locale può essere costoso per una soluzione basata su cloud dove viene addebitata una tariffa oraria o al minuto per le risorse di calcolo.

Quando si usa HDInsight, i file di dati vengono archiviati in modo scalabile e resiliente nel cloud tramite Archiviazione BLOB di Azure e, facoltativamente, Azure Data Lake Storage. Questo servizio offre i seguenti vantaggi:

* Archiviazione a lungo termine economica.
* Accessibilità da servizi esterni, ad esempio siti Web, utilità di caricamento e download di file, SDK di linguaggi diversi e Web browser.
* Capacità file elevata e archiviazione scalabile elevata.

Per altre informazioni, vedere [Understanding blobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) (Informazioni sui BLOB) e [Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/).

Quando si usa Archiviazione di Azure o Data Lake Storage, non è necessario eseguire alcuna operazione particolare da HDInsight per accedere ai dati. Ad esempio, il comando seguente elenca i file presenti in `/example/data` indipendentemente dal fatto che la cartella sia disponibile in Archiviazione di Azure o in Data Lake Storage:

    hdfs dfs -ls /example/data

In HDInsight le risorse di archiviazione dati (Archiviazione BLOB di Azure e Azure Data Lake Storage) sono separate dalle risorse di calcolo. Pertanto è possibile creare dei cluster HDInsight per eseguire i calcoli in base alle esigenze ed eliminare il cluster al termine del lavoro mantenendo i file di dati in modo sicuro nell'archiviazione cloud per tutto il tempo necessario.


### <a name="URI-and-scheme"></a>URI e schema

Alcuni comandi richiedono di specificare lo schema come parte dell'URI quando si accede a un file. Ad esempio, il componente Storm-HDFS richiede di specificare lo schema. Quando si usa un archivio non predefinito (aggiunto al cluster come spazio di archiviazione "aggiuntivo"), è sempre necessario usare lo schema come parte dell'URI.

Quando si usa __Archiviazione di Azure__, usare uno degli schemi URI seguenti:

* `wasb:///`: Accesso allo spazio di archiviazione predefinito usando la comunicazione non crittografata.

* `wasbs:///`: Accesso allo spazio di archiviazione predefinito usando la comunicazione crittografata.  Lo schema wasbs è supportato solo da HDInsight versione 3.6 in poi.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Usato durante la comunicazione con un account di archiviazione non predefinito. ad esempio quando si dispone di un account di archiviazione aggiuntivo o quando si accede a dati archiviati in un account di archiviazione pubblicamente accessibile.

Quando si usa __Azure Data Lake Storage Gen2__, usare uno degli schemi URI seguenti:

* `abfs:///`: Accesso allo spazio di archiviazione predefinito usando la comunicazione non crittografata.

* `abfss:///`: Accesso allo spazio di archiviazione predefinito usando la comunicazione crittografata.  Lo schema abfss è supportato solo da HDInsight versione 3.6 o successiva.

* `abfs://<container-name>@<account-name>.dfs.core.windows.net/`: Usato durante la comunicazione con un account di archiviazione non predefinito. ad esempio quando si dispone di un account di archiviazione aggiuntivo o quando si accede a dati archiviati in un account di archiviazione pubblicamente accessibile.

Quando si usa __Azure Data Lake Storage Gen1__, usare uno degli schemi URI seguenti:

* `adl:///`: Consente di accedere all'istanza predefinita di Data Lake Storage per il cluster.

* `adl://<storage-name>.azuredatalakestore.net/`: Usato durante la comunicazione con un'istanza di Data Lake Storage non predefinita. Usato anche per accedere ai dati all'esterno della directory radice del cluster HDInsight.

> [!IMPORTANT]  
> Quando si usa Data Lake Storage come archivio predefinito per HDInsight, è necessario specificare un percorso all'interno dell'archivio da usare come radice per l'archiviazione di HDInsight. Il percorso predefinito è `/clusters/<cluster-name>/`.
>
> Quando si usa `/` o `adl:///` per accedere ai dati, è possibile accedere solo ai dati memorizzati nella directory radice del cluster, ad esempio `/clusters/<cluster-name>/`. Per accedere ai dati in un punto qualsiasi dell'archivio, usare il formato `adl://<storage-name>.azuredatalakestore.net/`.

### <a name="what-storage-is-the-cluster-using"></a>Archivio usato dal cluster

Ambari consente di recuperare le informazioni relative alla configurazione di archiviazione predefinita per il cluster. Usare il comando seguente per recuperare le informazioni di configurazione HDFS tramite curl e filtrarle tramite [jq](https://stedolan.github.io/jq/):

```bash
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

> [!NOTE]  
> Questo comando restituisce la prima configurazione applicata al server (`service_config_version=1`) che contiene queste informazioni. Potrebbe essere necessario elencare tutte le versioni di configurazione per trovare quella più recente.

Il comando restituisce un valore simile all'URI seguente:

* `wasb://<container-name>@<account-name>.blob.core.windows.net` se si usa un account di archiviazione di Azure.

    Il nome dell'account è il nome dell'account di archiviazione di Azure. Il nome del contenitore è il contenitore BLOB che è la radice dell'archiviazione cluster.

* `adl://home` se si usa Azure Data Lake Storage. Per ottenere il nome di Data Lake Storage, usare la chiamata REST seguente:

     ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    Questo comando restituisce il nome host seguente: `<data-lake-store-account-name>.azuredatalakestore.net`.

    Per recuperare la directory che nell'archivio è la radice di HDInsight, usare la chiamata REST seguente:

    ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    Il comando restituisce un percorso simile al seguente: `/clusters/<hdinsight-cluster-name>/`.

Per individuare le informazioni di archiviazione usando il portale di Azure, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com/)selezionare il cluster HDInsight.

2. Nella sezione **Proprietà** selezionare **Account di archiviazione**. Vengono visualizzate le informazioni di archiviazione del cluster.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Come accedere ai file dall'esterno di HDInsight

Esistono vari modi per accedere ai dati dall'esterno del cluster HDInsight. Di seguito sono indicati alcuni collegamenti a utilità e SDK da usare per lavorare con i dati:

Se si usa __Archiviazione di Azure__, vedere i collegamenti seguenti per informazioni sulle modalità di accesso ai dati:

* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-az-cli2): comandi dell'interfaccia della riga di comando per l'uso con Azure. Dopo l'installazione, usare il comando `az storage` per informazioni sull'uso dell'archiviazione o `az storage blob` per i comandi specifici dei BLOB.
* [blobxfer.py](https://github.com/Azure/blobxfer): uno script Python per l'uso con i BLOB in Archiviazione di Azure.
* Vari SDK:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [API REST di archiviazione](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Se si usa __Azure Data Lake Storage__, vedere i collegamenti seguenti per informazioni sulle modalità di accesso ai dati:

* [Web browser](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Interfaccia della riga di comando di Azure](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [API REST WebHDFS](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Strumenti di Data Lake per Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling"></a>Ridimensionamento del cluster

La funzionalità di ridimensionamento del cluster consente di modificare il numero di nodi dati usati da un cluster in modo dinamico. È possibile eseguire operazioni di ridimensionamento mentre altri processi sono in esecuzione nel cluster.  Vedere anche ridimensionare i [cluster HDInsight](./hdinsight-scaling-best-practices.md)

L'operazione di ridimensionamento può influire sui tipi di cluster come indicato di seguito:

* **Hadoop**: durante la riduzione del numero di nodi in un cluster, alcuni servizi nel cluster vengono riavviati. È quindi possibile che al termine dell'operazione di ridimensionamento, i processi in esecuzione o in sospeso abbiano esito negativo. In questo caso, inviare nuovamente i processi una volta completata l'operazione.
* **HBase**: i server a livello di area vengono bilanciati automaticamente entro pochi minuti dal completamento dell'operazione di ridimensionamento. Per bilanciare manualmente i server a livello di area, seguire questa procedura:

    1. Connettersi al cluster HDInsight tramite SSH. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

    2. Usare il codice seguente per avviare la shell HBase:

            hbase shell

    3. Una volta caricata la shell HBase, usare il codice seguente per bilanciare manualmente i server a livello di area:

            balancer

* **Storm**: al termine dell'operazione di ridimensionamento, ribilanciare qualsiasi topologia Storm in esecuzione. Il ridimensionamento consente alla topologia di rettificare le impostazioni di parallelismo in base al nuovo numero di nodi nel cluster. Per ribilanciare le topologie in esecuzione, usare una delle opzioni seguenti:

    * **SSH**: connettersi al server e usare il comando seguente per ribilanciare una topologia:

            storm rebalance TOPOLOGYNAME

        È anche possibile specificare parametri per eseguire l'override degli hint di parallelismo forniti in origine dalla topologia. Ad esempio, `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` riconfigura la topologia con 5 processi di lavoro, 3 esecutori per il componente blue-spout e 10 esecutori per il componente yellow-bolt.

    * **Interfaccia utente Storm**: usare la procedura seguente per ribilanciare una topologia usando l'interfaccia utente Storm.

        1. Aprire `https://CLUSTERNAME.azurehdinsight.net/stormui` nel Web browser, dove `CLUSTERNAME` è il nome del cluster Storm. Se richiesto, immettere il nome amministratore (admin) del cluster HDInsight e la password specificata durante la creazione del cluster.
        2. Selezionare la topologia da ribilanciare e quindi fare clic sul pulsante **Rebalance** (Ribilancia). Specificare il ritardo prima dell'esecuzione dell'operazione di ribilanciamento.

* **Kafka**: è consigliabile ribilanciare le repliche di una partizione dopo le operazioni di ridimensionamento. Per altre informazioni, vedere il documento [Disponibilità elevata dei dati con Apache Kafka in HDInsight](./kafka/apache-kafka-high-availability.md).

Per informazioni specifiche sul ridimensionamento del cluster HDInsight, vedere:

* [Gestire cluster Apache Hadoop in HDInsight tramite il portale di Azure](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Gestire cluster di Apache Hadoop in HDInsight usando l'interfaccia della riga di comando di Azure](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Come si installa Hue (o un altro componente Hadoop)?

HDInsight è un servizio gestito. Se Azure rileva un problema con il cluster, è possibile eliminare il nodo con l'errore e creare un nodo per sostituirlo. Se si esegue l'installazione manuale degli elementi nel cluster, questi non vengono salvati in modo permanente quando si esegue questa operazione. Usare invece le [azioni script di HDInsight](hdinsight-hadoop-customize-cluster-linux.md). Un'azione script può essere usata per apportare le modifiche seguenti:

* Installare e configurare un servizio o un sito Web.
* Installare o configurare un componente che richiede modifiche di configurazione in più nodi del cluster,

Le azioni script sono script Bash. Gli script vengono eseguiti durante la creazione del cluster e possono essere usati per installare e configurare componenti aggiuntivi. Sono disponibili script di esempio per installare i componenti seguenti:

* [Apache Giraph](hdinsight-hadoop-giraph-install-linux.md)

Per informazioni su come sviluppare azioni script personalizzate, vedere [Sviluppo di azioni script con HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="jar-files"></a>File con estensione jar

Alcune tecnologie Hadoop vengono fornite in file con estensione jar indipendenti contenenti funzioni usate come parte di un processo MapReduce o dall'interno di Pig o Hive. Spesso non richiedono alcuna installazione e possono essere caricate nel cluster dopo la creazione e usate direttamente. Per assicurarsi che il componente venga mantenuto dopo la nuova creazione dell'immagine del cluster, è possibile archiviare il file nella risorsa di archiviazione predefinita per il cluster (WASB o ADL).

Se ad esempio si desidera usare l'ultima versione di [Apache DataFu](https://datafu.incubator.apache.org/), è possibile scaricare un file con estensione jar contenente il progetto e caricarlo nel cluster HDInsight. Seguire quindi la documentazione di DataFu per informazioni sull'uso da Pig o Hive.

> [!IMPORTANT]  
> Alcuni componenti che sono file con estensione jar autonomi vengono forniti con HDInsight, ma non sono presenti nel percorso. Se si desidera un componente specifico, è possibile usare il comando seguente per cercarlo nel cluster:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Viene restituito il percorso dei file con estensione jar corrispondenti.

Per usare una versione diversa di un componente, caricare la versione desiderata e usarla nei processi.

> [!IMPORTANT]
> I componenti forniti con il cluster HDInsight sono supportati in modo completo e il supporto tecnico Microsoft contribuirà a isolare e risolvere i problemi correlati a questi componenti.
>
> I componenti personalizzati ricevono supporto commercialmente ragionevole per semplificare la risoluzione dei problemi. È possibile che si ottenga la risoluzione dei problemi o che venga richiesto di usare i canali disponibili per le tecnologie open source, in cui è possibile ottenere supporto approfondito per la tecnologia specifica. Per esempio, è possibile ricorrere a molti siti di community, come: il [forum MSDN per HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Anche per i progetti Apache sono disponibili siti specifici in [https://apache.org](https://apache.org), per esempio: [Hadoop](https://hadoop.apache.org/), [Spark](https://spark.apache.org/).

## <a name="next-steps"></a>Passaggi successivi

* [Gestire i cluster HDInsight tramite l'API REST di Apache Ambari](./hdinsight-hadoop-manage-ambari-rest-api.md)
* [Usare Apache Hive con HDInsight](hadoop/hdinsight-use-hive.md)
* [Usare Pig con Hadoop in HDInsight](hadoop/hdinsight-use-pig.md)
* [Usare processi MapReduce con HDInsight](hadoop/hdinsight-use-mapreduce.md)
