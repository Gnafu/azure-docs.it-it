---
title: Trasformare dati tramite l'attività Hadoop MapReduce in Azure Data Factory | Microsoft Docs
description: Informazioni su come elaborare i dati eseguendo programmi Hadoop MapReduce in un cluster Azure HDInsight da un'istanza di Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: ccc194dd4120762a30da3ad28cdabed6faf53ba2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60611513"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Trasformare dati tramite l'attività Hadoop MapReduce in Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare la versione del servizio Data Factory in uso:"]
> * [Versione 1](v1/data-factory-map-reduce.md)
> * [Versione corrente](transform-data-using-hadoop-map-reduce.md)

L'attività HDInsight MapReduce in una [pipeline](concepts-pipelines-activities.md) di Data Factory richiama il programma MapReduce nei cluster HDInsight [personalizzati](compute-linked-services.md#azure-hdinsight-linked-service) o [su richiesta](compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Questo articolo si basa sull'articolo relativo alle [attività di trasformazione dei dati](transform-data.md) che presenta una panoramica generale della trasformazione dei dati e le attività di trasformazione supportate.

Se non si ha familiarità con Azure Data Factory, prima di leggere questo articolo vedere [Introduzione ad Azure Data Factory](introduction.md) ed eseguire l'esercitazione [Trasformare dati](tutorial-transform-data-spark-powershell.md).

Vedere [Pig](transform-data-using-hadoop-pig.md) e [Hive](transform-data-using-hadoop-hive.md) per informazioni dettagliate sull'esecuzione di script Pig/Hive in un cluster HDInsight da una pipeline tramite le attività HDInsight Pig e Hive.

## <a name="syntax"></a>Sintassi

```json
{
    "name": "Map Reduce Activity",
    "description": "Description",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.myorg.SampleClass",
        "jarLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "MyAzureStorage/jars/sample.jar",
        "getDebugInfo": "Failure",
        "arguments": [
            "-SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>Dettagli sintassi

| Proprietà          | Descrizione                              | Obbligatorio |
| ----------------- | ---------------------------------------- | -------- |
| name              | Nome dell'attività                     | Yes      |
| description       | Testo descrittivo per lo scopo dell'attività | No       |
| type              | Per l'attività MapReduce, il tipo di attività è HDinsightMapReduce | Yes      |
| linkedServiceName | Riferimento al cluster HDInsight registrato come servizio collegato in Data Factory. Per informazioni su questo servizio collegato, vedere l'articolo [Servizi collegati di calcolo](compute-linked-services.md). | Yes      |
| className         | Nome della classe da eseguire         | Yes      |
| jarLinkedService  | Riferimento a un servizio collegato di Archiviazione di Azure usato per archiviare i file Jar. Se non si specifica questo servizio collegato, viene usato il servizio collegato Archiviazione di Azure definito nel servizio collegato HDInsight. | No       |
| jarFilePath       | Specificare il percorso dei file Jar archiviati nel servizio Archiviazione di Azure indicato da jarLinkedService. Il nome del file distingue tra maiuscole e minuscole. | Yes      |
| jarlibs           | Percorso dei file di libreria Jar indicati dal processo archiviato nel servizio Archiviazione di Azure indicato da jarLinkedService. Il nome del file distingue tra maiuscole e minuscole. | No       |
| getDebugInfo      | Specifica quando i file di log vengono copiati nel servizio Archiviazione di Azure usato dal cluster HDInsight o specificato da jarLinkedService. Valori consentiti: None, Always e Failure. Valore predefinito: No. | No       |
| arguments         | Specifica una matrice di argomenti per un processo Hadoop. Gli argomenti vengono passati a ogni attività come argomenti della riga di comando. | No       |
| defines           | Specificare i parametri come coppie chiave/valore per fare riferimento a essi nello script Hive. | No       |



## <a name="example"></a>Esempio
È possibile usare l’attività MapReduce di HDInsight per l'esecuzione di file JAR di MapReduce in un cluster HDInsight. Nella definizione JSON seguente di una pipeline di esempio l'attività HDInsight è configurata per eseguire un file JAR di Mahout.

```json
{
    "name": "MapReduce Activity for Mahout",
    "description": "Custom MapReduce to generate Mahout result",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
        "jarLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
        "arguments": [
            "-s",
            "SIMILARITY_LOGLIKELIHOOD",
            "--input",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
            "--output",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
            "--maxSimilaritiesPerItem",
            "500",
            "--tempDir",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
        ]
    }
}
```
È possibile specificare eventuali argomenti per il programma MapReduce nella sezione **arguments**. In fase di esecuzione, vengono visualizzati alcuni argomenti aggiuntivi (ad esempio: mapreduce.job.tags) dal framework di MapReduce. Per differenziare gli argomenti con gli argomenti di MapReduce, è consigliabile usare sia l'opzione che il valore come argomenti, come illustrato nell'esempio seguente (- s, --input - output e così via sono opzioni immediatamente seguite dai valori).

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti, che illustrano altre modalità di trasformazione dei dati:

* [Attività U-SQL](transform-data-using-data-lake-analytics.md)
* [Attività Hive](transform-data-using-hadoop-hive.md)
* [Attività Pig](transform-data-using-hadoop-pig.md)
* [Attività Hadoop Streaming](transform-data-using-hadoop-streaming.md)
* [Attività Spark](transform-data-using-spark.md)
* [Attività personalizzata .NET](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Bach Execution Activity](transform-data-using-machine-learning.md) (Attività di esecuzione batch di Machine Learning)
* [Attività stored procedure](transform-data-using-stored-procedure.md)
