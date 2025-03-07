---
title: RequestBodyTooLarge nei log per l'app Apache Spark streaming in Azure HDInsight
description: NativeAzureFileSystem ... RequestBodyTooLarge viene visualizzato nel log per Apache Spark app di streaming in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: b6e6d3eeff8569c8b00ac16310da3c94e484b32f
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088713"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"NativeAzureFileSystem... RequestBodyTooLarge "viene visualizzato nel registro applicazioni di Apache Spark streaming in HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di Apache Spark componenti nei cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Errore: `NativeAzureFileSystem ... RequestBodyTooLarge` viene visualizzato nel log driver per un'app di Apache Spark streaming.

## <a name="cause"></a>Causa

È probabile che il file di registro eventi Spark raggiunga il limite di lunghezza del file per WASB.

In Spark 2,3 ogni app Spark genera un file di log eventi Spark. Il file di registro eventi Spark per un'app Spark streaming continua a crescere mentre l'app è in esecuzione. Attualmente un file in WASB ha un limite di 50000 blocchi e le dimensioni predefinite del blocco sono pari a 4 MB. Quindi, nella configurazione predefinita le dimensioni massime del file sono di 195 GB. Tuttavia, archiviazione di Azure ha aumentato le dimensioni massime del blocco a 100 MB, che hanno consentito il limite di file singolo a 4,75 TB. Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

## <a name="resolution"></a>Risoluzione

Sono disponibili tre soluzioni per questo errore:

* Aumentare le dimensioni del blocco fino a 100 MB. Nell'interfaccia utente di Ambari modificare la proprietà `fs.azure.write.request.size` di configurazione HDFS (o `Custom core-site` crearla nella sezione). Impostare la proprietà su un valore più grande, ad esempio: 33554432. Salvare la configurazione aggiornata e riavviare i componenti interessati.

* Arrestare periodicamente e inviare nuovamente il processo di Spark-streaming.

* Usare HDFS per archiviare i log eventi di Spark. L'uso di HDFS per l'archiviazione può comportare la perdita di dati evento Spark durante il ridimensionamento del cluster o gli aggiornamenti di Azure

    1. Apportare modifiche a `spark.eventlog.dir` e `spark.history.fs.logDirectory` tramite l'interfaccia utente di Ambari:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Creare directory in HDFS:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Riavviare tutti i servizi interessati tramite l'interfaccia utente di Ambari.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
