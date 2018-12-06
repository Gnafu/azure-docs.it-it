---
title: Componenti e versioni di Apache Hadoop - Azure HDInsight
description: Informazioni sui componenti e sulle versioni di Apache Hadoop in HDInsight e sui livelli di servizio disponibili in questa distribuzione cloud di Hortonworks Data Platform.
keywords: versioni hadoop, componenti ecosistema hadoop, componenti hadoop, come controllare versione hadoop
services: hdinsight
ms.reviewer: jasonh
author: kkampf
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: kakampf
ms.openlocfilehash: 2ae0450be420edeb4ef18d0c9e5ca6e1dacaf50d
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52497019"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>Componenti e versioni di Apache Hadoop disponibili in HDInsight.

Informazioni su componenti e versioni dell'ecosistema [Apache Hadoop](https://hadoop.apache.org/) in Microsoft Azure HDInsight e su Enterprise Security Package. Informazioni su come controllare le versioni di componenti Hadoop in HDInsight. 

Ogni versione di HDInsight è una distribuzione cloud di una versione di Hortonworks Data Platform (HDP).

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>Componenti di Apache Hadoop disponibili con diverse versioni di HDInsight
Azure HDInsight supporta più versioni cluster di Hadoop che possono essere distribuite in qualsiasi momento. Ogni versione scelta crea una versione specifica della distribuzione HDP (Hortonworks Data Platform) e un set di componenti contenuti in tale distribuzione. A partire dal 4 aprile 2017, la versione cluster predefinita utilizzata da Azure HDInsight è la 3.6, basata su HDP 2.6.

Le versioni del componente associate alle versioni del cluster HDInsight sono elencate nella tabella seguente: 

> [!NOTE]
> La versione predefinita per il servizio HDInsight può essere modificata senza preavviso. Se si dispone di una dipendenza della versione, specificare la versione HDInsight quando si crea il cluster tramite l'SDK .NET con Azure PowerShell e l'interfaccia della riga di comando classica di Azure.

| Componente | HDInsight 4.0 (anteprima) | HDInsight 3.6 (predefinito) | HDInsight 3.5 | HDInsight 3.4 | HDInsight 3.3 | HDInsight 3.2 | HDInsight 3.1 | HDInsight 3.0 |
| --- | --- | --- | --- | --- | --- | --- | --- |--- |
| Hortonworks Data Platform |3.0 |2.6 |2.5 |2.4 |2.3 |2.2 |2.1.7 |2.0 |
| Apache Hadoop e YARN |3.1.1 |2.7.3 |2.7.3 |2.7.1 |2.7.1 |2.6.0 |2.4.0 |2.2.0 |
| Apache Tez |0.9.1 |0.7.0 |0.7.0 |0.7.0 |0.7.0 |0.5.2 |0.4.0 |-|
| Apache Pig |0.16.0 |0.16.0 |0.16.0 |0.15.0 |0.15.0 |0.14.0 |0.12.1 |0.12.0 |
| Apache Hive e HCatalog |-|1.2.1 |1.2.1 |1.2.1 |1.2.1 |0.14.0 |0.13.1 |0.12.0 |
| Apache Hive |3.1.0 | 2.1.0 |-|-|-|-|-|-|
| Apache Tez Hive2 |-| 0.8.4 |-|-|-|-|-|-|
| Apache Ranger |1.1.0 |0.7.0 |0.6.0 |-|-|-|-|-|
| Apache HBase |2.0.1 |1.1.2 |1.1.2 |1.1.2 |1.1.1 |0.98.4 |0.98.0 |-|
| Apache Sqoop |1.4.7 |1.4.6 |1.4.6 |1.4.6 |1.4.6 |1.4.5 |1.4.4 |1.4.4 |
| Apache Oozie |4.3.1 |4.2.0 |4.2.0 |4.2.0 |4.2.0 |4.1.0 |4.0.0 |4.0.0 |
| Apache Zookeeper |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.5 |3.4.5 |
| Apache Storm |1.2.1 |1.1.0 |1.0.1 |0.10.0 |0.10.0 |0.9.3 |0.9.1 |-|
| Apache Mahout |-|0.9.0+ |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0 |0.9.0 |-|
| Apache Phoenix |5 |4.7.0 |4.7.0 |4.4.0 |4.4.0 |4.2.0 |4.0.0.2.1.7.0-2162 |-|
| Apache Spark |2.3.1 |2.3.0, 2.2.0, 2.1.0 |1.6.2, 2.0 |1.6.0 |1.5.2 |1.3.1 (solo Windows) |-|-|
| Apache Livy |0,5 |0,4 |0,3 |0,3 |0,2 |-|-|-|
| Apache Kafka | 1.1 |1.1, 1.0, 0.10.1 | 0.10.0 | 0.9.0 |-|-|-|-|
| Apache Ambari | 2.7.0 |2.6.0 | 2.4.0 | 2.2.1 | 2.1.0 |-|-|-|
| Apache Zeppelin | 0.8.0 |0.7.0 |-|-|-|-|-|-|
| Mono |4.2.1 |4.2.1 |4.2.1 |3.2.8 |-|-|-|
| Apache Slider |-| 0.92.0 |-|-|-|-|-|-|

