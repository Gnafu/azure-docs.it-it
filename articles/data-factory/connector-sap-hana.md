---
title: Copiare dati da SAP HANA usando Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da SAP HANA in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: cdd83c3ff9d34a5e8b7f2c164136ab82f498ffb5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60343767"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Copiare dati da SAP HANA usando Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare la versione del servizio Data Factory in uso:"]
> * [Versione 1](v1/data-factory-sap-hana-connector.md)
> * [Versione corrente](connector-sap-hana.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da un database SAP HANA. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da un database SAP HANA in qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore SAP HANA supporta:

- La copia di dati da qualsiasi versione del database SAP HANA.
- La copia di dati da **modelli di informazioni HANA** (ad esempio, dalle viste di analisi e calcolo) e da **tabelle Riga/Colonna** tramite query SQL.
- La copia di dati usando l'autenticazione **Di base** o **Windows**.

> [!NOTE]
> Per copiare dati **in** un archivio dati SAP HANA, usare il connettore ODBC generico. Per i dettagli, vedere [Sink SAP HANA](connector-odbc.md#sap-hana-sink). Si noti che i servizi collegati per i connettori SAP HANA e ODBC sono associati a tipi diversi e pertanto non possono essere riusati.

## <a name="prerequisites"></a>Prerequisiti

Per usare questo connettore SAP HANA, è necessario:

- Configurare un runtime di integrazione self-hosted. Per i dettagli, vedere l'articolo [Runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md).
- Installare il driver ODBC di SAP HANA nel computer del runtime di integrazione. È possibile scaricare il driver ODBC di SAP HANA dall'[area per il download di software SAP](https://support.sap.com/swdc). Per cercare il driver, usare la parola chiave **SAP HANA CLIENT for Windows**.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore SAP HANA.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di SAP HANA sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **SapHana** | Sì |
| server | Nome del server in cui si trova l'istanza di SAP HANA. Se il server usa una porta personalizzata, specificare `server:port`. | Sì |
| authenticationType | Tipo di autenticazione usato per la connessione al database SAP HANA.<br/>I valori consentiti sono i seguenti: **Basic** e **Windows** | Sì |
| userName | Nome dell'utente che ha accesso al server SAP. | Sì |
| password | Password per l'utente. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È necessario un runtime di integrazione self-hosted come indicato in [Prerequisiti](#prerequisites). |Sì |

**Esempio:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui set di dati. Questa sezione presenta un elenco delle proprietà supportate dal set di dati SAP HANA.

Per copiare dati da SAP HANA, impostare la proprietà type del set di dati su **RelationalTable**. Il set di dati SAP HANA di tipo RelationalTable non supporta alcuna proprietà specifica del tipo.

**Esempio:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine SAP HANA.

### <a name="sap-hana-as-source"></a>SAP HANA come origine

Per copiare dati da SAP HANA, impostare il tipo di origine nell'attività di copia su **RelationalSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine di attività di copia deve essere impostata su: **RelationalSource** | Sì |
| query | Specifica la query SQL che consente di leggere i dati dall'istanza di SAP HANA. | Sì |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "RelationalSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-hana"></a>Mapping dei tipi di dati per SAP HANA

Quando si copiano dati da SAP HANA, vengono usati i mapping seguenti tra i tipi di dati di SAP HANA e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo di dati di SAP HANA | Tipo di dati provvisori di Data Factory |
|:--- |:--- |
| ALPHANUM | String |
| BIGINT | Int64 |
| BLOB | Byte[] |
| BOOLEAN | Byte |
| CLOB | Byte[] |
| DATE | DateTime |
| DECIMAL | Decimal |
| DOUBLE | Single |
| INT | Int32 |
| NVARCHAR | String |
| REAL | Single |
| SECONDDATE | DateTime |
| SMALLINT | Int16 |
| TIME | TimeSpan |
| TIMESTAMP | DateTime |
| TINYINT | Byte |
| VARCHAR | String |

## <a name="known-limitations"></a>Limitazioni note

Quando si copiano dati da SAP HANA, è necessario tenere conto di alcune limitazioni:

- Le stringhe NVARCHAR vengono troncate alla lunghezza massima di 4000 caratteri Unicode
- SMALLDECIMAL non è supportato
- VARBINARY non è supportato
- Le date valide sono tra comprese tra il 30/12/1899 e il 31/12/9999


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
