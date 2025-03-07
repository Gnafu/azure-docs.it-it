---
title: Formato parquet in Azure Data Factory | Microsoft Docs
description: In questo argomento viene descritto come gestire il formato parquet in Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 572547f4e22a4fcb63a030e64ca95a0b9d3eff00
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68734478"
---
# <a name="parquet-format-in-azure-data-factory"></a>Formato parquet in Azure Data Factory

Seguire questo articolo quando si desidera **analizzare i file parquet o scrivere i dati in formato parquet**. 

Il formato parquet è supportato per i connettori seguenti: [Amazon S3](connector-amazon-simple-storage-service.md), [BLOB di Azure](connector-azure-blob-storage.md), [Azure Data Lake storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md), [archiviazione file di Azure](connector-azure-file-storage.md), [file System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati parquet.

| Proprietà         | Descrizione                                                  | Obbligatoria |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | La proprietà Type del set di dati deve essere impostata su **parquet**. | Sì      |
| location         | Impostazioni del percorso dei file. Ogni connettore basato su file ha un tipo di percorso e proprietà supportate in `location`. **Per informazioni dettagliate, vedere l'articolo connettore-> sezione Proprietà set di dati**. | Sì      |
| compressionCodec | Codec di compressione da usare durante la scrittura in file parquet. Durante la lettura da file parquet, Data Factory determina automaticamente il codec di compressione basato sui metadati del file.<br>I tipi supportati sono "**None**", "**gzip**", "**Snapper**" (impostazione predefinita) e "**LZO**". Nota Attualmente l'attività di copia non supporta LZO. | No       |

> [!NOTE]
> Lo spazio vuoto nel nome della colonna non è supportato per i file parquet.

Di seguito è riportato un esempio di set di dati parquet nell'archivio BLOB di Azure:

```json
{
    "name": "ParquetDataset",
    "properties": {
        "type": "Parquet",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink parquet.

### <a name="parquet-as-source"></a>Parquet come origine

Nella sezione ***\*origine\**** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **ParquetSource**. | Sì      |
| storeSettings | Un gruppo di proprietà su come leggere i dati da un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di lettura supportate `storeSettings`in. **Per informazioni dettagliate, vedere l'articolo connettore > sezione proprietà dell'attività di copia**. | No       |

### <a name="parquet-as-sink"></a>Parquet come sink

Nella sezione ***\*sink\**** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **ParquetSink**. | Sì      |
| storeSettings | Gruppo di proprietà su come scrivere dati in un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di scrittura supportate `storeSettings`in. **Per informazioni dettagliate, vedere l'articolo connettore > sezione proprietà dell'attività di copia**. | No       |

## <a name="mapping-data-flow-properties"></a>Mapping delle proprietà del flusso di dati

Per informazioni dettagliate, vedere [trasformazione origine](data-flow-source.md) e [trasformazione sink](data-flow-sink.md) nel flusso di dati del mapping.

## <a name="data-type-support"></a>Supporto dei tipi di dati

I tipi di dati parquet complessi non sono attualmente supportati (ad esempio, MAP, LIST, STRUCT).

## <a name="using-self-hosted-integration-runtime"></a>Uso di Integration Runtime self-hosted

> [!IMPORTANT]
> Per le copie attivate dal runtime di integrazione self-hosted, ad esempio tra l'archivio dati locale e quello nel cloud, se non si esegue una copia **identica** dei file Parquet, è necessario installare **JRE 8 (Java Runtime Environment) a 64 bit o OpenJDK** nel computer del runtime di integrazione. Per informazioni più dettagliate, vedere il paragrafo seguente.

Per la copia in esecuzione nel runtime di integrazione self-hosted con la serializzazione/deserializzazione dei file Parquet, il file di definizione dell'applicazione (AFD) individua il runtime Java eseguendo prima una ricerca di JRE nel Registro di sistema *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* . In caso di esito negativo, esegue una ricerca di OpenJDK nella variabile di sistema *`JAVA_HOME`* .

- **Per usare JRE**: il runtime di integrazione a 64 bit richiede JRE a 64 bit disponibile [qui](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Per usare OpenJDK**: è supportato a partire dalla versione 3.13 del runtime di integrazione. Includere jvm.dll in un pacchetto con tutti gli altri assembly necessari di OpenJDK nel computer del runtime di integrazione self-hosted e impostare di conseguenza la variabile di ambiente di sistema JAVA_HOME.

> [!TIP]
> Se si copiano i dati nel/dal formato Parquet usando il runtime di integrazione self-hosted e si verifica l'errore "An error occurred when invoking java, message: **java.lang.OutOfMemoryError:Java heap space**" (Errore durante la chiamata di Java, messaggio: java.lang.OutOfMemoryError: spazio dell'heap di Java), è possibile aggiungere una variabile di ambiente `_JAVA_OPTIONS` nel computer che ospita il runtime di integrazione self-hosted per regolare le dimensioni min/max dell'heap per JVM e poter ottimizzare la copia, quindi eseguire di nuovo la pipeline.

![Impostare le dimensioni dell'heap JVM nel runtime di integrazione self-hosted](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Esempio: impostare la variabile `_JAVA_OPTIONS` con il valore `-Xms256m -Xmx16g`. Il flag `Xms` specifica il pool di allocazione della memoria iniziale per Java Virtual Machine (JVM), mentre `Xmx` specifica il pool di allocazione della memoria massima. JVM verrà quindi avviato con una quantità di memoria pari a `Xms` e potrà usare una quantità massima di memoria pari a `Xmx`. Per impostazione predefinita, Azure Data Factory usa una quantità di memoria minima pari a 64 MB e una quantità di memoria massima pari a 1 GB.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Mapping del flusso di dati](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
