---
title: Inserire dati usando la libreria di Esplora dati di Azure per Python
description: Questo articolo illustra come inserire (caricare) i dati in Azure Esplora dati usando Python.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: f109f2dd45fe90884d3947b244b3dafffd547725
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68355928"
---
# <a name="ingest-data-using-the-azure-data-explorer-python-library"></a>Inserire dati usando la libreria di Esplora dati di Azure per Python

Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. Esplora dati di Azure offre due librerie client per Python: una [libreria di inserimento](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-ingest) e [una libreria di dati](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data). Queste librerie consentono di inserire (caricare) i dati in un cluster ed eseguire una query di dati dal codice. In questo articolo viene innanzitutto creata una tabella e il mapping dei dati in un cluster. Quindi viene accodato l'inserimento nel cluster e vengono convalidati i risultati.

Questo articolo è disponibile anche come [notebook di Azure](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueuedIngestSingleBlob.ipynb).

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

* [Un cluster e un database](create-cluster-database-portal.md)

* [Python](https://www.python.org/downloads/) installato nel computer di sviluppo

## <a name="install-the-data-and-ingest-libraries"></a>Installare le librerie di dati e di inserimento

Installare *Installareo* e *azure-kusto-ingest*.

```
pip install azure-kusto-data
pip install azure-kusto-ingest
```

## <a name="add-import-statements-and-constants"></a>Aggiungere le costanti e le istruzioni import

Importare le classi da azure-kusto-data.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
from azure.kusto.data.helpers import dataframe_from_result_table
```

Per autenticare un'applicazione, Esplora dati di Azure usa l'ID tenant AAD. Per trovare l'ID del tenant, usare l'URL seguente, sostituendo il dominio a *YourDomain*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Ad esempio, se il dominio è *contoso.com*, l'URL è: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Fare clic su questo URL per visualizzare i risultati; la prima riga è come indicato di seguito. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Il tenant ID in questo caso è `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. Impostare i valori per AAD_TENANT_ID KUSTO_URI, KUSTO_INGEST_URI e KUSTO_DATABASE prima di eseguire questo codice.

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_URI = "https://<ClusterName>.<Region>.kusto.windows.net:443/"
KUSTO_INGEST_URI = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
KUSTO_DATABASE = "<DatabaseName>"
```

Costruire ora la stringa di connessione. Questo esempio usa l'autenticazione del dispositivo per accedere al cluster. È anche possibile usare il [certificato dell'applicazione AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L24), la [chiave dell'applicazione Azure Active Directory](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L20) e l'[utente e password di AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L34).

Creare la tabella di destinazione e il mapping in un passaggio successivo.

```python
KCSB_INGEST = KustoConnectionStringBuilder.with_aad_device_authentication(
    KUSTO_INGEST_URI, AAD_TENANT_ID)

KCSB_DATA = KustoConnectionStringBuilder.with_aad_device_authentication(
    KUSTO_URI, AAD_TENANT_ID)

DESTINATION_TABLE = "StormEvents"
DESTINATION_TABLE_COLUMN_MAPPING = "StormEvents_CSV_Mapping"
```

## <a name="set-source-file-information"></a>Impostare le informazioni sul file di origine

Importare le classi aggiuntive e impostare le costanti per il file di origine dati. Questo esempio usa un file di esempio ospitato nell'archiviazione BLOB di Azure. Il set di dati di esempio **StormEvents** contiene dati relativi al meteo del [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

```python
from azure.storage.blob import BlockBlobService
from azure.kusto.ingest import KustoIngestClient, IngestionProperties, FileDescriptor, BlobDescriptor, DataFormat, ReportLevel, ReportMethod

CONTAINER = "samplefiles"
ACCOUNT_NAME = "kustosamplefiles"
SAS_TOKEN = "?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D"
FILE_PATH = "StormEvents.csv"
FILE_SIZE = 64158321    # in bytes

BLOB_PATH = "https://" + ACCOUNT_NAME + ".blob.core.windows.net/" + \
    CONTAINER + "/" + FILE_PATH + SAS_TOKEN
```

## <a name="create-a-table-on-your-cluster"></a>Creare una tabella nel cluster

Creare una tabella che corrisponde allo schema dei dati nel file StormEvents.csv. Quando viene eseguito, questo codice restituisce un messaggio simile al seguente: *Per accedere, usare un Web browser per aprire la pagina https://microsoft.com/devicelogin e immettere il codice F3W4VWZDM per eseguire l'autenticazione*. Seguire i passaggi per l'accesso, quindi tornare a eseguire il blocco di codice successivo. I blocchi di codice successivi che stabiliscono una connessione richiedono di eseguire nuovamente l'accesso.

```python
KUSTO_CLIENT = KustoClient(KCSB_DATA)
CREATE_TABLE_COMMAND = ".create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)"

RESPONSE = KUSTO_CLIENT.execute_mgmt(KUSTO_DATABASE, CREATE_TABLE_COMMAND)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="define-ingestion-mapping"></a>Definire il mapping di inserimento

Eseguire il mapping dei dati CSV in ingresso sui nomi di colonna e tipi di dati usati durante la creazione della tabella. Associa in modo deterministico i campi dati di origine alle colonne della tabella di destinazione

```python
CREATE_MAPPING_COMMAND = """.create table StormEvents ingestion csv mapping 'StormEvents_CSV_Mapping' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EpisodeId","datatype":"int","Ordinal":2},{"Name":"EventId","datatype":"int","Ordinal":3},{"Name":"State","datatype":"string","Ordinal":4},{"Name":"EventType","datatype":"string","Ordinal":5},{"Name":"InjuriesDirect","datatype":"int","Ordinal":6},{"Name":"InjuriesIndirect","datatype":"int","Ordinal":7},{"Name":"DeathsDirect","datatype":"int","Ordinal":8},{"Name":"DeathsIndirect","datatype":"int","Ordinal":9},{"Name":"DamageProperty","datatype":"int","Ordinal":10},{"Name":"DamageCrops","datatype":"int","Ordinal":11},{"Name":"Source","datatype":"string","Ordinal":12},{"Name":"BeginLocation","datatype":"string","Ordinal":13},{"Name":"EndLocation","datatype":"string","Ordinal":14},{"Name":"BeginLat","datatype":"real","Ordinal":16},{"Name":"BeginLon","datatype":"real","Ordinal":17},{"Name":"EndLat","datatype":"real","Ordinal":18},{"Name":"EndLon","datatype":"real","Ordinal":19},{"Name":"EpisodeNarrative","datatype":"string","Ordinal":20},{"Name":"EventNarrative","datatype":"string","Ordinal":21},{"Name":"StormSummary","datatype":"dynamic","Ordinal":22}]'"""

RESPONSE = KUSTO_CLIENT.execute_mgmt(KUSTO_DATABASE, CREATE_MAPPING_COMMAND)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="queue-a-message-for-ingestion"></a>Accodare un messaggio per l'inserimento

Accodare un messaggio per eseguire il pull dei dati dall'archiviazione BLOB e inserire i dati in Esplora dati di Azure.

```python
INGESTION_CLIENT = KustoIngestClient(KCSB_INGEST)

# All ingestion properties are documented here: https://docs.microsoft.com/azure/kusto/management/data-ingest#ingestion-properties
INGESTION_PROPERTIES = IngestionProperties(database=KUSTO_DATABASE, table=DESTINATION_TABLE, dataFormat=DataFormat.csv,
                                           mappingReference=DESTINATION_TABLE_COLUMN_MAPPING, additionalProperties={'ignoreFirstRecord': 'true'})
# FILE_SIZE is the raw size of the data in bytes
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
INGESTION_CLIENT.ingest_from_blob(
    BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)

print('Done queuing up ingestion with Azure Data Explorer')
```

## <a name="query-data-that-was-ingested-into-the-table"></a>Eseguire query sui dati che sono stati inseriti nella tabella

Attendere da cinque a dieci minuti per l'operazione di inserimento in coda per pianificare l'inserimento e caricare i dati in Esplora dati di Azure. Eseguire quindi il codice seguente per ottenere il numero di record nella tabella StormEvents.

```python
QUERY = "StormEvents | count"

RESPONSE = KUSTO_CLIENT.execute_query(KUSTO_DATABASE, QUERY)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="run-troubleshooting-queries"></a>Eseguire query sulla risoluzione dei problemi

Accedere al [https://dataexplorer.azure.com](https://dataexplorer.azure.com) e connettersi al cluster. Eseguire il comando seguente nel database per verificare la presenza di eventuali errori di inserimento nelle ultime quattro ore. Sostituire il nome del database prima dell'esecuzione.

```Kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

Eseguire il comando seguente per visualizzare lo stato di tutte le operazioni di inserimento nelle ultime quattro ore. Sostituire il nome del database prima dell'esecuzione.

```Kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Table == "StormEvents" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di seguire gli altri articoli, è necessario salvare le risorse create. In caso contrario, eseguire il comando seguente nel database per pulire la tabella StormEvents.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire query sui dati con Python](python-query-data.md)