## <a name="check-for-current-hadoop-component-version-information"></a>Controllare le informazioni sulle versioni correnti dei componenti Hadoop

Le versioni dei componenti dell'ecosistema Hadoop associate alle versioni cluster HDInsight potrebbero subire modifiche con gli aggiornamenti a HDInsight. Per controllare i componenti Hadoop e verificare le versioni in uso per un cluster, usare l'API REST Ambari. Il comando **GetComponentInformation** recupera informazioni sui componenti del servizio. Per informazioni dettagliate, vedere la [documentazione di Apache Ambari][ambari-docs].

Per i cluster Windows, un altro modo per controllare le versioni dei componenti è accedere a un cluster usando Desktop remoto ed esaminare i contenuti della directory C:\apps\dist\.

> [!IMPORTANT]
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [Ritiro di HDInsight su Windows](#hdinsight-windows-retirement).

### <a name="release-notes"></a>Note sulla versione

Per altre note sulla versione relative alle versioni più recenti di HDInsight, vedere [Note sulla versione di HDInsight](hdinsight-release-notes.md) .

## <a name="supported-hdinsight-versions"></a>Versioni supportate di HDInsight
Le tabelle seguenti elencano le versioni di HDInsight. Le versioni HDP che corrispondono a ogni versione di HDInsight sono elencate insieme alle date di rilascio del prodotto. Se note, vengono indicate anche la data di scadenza del supporto e la data di ritiro.

### <a name="available-versions"></a>Versioni disponibili

La tabella seguente elenca le versioni di HDInsight disponibili nel portale di Azure nonché altri metodi di distribuzione come PowerShell e .NET SDK.

| Versione HDInsight | Versione HDP | Sistema operativo della macchina virtuale | Data di rilascio | Data di scadenza del supporto | Data di ritiro | Disponibilità elevata |  Disponibilità nel portale di Azure | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 <br>  (Anteprima) |HDP 3.0 |Ubuntu 16.0.4 LTS |24 settembre 2018 | | |Yes |Yes |
| HDInsight 3.6 |HDP 2.6 |Ubuntu 16.0.4 LTS |4 aprile 2017 | | |Yes |Yes |
| HDInsight 3.5 <br> (Spark)* |HDP 2.6 |Ubuntu 16.0.4 LTS |30 settembre 2016 |13 marzo 2019 |13 marzo 2019 |Yes |Yes |

*&ast;Il supporto per HDInsight 3.5 è stato esteso solo per i tipi di cluster Spark*

> [!NOTE]
> Se il supporto per una versione è scaduto, potrebbe non essere disponibile tramite il portale di Microsoft Azure. Tuttavia, le versioni dei cluster continueranno a essere disponibili usando il parametro `Version` nel comando [New-AzureRmHDInsightCluster](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightcluster) di Windows PowerShell e .NET SDK fino alla data di ritiro della versione.
>

### <a name="retired-versions"></a>Versioni ritirate

La tabella seguente elenca le versioni di HDInsight attualmente **non** disponibili nel portale di Azure.

| Versione HDInsight | Versione HDP | Sistema operativo della macchina virtuale | Data di rilascio | Data di scadenza del supporto | Data di ritiro | Disponibilità elevata |  Disponibilità nel portale di Azure | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 <br> (Non Spark) |HDP 2.5 |Ubuntu 16.0.4 LTS |30 settembre 2016 |5 settembre 2017 |28 giugno 2018 |Yes |No  |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |29 marzo 2016 |29 dicembre 2016 |9 gennaio 2018 |Yes |No  |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2 dicembre 2015 |27 giugno 2016 |31 luglio 2018 |Yes |No  |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2 dicembre 2015 |27 giugno 2016 |31 luglio 2017 |Yes |No  |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS o Windows Server 2012 R2 |18 febbraio 2015 |1° marzo 2016 |1° aprile 2017 |Yes |No  |
| HDInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |24 giugno 2014 |18 maggio 2015 |30 giugno 2016 |Yes |No  |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |11 febbraio 2014 |17 settembre 2014 |30 giugno 2015 |Yes |No  |
| HDInsight 2.1 |HDP 1.3 |Windows Server 2012 R2 |28 ottobre 2013 |12 maggio 2014 |31 maggio 2015 |Yes |No  |
| HDInsight 1.6 |HDP 1.1 | |28 ottobre 2013 |26 aprile 2014 |31 maggio 2015 |No  |No  |

> [!NOTE]
> I cluster ad alta disponibilità con due nodi head vengono distribuiti per impostazione predefinita per HDInsight versione 2.1 e successive. Non sono disponibili per i cluster HDInsight versione 1.6.

## <a name="enterprise-security-package-for-hdinsight"></a>Pacchetto di sicurezza aziendale per HDInsight

Si tratta di un pacchetto opzionale che è possibile aggiungere al cluster HDInsight come parte del flusso di lavoro di creazione del cluster. Il Pacchetto di sicurezza aziendale supporta:

- Integrazione con Active Directory per l'autenticazione.

    In passato era possibile creare cluster HDInsight solo con un utente amministratore locale e un utente SSH locale. L'utente amministratore locale poteva accedere a tutti i file, cartelle, tabelle e colonne.  Con il Pacchetto di sicurezza aziendale è possibile abilitare il controllo degli accessi in base al ruolo integrando i cluster HDInsight con il proprio Active Directory, inclusi Active Directory locale, Azure AD Domain Services o Active Directory su macchina virtuale IaaS. L'amministratore del dominio nel cluster può concedere agli utenti di usare il proprio nome utente aziendale (del dominio) e relativa password per accedere al cluster. 

    Per altre informazioni, vedere:

    - [Introduzione alla sicurezza Apache Hadoop con i cluster HDInsight aggiunti al dominio](./domain-joined/apache-domain-joined-introduction.md)
    - [Pianificare cluster Apache Hadoop aggiunti a un dominio di Azure in HDInsight](./domain-joined/apache-domain-joined-architecture.md)
    - [Configurare l'ambiente sandbox aggiunto al dominio](./domain-joined/apache-domain-joined-configure.md)
    - [Configurare cluster HDInsight aggiunti al dominio usando Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Autorizzazione per i dati

    - Integrazione con Apache Ranger per l'autorizzazione per code Yarn, Hive e Spark SQL.
    - È possibile impostare il controllo di accesso per file e cartelle.

    Per altre informazioni, vedere:

    - [Configurare criteri Apache Hive in HDInsight aggiunto al dominio](./domain-joined/apache-domain-joined-run-hive.md)

- Visualizzare i log di controllo per monitorare gli accessi e i criteri configurati. 

### <a name="supported-cluster-types"></a>Tipi di cluster supportati

Attualmente solo i tipi di cluster seguenti supportano il Pacchetto di sicurezza aziendale:

- Hadoop (solo HDInsight 3.6)
- Spark
- Interactive Query

### <a name="support-for-azure-data-lake-store"></a>Supporto per Archivio Azure Data Lake

Il Pacchetto di sicurezza aziendale supporta l'uso di Azure Data Lake Store sia come risorsa di archiviazione primaria che come risorsa di archiviazione aggiuntiva.

### <a name="pricing-and-sla"></a>Prezzi e contratto di servizio
Per informazioni su prezzi e contratto di servizio per il Pacchetto di sicurezza aziendale, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="hdinsight-windows-retirement"></a>Ritiro di HDInsight in Windows
Microsoft Azure HDInsight versione 3.3 è stata l'ultima versione di HDInsight per Windows. La data di ritiro di HDInsight per Windows è il 31 luglio 2018. Se si dispone di tutti i cluster HDInsight in Windows 3.3 o precedente, è necessario eseguire la migrazione ad HDInsight per Linux (HDInsight 3.5 o versione successiva) prima del 31 luglio 2018. La migrazione al sistema operativo Linux consente di mantenere la possibilità di creare o ridimensionare i cluster HDInsight. Il supporto per HDInsight versione 3.3 per Windows è scaduto il 27 giugno 2016.

A partire da HDInsight versione 3.4, Microsoft ha rilasciato HDInsight solo per il sistema operativo Linux. Di conseguenza, alcuni dei componenti all'interno di HDInsight sono disponibili esclusivamente per Linux. Sono inclusi [Apache Ranger](https://ranger.apache.org/), [Apache Kafka](https://kafka.apache.org/), Interactive Query, [Apache Spark](https://spark.apache.org/), le applicazioni HDInsight e Azure Data Lake Store come file system primario. Le versioni future di HDInsight sono disponibili solo per il sistema operativo Linux. Per il sistema operativo Windows non saranno più rilasciate versioni di HDInsight. 

## <a name="faqs"></a>Domande frequenti

### <a name="what-is-the-timeline-for-retiring-hdinsight-on-windows"></a>Qual è la sequenza temporale per il ritiro di HDInsight su Windows?
La data di ritiro di HDInsight per Windows è il 31 luglio 2018. Se la data di ritiro pianificata è diversa per la propria regione, si riceverà una notifica separata. 

### <a name="what-is-the-impact-of-retiring-hdinsight-on-windows-for-existing-customers"></a>Quale sarà l'impatto che il ritiro di HDInsight per Windows avrà sui clienti esistenti?
Dopo che HDInsight per Windows è ritirato, non sarà possibile creare un nuovo cluster HDInsight su Windows o ridimensionarne uno esistente. Il supporto per HDInsight versione 3.3 è scaduto il 27 giugno 2016. Non sono pertanto disponibili supporto e correzioni di bug per HDInsight 3.3 o versioni precedenti. Le versioni future di HDInsight sono disponibili solo per il sistema operativo Linux. Per il sistema operativo Windows non saranno più rilasciate versioni di HDInsight.
 
### <a name="which-versions-of-hdinsight-on-windows-are-affected"></a>Quali versioni di HDInsight per Windows sono interessate?
Azure HDInsight versione 3.3 è l'ultima versione di HDInsight per Windows. Prima che HDInsight su Windows venga ritirato, tutti i cluster HDInsight per Windows ,versione 3.3, devono essere migrati a HDInsight per Linux, versione 3.5 o successiva. La migrazione dei cluster a HDInsight per Linux consente di mantenere la possibilità di creare nuovi cluster o di ridimensionare un cluster esistente. 

### <a name="what-do-i-need-to-do"></a>Cosa occorre fare?
Eseguire la migrazione del cluster HDInsight per Windows a un cluster HDInsight per Linux supportato prima del 31 luglio 2018. Per maggiori informazioni, fare riferimento al [documento di migrazione HDInsight](hdinsight-migrate-from-windows-to-linux.md). Per informazioni dettagliate sulle versioni di Azure HDInsight, vedere l'elenco delle [versioni supportate](hdinsight-component-versioning.md#supported-hdinsight-versions). 

### <a name="where-do-i-find-the-cluster-os-type"></a>Dove è possibile trovare il tipo di sistema operativo del cluster?
Nel portale di Azure, accedere alla pagina della panoramica del cluster HDInsight e individuare **Tipo cluster** in **Informazioni di base**. I tipi di sistema operativo del cluster sono elencati in questa pagina. 

### <a name="i-cant-migrate-to-an-hdinsight-linux-cluster-by-july-31-2018-what-is-the-impact-to-my-hdinsight-windows-cluster"></a>Impossibile eseguire la migrazione a un cluster HDInsight per Linux entro il 31 luglio 2018. Quali sono le conseguenze per il cluster HDInsight per Windows?
Il cluster HDInsight per Windows verrà eseguito "così come è". Non sarà invece possibile creare un nuovo cluster HDInsight su Windows o ridimensionarne uno esistente. 

### <a name="my-cluster-has-a-net-dependency-how-do-i-resolve-this-dependency-on-linux"></a>Il cluster include una dipendenza .NET. Come si risolve questa dipendenza su Linux?
È possibile risolvere la dipendenza del cluster per Linux tramite il [progetto Mono](http://www.mono-project.com/). Questa implementazione open source di .NET è disponibile per i cluster HDInsight su Linux. Per maggiori informazioni, fare riferimento al [documento di migrazione HDInsight](hdinsight-migrate-from-windows-to-linux.md). 

### <a name="im-a-new-customer-for-hdinsight-on-windows-how-can-i-create-an-hdinsight-windows-cluster"></a>Sono un nuovo cliente di HDInsight per Windows. Come è possibile creare un cluster HDInsight per Windows?
A partire dal 3 luglio 2017, solo i clienti esistenti di HDInsight per Windows possono creare nuovi cluster HDInsight per Windows. I nuovi clienti non possono creare un cluster HDInsight per Windows nel portale di Azure usando PowerShell o SDK. È consigliabile che i nuovi clienti creino un cluster HDInsight per Linux. I clienti esistenti possono creare nuovi cluster HDInsight per Windows fino alla data di ritiro di HDInsight per Windows. 

### <a name="is-there-a-pricing-impact-associated-with-moving-from-hdinsight-on-windows-to-hdinsight-on-linux"></a>Il passaggio da HDInsight per Windows a HDInsight per Linux influisce sui prezzi?
No, il piano tariffario è lo stesso per HDInsight su entrambi i sistemi operativi. 

### <a name="what-are-the-customer-advantages-associated-with-the-move-to-only-using-hdinsight-on-linux"></a>Quali sono i vantaggi per i clienti che passano a HDInsight utilizzando solo il sistema operativo Linux?
* Tempi di realizzazione più veloci per le tecnologie Big Data open source tramite il servizio HDInsight
* Sono disponibili una community e un ecosistema di grandi dimensioni per il supporto
* La community del software open source per Hadoop permette uno sviluppo più attivo e offre tecnologie Big Data innovative

### <a name="does-hdinsight-on-linux-provide-additional-functionality-beyond-what-is-available-in-hdinsight-on-windows"></a>HDInsight per Linux fornisce funzionalità aggiuntive oltre a quelle disponibili in HDInsight per Windows?
A partire da HDInsight versione 3.4, Microsoft ha rilasciato HDInsight solo per il sistema operativo Linux. Di conseguenza, alcuni dei componenti all'interno di HDInsight sono disponibili esclusivamente per Linux. Sono inclusi Apache Ranger, Kafka, Interactive Query, Spark, le applicazioni HDInsight e Azure Data Lake Store come file system primario. 

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Contratto di servizio per le versioni dei cluster HDInsight
Il Contratto di servizio (SLA) viene definito come _finestra di supporto_. Il termine finestra di supporto indica il periodo di tempo in cui una versione cluster HDInsight è supportata dal Supporto tecnico Microsoft. Un cluster HDInsight non è compreso nella finestra di supporto se la _data di scadenza del supporto_ della versione ha superato la data corrente. Per ulteriori informazioni sulle versioni supportate, vedere l'elenco delle [versioni cluster HDInsight supportate](hdinsight-migrate-from-windows-to-linux.md). La data di scadenza del supporto per una specifica versione di HDInsight X (dopo che sarà disponibile una versione X+1 più recente) viene calcolata come l'ultima di:  

* Formula 1: aggiungere 180 giorni alla data di rilascio del cluster HDInsight versione X.
* Formula 2: aggiungere 90 giorni alla data di rilascio del cluster HDInsight versione X+1 nel portale di Azure.

La _data di ritiro_ è la data dopo la quale non è possibile creare la versione del cluster su HDInsight. A partire dal 31 luglio 2017 non è possibile ridimensionare un cluster HDInsight dopo la data di ritiro. 

> [!NOTE]
> I cluster HDInsight su Windows (incluse le versioni 2.1, 3.0, 3.1, 3.2 e 3.3) eseguono il sistema operativo guest di Azure Family versione 4, che usa la versione a 64 bit di Windows Server 2012 R2. Il sistema operativo guest di Azure Family versione 4 supporta le versioni di .NET Framework 4.0, 4.5, 4.5.1 e 4.5.2.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Note sulla versione di Hortonworks associate alle versioni di HDInsight

La sezione fornisce collegamenti alle note sulla versione di Hortonworks Data Platform e i componenti di Apache utilizzati con HDInsight.
* Il cluster HDInsight versione 4.0 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 3.0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html).
* Il cluster HDInsight versione 3.6 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.6](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html).
* Il cluster HDInsight versione 3.5 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.5](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). La versione del cluster HDInsight 3.5 è il cluster Hadoop _predefinito_ che viene creato nel portale di Azure.
* Il cluster HDInsight versione 3.4 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html).
* Il cluster HDInsight versione 3.3 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).

  * Le [note sulla versione di Apache Storm](https://storm.apache.org/2015/11/05/storm0100-released.html) sono disponibili nel sito Web di Apache.
  * Le [note sulla versione di Apache Hive](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) sono disponibili nel sito Web di Apache.
* Il cluster HDInsight versione 3.2 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.2][hdp-2-2].

  * Note sulla versione per componenti specifici di Apache sono disponibili come segue: [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112) e [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* Il cluster HDInsight versione 3.1 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. I cluster HDInsight 3.1 creati prima del 7 novembre 2014 sono basati su [Hortonworks Data Platform 2.1.1][hdp-2-1-1].
* Il cluster HDInsight versione 3.0 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.0][hdp-2-0-8].
* Il cluster HDInsight versione 2.1 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 1.3][hdp-1-3-0].
* Il cluster HDInsight versione 1.6 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 1.1][hdp-1-1-0].






## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Configurazione del nodo predefinito e dimensioni della macchina virtuale per i cluster
La tabella seguente elenca le dimensioni di macchina virtuale (VM) predefinite per i cluster HDInsight.

> [!IMPORTANT]
> Se si prevedono più di 32 nodi di lavoro in un cluster, è necessario selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM.
> 
> 

* Tutte le aree supportate tranne Brasile meridionale e Giappone occidentale:

  | Tipo di cluster | Hadoop | hbase | Interactive Query | Storm | Spark | ML Server |
  | --- | --- | --- | --- | --- | --- | --- |
  | Head: dimensioni VM predefinite |D3 v2 |D3 v2 | D13, D14 |A4 v2 |D12 v2 |D12 v2 |
  | Head: dimensioni VM consigliate |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2  | D13, D14 |A4 v2, A8 v2, A2m v2 |D12 v2, D13 v2, D14 v2 |D12 v2, D13 v2, D14 v2 |
  | Ruolo di lavoro: dimensioni VM predefinite |D3 v2 |D3 v2  | D13, D14 |D3 v2 |Windows: D12 v2; Linux: D4 v2 |Windows: D12 v2; Linux: D4 v2 |
  | Ruolo di lavoro: dimensioni VM consigliate |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2  | D13, D14 |D3 v2, D4 v2, D12 v2 |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |
  | ZooKeeper: dimensioni VM predefinite | |A4 v2 | |A2 v2 | | |
  | ZooKeeper: dimensioni VM consigliate | |A4 v2, A8 v2, A2m v2 | | A2 v2, A4 v2, A8 v2 | | |
  | Edge: dimensioni VM predefinite | | | | | |Windows: D12 v2; Linux: D4 v2 |
  | Edge: dimensioni VM consigliate | | | | | |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |
