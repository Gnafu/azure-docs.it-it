---
title: Note sulla versione archiviata di Azure HDInsight
description: Note sulla versione archiviata e versioni di Azure HDInsight.
services: hdinsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: hrasheed
ms.openlocfilehash: 5e70a92b4a0de633f88012d756276cef07226c82
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037676"
---
# <a name="archived-release-notes-for-azure-hdinsight"></a>Note sulla versione archiviata di Azure HDInsight

Per gli aggiornamenti della versione **più recente** di Azure HDInsight, vedere [Note sulla versione di HDInsight](hdinsight-release-notes.md).

> [!IMPORTANT]
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere l'[articolo sul controllo delle versioni di HDInsight](hdinsight-component-versioning.md).

## <a name="notes-for-06272018---release-of-new-open-source-versions-adls-gen2-etc-on-hdinsight-36"></a>Note per 27/06/2018: rilascio delle nuove versioni open source, ADLS Gen2 e così via per HDInsight 3.6
La versione di giugno 2018 di HDInsight è una versione importante che contiene molti nuovi aggiornamenti e molte nuove funzionalità per i clienti. Per altri dettagli, vedere questo [post](https://azure.microsoft.com/blog/enterprises-get-deeper-insights-with-hadoop-and-spark-updates-on-azure-hdinsight/).

Di seguito le informazioni più importanti. Per le note sulla versione dettagliate, la risoluzione di bug, i problemi noti e così via, vedere [Note sulla versione per Azure HDInsight](hdinsight-release-notes.md).

- **Aggiornare Hadoop e altri progetti open source**: oltre alla correzione di più di un migliaio di bug in oltre 20 progetti open source, questo aggiornamento contiene una nuova versione di Spark, la 2.3, e di Kafka, la 1.0.
- **Aggiornare R Server 9.1 a Machine Learning Services 9.3**: con questa versione, vengono offerti a tecnici ed esperti di dati i migliori progetti open source, ottimizzati con innovazioni algoritmiche e operatività semplificata, tutti disponibili nella loro lingua preferita, con la velocità di Apache Spark. Questa versione amplia le funzionalità offerte da R Server con supporto aggiuntivo per Python, con conseguente modifica del nome del cluster da R Server a Machine Learning Services. 
- **Supporto per l'archiviazione di Azure Data Lake Gen2**: HDInsight supporterà la versione di anteprima dell'archiviazione di Azure Data Lake Gen2. Nelle aree disponibili i clienti potranno scegliere un account di ADLS Gen2 come archivio per i cluster HDInsight.
- **Aggiornamenti di HDInsight Enterprise Security Package (anteprima)**: supporto degli endpoint del servizio Rete virtuale di Microsoft Azure (anteprima) per l'archiviazione BLOB di Azure, ADLS Gen1, Cosmos DB e database di Azure. 

## <a name="notes-for-03202018---release-of-spark-22-on-hdinsight-36"></a>Note sulla versione di Spark 2.2 in HDInsight 3.6 rilasciata il 20/03/2018

- Spark 2.2.0 migliora la stabilità in Spark Core, SQL, ML e porta la funzionalità di flusso strutturato allo stato di disponibilità generale. Spark 2.2.0 è ora disponibile in HDInsight 3.6.


## <a name="notes-for-08012017-release-of-hdinsight"></a>Note per la versione di HDInsight rilasciata in data 01/08/2017

| Title | DESCRIZIONE | Area interessata  | Tipo di cluster  | 
| --- | --- | --- | --- | --- |
| Versione di Microsoft R Server 9.1 in HDInsight |HDInsight supporta ora il provisioning dei cluster R Server 9.1 in HDInsight. Per altre informazioni sulla versione di Microsoft R Server 9.1, vedere [questo blog](https://blogs.technet.microsoft.com/dataplatforminsider/2017/04/19/introducing-microsoft-r-server-9-1-release/). |Service |R Server |
| HDInsight 3.6 include ora le versioni più recenti dello stack di Hadoop|<ul><li>Per un elenco dettagliato delle versioni aggiornate, vedere [Componenti di Hadoop disponibili con diverse versioni di HDInsight](hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).</li><li>Per un elenco di bug risolti nelle versioni più recenti dello stack di Hadoop, vedere la pagina relativa alle [informazioni delle patch Apache](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/patch_parent.html).</li><li>Per un elenco delle principali modifiche rispetto a HDP 2.6.1 (che è ora disponibile in HDInsight 3.6), vedere [https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html).</li><li>Per un elenco dei problemi noti di HDP 2.6.1, vedere la pagina relativa ai [problemi noti](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/known_issues.html).</li></ul> |Service |Tutti |N/D |
| Aggiornamenti ai cluster Interactive Hive (anteprima) |<ul><li><b>Miglioramento della funzionalità.</b> L'implementazione del metastore memorizzato nella cache riduce il carico sul database di back-end di SQL memorizzando nella cache i metadati e migliora le prestazioni per tutte le operazioni dei metadati.  Questo miglioramento è ora un'impostazione predefinita in tutti i cluster Interactive Hive. Per altre informazioni, vedere [https://issues.apache.org/jira/browse/HIVE-16520](https://issues.apache.org/jira/browse/HIVE-16520).</li><li><b>Miglioramento della funzionalità.</b> Il caricamento della partizione dinamica è ottimizzato. Per altre informazioni, vedere [https://issues.apache.org/jira/browse/HIVE-14204](https://issues.apache.org/jira/browse/HIVE-14204).</li><li><b>Miglioramento della funzionalità.</b> Ottimizzazioni di configurazione per HDInsight su Linux.</li><li><b>Correzione di bug.</b> `CredentialProviderFactory$getProviders` non è thread-safe. Questo problema ora è stato corretto. Per altre informazioni, vedere [https://issues.apache.org/jira/browse/HADOOP-14195](https://issues.apache.org/jira/browse/HADOOP-14195).</li><li><b>Correzione di bug.</b> L'utilizzo elevato della CPU con API `liststatus` del driver WASB genera prestazioni ATS non valide. Questo problema ora è stato corretto. Per altre informazioni, vedere [https://github.com/Azure/azure-storage-java/pull/154](https://github.com/Azure/azure-storage-java/pull/154).</li></ul> |Service |Interactive Hive (anteprima) |
| Aggiornamenti ai cluster Hadoop |L'affidabilità dell'operazione del processo Templeton è migliorata. Per altre informazioni, vedere [https://issues.apache.org/jira/browse/HIVE-15947](https://issues.apache.org/jira/browse/HIVE-15947) |Service |Hadoop |
| Aggiornamenti di YARN | HDInsight ora crea un database Ambari da 250 GB (senza l'aumento dei costi), che comporta una migliore esperienza per i clienti. Questa modifica dovrebbe impedire il riempimento di ATS e migliorare le prestazioni. |Service |Tutti |
| Aggiornamenti di Spark | Versione di Spark 2.1.1. Per altre informazioni, vedere la pagina relativa alle [note sulla versione di Spark 2.1.1](https://spark.apache.org/releases/spark-release-2-1-1.html). | Service | Spark |

  



## <a name="04062017---general-availability-of-hdinsight-36"></a>04/06/2017 - Disponibilità generale di HDInsight 3.6

* Con questa versione, Azure HDInsight aggiunge la versione 3.6, basata su HDP 2.6. Le note sulla versione di HDP 2.6 sono disponibili [qui](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html) e altre informazioni sulle versioni di HDInsight sono disponibili [qui](hdinsight-component-versioning.md). HDInsight 3.6 è disponibile per i carichi di lavoro seguenti:

    * Hadoop v2.7.3
    * HBase v1.1.2
    * Storm v1.1.0
    * Spark v2.1.0
    * Interactive Hive v2.1.0

* **Supporto per Hive View 2.0**. Questa vista migliora l'esperienza utente in Interactive Hive. Per altre informazioni, vedere la [documentazione di Hortonworks](http://docs.hortonworks.com/HDPDocuments/Ambari-2.5.0.3/bk_ambari-views/content/ch_using_hive_view.html).

* **Miglioramenti delle prestazioni con Hive LLAP**. Per altre informazioni, vedere la [documentazione di Hortonworks](https://hortonworks.com/blog/top-5-performance-boosters-with-apache-hive-llap/).

* **Nuove funzionalità di Hive**. Vedere la [documentazione di Hortonworks](https://hortonworks.com/apache/hive/#section_4).

* **Interfaccia della riga di comandi di Hive deprecata**: l'interfaccia della riga di comando di Hive verrà deprecata e si invita gli utenti a usare Beeline al suo posto. Per altre informazioni, vedere la [documentazione Apache](https://cwiki.apache.org/confluence/display/Hive/Replacing+the+Implementation+of+Hive+CLI+Using+Beeline). Per istruzioni su come usare Beeline con HDInsight, vedere [Use Beeline with HDInsight Hadoop clusters](hadoop/apache-hadoop-use-hive-beeline.md) (Usare Beeline con i cluster Hadoop HDInsight).

* **Nuove funzionalità di Apache Phoenix e HBase**.
    * Supporto di quota di archiviazione: comunemente usato negli ambienti multi-tenant, consente uno spazio di archiviazione limitato a livello di tabella e di spazio dei nomi.
    * Miglioramenti di indicizzazione Phoenix: creazione dell'indice incrementale e ricompilazione o ripresa dell'indicizzazione da errori precedenti.
    * Strumento di integrità dei dati di Phoenix: supporta la convalida di schema, indice e altri metadati.


* **Problema con HBase**: durante l'esecuzione di un processo MapReduce di caricamento in massa di CSV, la sintassi seguente potrebbe causare un errore.

        HADOOP_CLASSPATH=$(hbase mapredcp):/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv

    Usare invece la sintassi seguente:

        HADOOP_CLASSPATH=/path/to/hbase-protocol.jar:/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv


## <a name="02282017---release-of-spark-21-on-hdinsight-36-preview"></a>02/28/2017 - Versione di Spark 2.1 in HDInsight 3.6 (Anteprima)
* [Spark 2.1](http://spark.apache.org/releases/spark-release-2-1-0.html) risolve i molti problemi relativi a usabilità e stabilità riscontrati con le versioni precedenti. Offre anche nuove funzionalità in tutti i carichi di lavoro di Spark, ad esempio Spark Core, SQL, ML e Streaming.
* Streaming strutturati presentano una migliore scalabilità con il supporto per soglie di tempo dell'evento e con il connettore Kafka 0.10.
* Il partizionamento Spark SQL viene ora gestito usando il nuovo meccanismo di gestione di partizioni scalabili. Per altre informazioni su come eseguire l'aggiornamento, vedere [qui](http://spark.apache.org/releases/spark-release-2-1-0.html).
* Spark 2.1 in Azure HDInsight 3.6 Preview attualmente non supporta la connettività degli strumenti di Business Intelligence tramite il driver ODBC.
* L'accesso di Azure Data Lake Store dai cluster Spark 2.1 non è supportato in questa versione di anteprima.


## <a name="11182016---release-of-spark-201-on-hdinsight-35"></a>11/18/2016 - Versione di Spark 2.0.1 in HDInsight 3.5
Spark 2.0.1 è ora disponibile in cluster Spark (HDInsight versione 3.5).

## <a name="11162016---release-of-r-server-90-on-hdinsight-35-spark-20"></a>11/16/2016 - Versione di R Server 9.0 in HDInsight 3.5 (Spark 2.0)
*   Nei cluster R Server è ora possibile scegliere due versioni: R Server 9.0 in HDI 3.5 (Spark 2.0) e R Server 8.0 in HDI 3.4 (Spark 1.6).
*   R Server 9.0 in HDI 3.5 (Spark 2.0) si basa su R 3.3.2 e include nuove funzioni di origine dati per ScaleR, denominate RxHiveData e RxParquetData, per il caricamento diretto di dati da Hive e Parquet a frame di dati Spark per l'analisi in ScaleR. Per altre informazioni su queste funzioni, vedere la guida in linea di R usando i comandi **?RxHiveData** e **?RxParquetData**.
*   RStudio Server Community Edition è ora installato per impostazione predefinita (con possibilità di rifiuto esplicito) nel pannello Configurazione cluster, nell'ambito del flusso di provisioning.

## <a name="11092016---release-of-spark-20-on-hdinsight"></a>11/09/2016 - Versione di Spark 2.0 in HDInsight
* I cluster Spark 2.0 in HDInsight 3.5 supportano ora servizi Livy e Jupyter.

## <a name="10262016---release-of-r-server-on-hdinsight"></a>10/26/2016 - Versione di R Server in HDInsight
* L'URI per l'accesso a nodi perimetrali è stato modificato in **nomecluster**-ed-ssh.azurehdinsight.net
* Il provisioning del cluster R Server in HDInsight è stato semplificato.
* R Server in HDInsight ora è disponibile come normale tipo di cluster "R Server" in HDInsight e non viene più installato come applicazione HDInsight separata. Il provisioning del nodo perimetrale e dei file binari di R Server ora viene effettuato durante la distribuzione del cluster R Server. In questo modo il provisioning è più veloce e affidabile. Il modello di determinazione prezzi per R Server viene aggiornato di conseguenza.
* Il prezzo del tipo di cluster R Server ora corrisponde al prezzo del piano Standard più la maggiorazione per R Server. Questa modifica non ha effetto sul prezzo effettivo di R Server. Cambia solo il modo in cui gli addebiti vengono presentati nella fattura. Tutti i cluster R Server esistenti e i modelli di Azure Resource Manager continuano a funzionare fino all'avviso di funzionalità deprecata. **È tuttavia consigliabile aggiornare le distribuzioni con script per l'uso del nuovo modello di Resource Manager.**






