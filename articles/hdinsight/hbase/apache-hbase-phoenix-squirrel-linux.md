---
title: Usare Apache Phoenix e SQLLine con HBase in Azure HDInsight | Microsoft Docs
description: Informazioni su come usare Apache Phoenix in HDInsight. Vengono fornite anche informazioni su come installare e configurare SQLLine nel computer in uso per connettersi a un cluster HBase in HDInsight.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: cda0f33b-a2e8-494c-972f-ae0bb482b818
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: 64700567b8acf816f42e6bf8cdc5386b6c65fe3f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
---
# <a name="use-apache-phoenix-with-linux-based-hbase-clusters-in-hdinsight"></a>Usare Apache Phoenix con cluster HBase basati su Linux in HDinsight
Questo articolo fornisce informazioni su come usare [Apache Phoenix](http://phoenix.apache.org/) in Azure HDInsight e su come usare SQLLine. Per altre informazioni su Phoenix, vedere la [breve panoramica su Phoenix](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Per la grammatica di Phoenix, vedere l'articolo relativo alla [grammatica di Phoenix](http://phoenix.apache.org/language/index.html).

> [!NOTE]
> Per informazioni sulla versione di Phoenix in relazione a HDInsight, vedere [Novità delle versioni dei cluster Hadoop fornite da HDInsight](../hdinsight-component-versioning.md).
>
>

## <a name="use-sqlline"></a>Usare SQLLine
[SQLLine](http://sqlline.sourceforge.net/) è un'utilità della riga di comando per eseguire SQL.

### <a name="prerequisites"></a>prerequisiti
Per usare SQLLine sono necessari gli elementi seguenti:

* **Un cluster HBase in HDInsight**. Per crearne uno, vedere [Introduzione ad Apache HBase in HDInsight](./apache-hbase-tutorial-get-started-linux.md).

Quando ci si connette a un cluster HBase, è necessario connettersi a una delle macchine virtuali ZooKeeper. Ogni cluster HDInsight ha tre macchine virtuali ZooKeeper.

**Per ottenere il nome host ZooKeeper**

1. Aprire Ambari passando ad **https://\<nome cluster\>.azurehdinsight.net**.
2. Per accedere, immettere nome utente e password HTTP (cluster).
3. Nel menu a sinistra selezionare **ZooKeeper**. Vengono elencate tre istanze di **ZooKeeper Server**.
4. Selezionare una delle istanze di **ZooKeeper Server**. Nel riquadro **Riepilogo** trovare **Nome host**. Il nome è simile a *zk1-jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Per usare SQLLine**

1. Permette di connettersi al cluster tramite SSH. Per altre informazioni, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. In SSH usare i comandi seguenti per eseguire SQLLine:

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ZOOKEEPER SERVER FQDN>:2181:/hbase-unsecure
3. Per creare una tabella HBase e inserire alcuni dati, eseguire questi comandi:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

        !quit

Per altre informazioni, vedere il [manuale di SQLLine](http://sqlline.sourceforge.net/#manual) e la [grammatica di Phoenix](http://phoenix.apache.org/language/index.html).

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come usare Apache Phoenix in HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* [Panoramica di HBase di HDInsight][hdinsight-hbase-overview].
  HBase è un database NoSQL open source Apache basato su Hadoop che fornisce accesso rapido e coerenza assoluta per quantità elevate di dati non strutturati e semi-strutturati.
* [Effettuare il provisioning di cluster HBase in Rete virtuale di Azure][hdinsight-hbase-provision-vnet].
  Grazie all'integrazione con la rete virtuale, è possibile distribuire i cluster HBase nella stessa rete virtuale delle applicazioni, consentendo così alle applicazioni di comunicare direttamente con HBase.
* [Configurare la replica geografica di HBase in HDInsight](apache-hbase-replication.md). Informazioni su come configurare la replica di HBase tra due data center di Azure.


[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp
[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-hbase-overview]:apache-hbase-overview.md