* Solo Brasile meridionale e Giappone occidentale (non sono disponibili dimensioni v2):

  | Tipo di cluster | Hadoop | hbase | Interactive Query |Storm | Spark | ML Services |
  | --- | --- | --- | --- | --- | --- | --- |
  | Head: dimensioni VM predefinite |D3 |D3  | D13, D14 |A3 |D12 |D12 |
  | Head: dimensioni VM consigliate |D3, D4, D12 |D3, D4, D12  | D13, D14 |A3, A4, A5 |D12, D13, D14 |D12, D13, D14 |
  | Ruolo di lavoro: dimensioni VM predefinite |D3 |D3  | D13, D14 |D3 |Windows: D12; Linux: D4 |Windows: D12; Linux: D4 |
  | Ruolo di lavoro: dimensioni VM consigliate |D3, D4, D12 |D3, D4, D12  | D13, D14 |D3, D4, D12 |Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |
  | ZooKeeper: dimensioni VM predefinite | |A2 | | A2 | | |
  | ZooKeeper: dimensioni VM consigliate | |A2, A3, A4 | |A2, A3, A4 | | |
  | Edge: dimensioni VM predefinite | | | | | |Windows: D12; Linux: D4 |
  | Edge: dimensioni VM consigliate | | | | | |Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |

> [!NOTE]
> - Il nodo Head è chiamato *Nimbus* per il tipo di cluster Storm.
> - Il nodo Ruolo di lavoro è chiamato *Supervisor* per il tipo di cluster Storm.
> - Il nodo Ruolo di lavoro è chiamato *Regione* per il tipo di cluster HBase.

## <a name="next-steps"></a>Passaggi successivi
- [Configurazione del cluster per Apache Hadoop, Spark e altre informazioni su HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Lavorare da un computer Windows in Apache Hadoop su HDInsight](hdinsight-hadoop-windows-tools.md)

[Supported HDInsight versions]:(#supported-hdinsight-versions)

[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.9/bk_HDP_RelNotes/content/ch_relnotes_v229.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.1.1.16_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/
