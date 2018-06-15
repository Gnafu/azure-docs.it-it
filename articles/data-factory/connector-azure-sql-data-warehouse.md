---
title: Copiare dati da/in Azure SQL Data Warehouse usando Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da archivi di origine supportati in Azure SQL Data Warehouse o da SQL Data Warehouse in archivi di sink supportati usando Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/28/2018
ms.author: jingwang
ms.openlocfilehash: c862f269a8e32814dfb6d311706e65b57d52d1bb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34617077"
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Copiare dati da o in Azure SQL Data Warehouse usando Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Versione 2 - Anteprima](connector-azure-sql-data-warehouse.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in Azure SQL Data Warehouse. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere le informazioni sul [connettore Azure SQL Data Warehouse nella versione 1](v1/data-factory-azure-sql-data-warehouse-connector.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da Azure SQL Data Warehouse in qualsiasi archivio dati di sink supportato o da qualsiasi archivio dati di origine supportato in Azure SQL Data Warehouse. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore Azure SQL Data Warehouse supporta:

- La copia di dati tramite l'**autenticazione SQL** e l'**autenticazione token dell'applicazione di Azure Active Directory** con entità servizio o identità del servizio gestito.
- Come origine, il recupero di dati tramite query SQL o stored procedure.
- Come sink, il caricamento di dati tramite **PolyBase** o l'inserimento bulk. Per ottimizzare le prestazioni di copia, è **consigliabile** usare il primo metodo.

> [!IMPORTANT]
> Si noti che PolyBase supporta solo l'autenticazione SQL, non l'autenticazione Azure Active Directory.

> [!IMPORTANT]
> Se si copiano i dati tramite il runtime di integrazione di Azure, configurare il [firewall del server SQL di Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) per [consentire ai servizi di Azure di accedere al server](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Se si copiano i dati tramite il runtime di integrazione self-hosted, configurare il firewall del server SQL di Azure per consentire un intervallo di indirizzi IP appropriato, incluso l'indirizzo IP del computer usato per la connessione al database SQL di Azure.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano le informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore Azure SQL Data Warehouse.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Azure SQL Data Warehouse sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà del tipo deve essere impostata su: **AzureSqlDW** | Sì |
| connectionString |Specificare le informazioni necessarie per connettersi all'istanza di Azure SQL Data Warehouse per la proprietà connectionString. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |Sì |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Sì, quando si usa l'autenticazione AAD con entità servizio. |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì, quando si usa l'autenticazione AAD con entità servizio. |
| tenant | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. È possibile recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì, quando si usa l'autenticazione AAD con entità servizio. |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No  |

Per altri tipi di autenticazione, fare riferimento alle sezioni seguenti relative, rispettivamente, ai prerequisiti e agli esempi JSON:

- [Uso dell'autenticazione SQL](#using-sql-authentication)
- [Uso dell'autenticazione token dell'applicazione di AAD: entità servizio](#using-service-principal-authentication)
- [Uso dell'autenticazione token dell'applicazione di AAD: identità del servizio gestito](#using-managed-service-identity-authentication)

### <a name="using-sql-authentication"></a>Uso dell'autenticazione SQL

**Esempio di servizio collegato tramite l'autenticazione SQL:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-service-principal-authentication"></a>Uso dell'autenticazione basata sull'entità servizio

Per usare l'autenticazione token dell'applicazione di AAD basata sull'entità servizio, seguire questa procedura:

1. **[Creare un'applicazione di Azure Active Directory nel portale di Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application).**  Prendere nota del nome dell'applicazione e dei valori seguenti da usare per definire il servizio collegato:

    - ID applicazione
    - Chiave applicazione
    - ID tenant

2. **[Effettuare il provisioning di un amministratore di Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** per il server SQL di Azure nel portale di Azure, se l'operazione non è già stata eseguita. L'amministratore di AAD può essere un utente o un gruppo di AAD. Se si concede al gruppo con identità del servizio gestito un ruolo di amministratore, ignorare i passaggi 3 e 4 riportati di seguito, in quanto l'amministratore avrà l'accesso completo al database.

3. **Creare un utente di database indipendente per l'entità servizio** tramite la connessione al data warehouse da/verso cui si vogliono copiare i dati usando strumenti come SQL Server Management Studio, con un'identità di AAD che abbia almeno l'autorizzazione ALTER ANY USER, e l'esecuzione del comando in T-SQL seguente. Per altre informazioni sull'utente di database indipendente, consultare [questo articolo](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Concedere all'entità servizio le autorizzazioni necessarie**, come si fa di norma per gli utenti SQL, ad esempio tramite l'esecuzione del codice seguente:

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. Nel file di definizione dell'applicazione (ADF) configurare un servizio collegato ad Azure SQL Data Warehouse.


**Esempio di servizio collegato tramite l'autenticazione basata su entità servizio:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-managed-service-identity-authentication"></a>Uso dell'autenticazione basata sull'identità del servizio gestito

Una data factory può essere associata a un'[identità del servizio gestito](data-factory-service-identity.md), che rappresenta la data factory specifica. È possibile usare questa identità del servizio per l'autenticazione di Azure SQL Data Warehouse, che consente a questa factory designata di accedere e copiare i dati dal o nel database.

> [!IMPORTANT]
> Si noti che PolyBase non è attualmente supportato per l'autenticazione tramite identità del servizio gestito.

Per usare l'autenticazione token dell'applicazione di AAD basata sull'identità del servizio gestito, seguire questa procedura:

1. **Creare un gruppo in Azure AD e aggiungere l'identità del servizio gestito della factory come membro del gruppo**.

    a. Trovare l'identità del servizio della data factory nel portale di Azure. Passare alla data factory -> Proprietà -> copiare il valore di **ID IDENTITÀ DEL SERVIZIO**.

    b. Installare il modulo [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2), eseguire l'accesso usando il comando `Connect-AzureAD` ed eseguire i comandi seguenti per creare un gruppo e aggiungere l'identità del servizio gestito della data factory come membro.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

2. **[Effettuare il provisioning di un amministratore di Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** per il server SQL di Azure nel portale di Azure, se l'operazione non è già stata eseguita.

3. **Creare un utente di database indipendente per il gruppo di AAD** tramite la connessione al data warehouse da/verso cui si vogliono copiare i dati usando strumenti come SQL Server Management Studio, con un'identità di AAD che abbia almeno l'autorizzazione ALTER ANY USER, e l'esecuzione del comando in T-SQL seguente. Per altre informazioni sull'utente di database indipendente, consultare [questo articolo](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

4. **Concedere al gruppo di AAD le autorizzazioni necessarie**, come si fa di norma per gli utenti SQL, ad esempio tramite l'esecuzione del codice seguente:

    ```sql
    EXEC sp_addrolemember [role name], [your AAD group name];
    ```

5. Nel file di definizione dell'applicazione (ADF) configurare un servizio collegato ad Azure SQL Data Warehouse.

**Esempio di servizio collegato tramite l'autenticazione basata sull'identità del servizio gestito:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui set di dati. Questa sezione presenta un elenco delle proprietà supportate dal set di dati Azure SQL Data Warehouse.

Per copiare dati da/in Azure SQL Data Warehouse, impostare la proprietà type del set di dati su **AzureSqlDWTable**. Sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **AzureSqlDWTable** | Sì |
| tableName |Nome della tabella o vista nell'istanza di Azure SQL Data Warehouse a cui fa riferimento il servizio collegato. | Sì |

**Esempio:**

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink Azure SQL Data Warehouse.

### <a name="azure-sql-data-warehouse-as-source"></a>Azure SQL Data Warehouse come origine

Per copiare dati da Azure SQL Data Warehouse, impostare il tipo di origine nell'attività di copia su **SqlDWSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **SqlDWSource** | Sì |
| SqlReaderQuery |Usare la query SQL personalizzata per leggere i dati. Esempio: `select * from MyTable`. |No  |
| sqlReaderStoredProcedureName |Nome della stored procedure che legge i dati dalla tabella di origine. L'ultima istruzione SQL deve essere un'istruzione SELECT nella stored procedure. |No  |
| storedProcedureParameters |Parametri per la stored procedure.<br/>I valori consentiti sono: coppie nome/valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. |No  |

**Punti da notare:**

- Se per SqlSource è specificata la proprietà **sqlReaderQuery**, l'attività di copia esegue questa query nell'origine Azure SQL Data Warehouse per ottenere i dati. In alternativa, è possibile specificare una stored procedure indicando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se la stored procedure accetta parametri).
- Se non si specifica né "sqlReaderQuery" né "sqlReaderStoredProcedureName", le colonne definite nella sezione "struttura" del set di dati JSON vengono usate per creare una query, `select column1, column2 from mytable`, da eseguire in Azure SQL Data Warehouse. Se nella definizione del set di dati non è inclusa la "struttura", vengono selezionate tutte le colonne della tabella.
- Quando si usa **sqlReaderStoredProcedureName** è necessario specificare un valore fittizio per la proprietà **tableName** nel set di dati JSON.

**Esempio: uso della query SQL**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Esempio: uso della stored procedure**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Definizione della stored procedure:**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-data-warehouse-as-sink"></a>Azure SQL Data Warehouse come sink

Per copiare dati in Azure SQL Data Warehouse, impostare il tipo di sink nell'attività di copia su **SqlDWSink**. Nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del sink dell'attività di copia deve essere impostata su: **SqlDWSink** | Sì |
| allowPolyBase |Indica se usare PolyBase, quando applicabile, invece del meccanismo BULKINSERT. <br/><br/> **L'uso di PolyBase è il modo consigliato per caricare dati in SQL Data Warehouse.** Per informazioni su vincoli e dettagli, vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) .<br/><br/>I valori consentiti sono: **True**, **False** (predefinito).  |No  |
| polyBaseSettings |Gruppo di proprietà che è possibile specificare quando la proprietà **allowPolybase** è impostata su **true**. |No  |
| rejectValue |Specifica il numero o la percentuale di righe che è possibile rifiutare prima che la query abbia esito negativo.<br/><br/>Per altre informazioni sulle opzioni di rifiuto di PolyBase, vedere la sezione **Arguments** (Argomenti) in [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) . <br/><br/>I valori consentiti sono: 0 (predefinito), 1, 2, ... |No  |
| rejectType |Indica se l'opzione rejectValue viene specificata come valore letterale o come percentuale.<br/><br/>I valori consentiti sono: **Valore** (predefinito), **Percentuale**. |No  |
| rejectSampleValue |Determina il numero di righe da recuperare prima che PolyBase ricalcoli la percentuale di righe rifiutate.<br/><br/>I valori consentiti sono: 1, 2, … |Sì se **rejectType** è **percentage** |
| useTypeDefault |Specifica come gestire i valori mancanti nei file con testo delimitato quando PolyBase recupera dati dal file di testo.<br/><br/>Per altre informazioni su questa proprietà, vedere la sezione Arguments (Argomenti) in [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>I valori consentiti sono: **True**, **False** (predefinito). |No  |
| writeBatchSize |Inserisce dati nella tabella SQL quando la dimensione del buffer raggiunge writeBatchSize. Si applica solo se non viene usato PolyBase.<br/><br/>I valori consentiti sono: integer (numero di righe). |No (il valore predefinito è 10.000) |
| writeBatchTimeout |Tempo di attesa per l'operazione di inserimento batch da completare prima del timeout. Si applica solo se non viene usato PolyBase.<br/><br/>I valori consentiti sono: intervallo di tempo. Ad esempio: "00:30:00" (30 minuti). |No  |
| preCopyScript |Specificare una query SQL per l'attività di copia da eseguire prima di scrivere i dati in Azure SQL Data Warehouse ad ogni esecuzione. È possibile usare questa proprietà per pulire i dati precaricati. |No  |(#repeatability-during-copy). |Istruzione di query. |No  |

**Esempio:**

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

Per altre informazioni su come usare PolyBase per caricare in modo efficiente in SQL Data Warehouse, vedere la sezione successiva.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Usare PolyBase per caricare dati in Azure SQL Data Warehouse

**[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** consente di caricare con efficacia grandi quantità di dati in Azure SQL Data Warehouse con una velocità effettiva elevata. L'uso di PolyBase consente un miglioramento significativo della velocità effettiva rispetto al meccanismo BULKINSERT predefinito. Vedere [Copiare il numero di riferimento prestazioni](copy-activity-performance.md#performance-reference) con il confronto dettagliato. Per la procedura dettagliata con un caso d'uso, vedere [Caricare 1 TB di dati in Azure SQL Data Warehouse in meno di 15 minuti con Azure Data Factory](connector-azure-sql-data-warehouse.md).

* Se il formato dei dati di origine è **BLOB di Azure o Azure Data Lake Store** e compatibile con PolyBase, è possibile eseguire la copia direttamente in Azure SQL Data Warehouse usando PolyBase. Vedere **[Copia diretta tramite PolyBase](#direct-copy-using-polybase)** con i relativi dettagli.
* Se l'archivio e il formato dei dati di origine non sono supportati in origine da PolyBase, è possibile usare la funzione **[copia di staging tramite PolyBase](#staged-copy-using-polybase)**. Viene inoltre generata una migliore velocità effettiva tramite la conversione automatica dei dati nel formato compatibile con PolyBase e l'archiviazione dei dati in Archiviazione BLOB di Azure. Vengono quindi caricati i dati in SQL Data Warehouse.

> [!IMPORTANT]
> Si noti che PolyBase non è attualmente supportato per l'autenticazione tramite token applicazione AAD basata sull'identità del servizio gestita.

### <a name="direct-copy-using-polybase"></a>Copia diretta tramite PolyBase

PolyBase di SQL Data Warehouse supporta direttamente Archiviazione BLOB di Azure e Azure Data Lake Store (mediante l'entità servizio) come origine e con requisiti di formato di file specifico. Se i dati di origine soddisfano i criteri descritti in questa sezione, è possibile eseguire la copia direttamente dall'archivio dati di origine ad Azure SQL Data Warehouse con PolyBase. In caso contrario è possibile usare la [copia di staging tramite PolyBase](#staged-copy-using-polybase).

> [!TIP]
> Nell'articolo [Azure Data Factory makes it even easier and convenient to uncover insights from data when using Data Lake Store with SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/) (Con Azure Data Factory è ancora più semplice e pratico individuare informazioni utili sui dati quando si usa Data Lake Store con SQL Data Warehouse) sono indicate altre informazioni utili per copiare efficacemente i dati da Data Lake Store a SQL Data Warehouse.

Se i requisiti non vengono soddisfatti, Azure Data Factory controlla le impostazioni e usa automaticamente il meccanismo BULKINSERT per lo spostamento dei dati.

1. Il **servizio collegato all'origine** è di tipo: **AzureStorage** o **AzureDataLakeStore** con autenticazione entità servizio.
2. Il **set di dati di input** è di tipo **AzureBlob** o **AzureDataLakeStoreFile** e il tipo di formato nelle proprietà `type` è **OrcFormat** **ParquetFormat** o **TextFormat**, con le configurazioni seguenti:

   1. `rowDelimiter` deve essere **\n**.
   2. `nullValue` è impostato su **stringa vuota** ("") o `treatEmptyAsNull` è impostato su **true**.
   3. `encodingName` è impostato su **utf-8**, ovvero il valore **predefinito**.
   4. `escapeChar`, `quoteChar`, `firstRowAsHeader` e `skipLineCount` non sono specificati.
   5. `compression` può essere **no compression**, **GZip** o **Deflate**.

    ```json
    "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8"
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
    },
    ```

3. Non è disponibile alcuna impostazione `skipHeaderLineCount` in **BlobSource** o **AzureDataLakeStore** per l'attività di copia nella pipeline.
4. Non è disponibile alcuna impostazione `sliceIdentifierColumnName` in **SqlDWSink** per l'attività di copia nella pipeline. PolyBase garantisce che tutti i dati verranno aggiornati o che nessun dato verrà aggiornato in una singola esecuzione. Per ottenere la **ripetibilità**, è possibile usare `sqlWriterCleanupScript`.

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-using-polybase"></a>copia di staging tramite PolyBase

Quando i dati di origine non soddisfano i criteri presentati nella sezione precedente, è possibile abilitare la copia dei dati tramite un'istanza di Archiviazione BLOB di Azure di gestione temporanea provvisoria (non può essere Archiviazione Premium). In questo caso, Azure Data Factory esegue automaticamente trasformazioni sui dati in modo che soddisfino i requisiti di formato dei dati di PolyBase e quindi usa PolyBase per caricare dati in SQL Data Warehouse e infine pulisce i dati temporanei dall'archiviazione BLOB. Per informazioni dettagliate sul funzionamento generale della copia dei dati tramite un BLOB di Azure di staging, vedere la sezione [Copia di staging](copy-activity-performance.md#staged-copy) .

Per usare questa funzionalità, creare un [servizio collegato Archiviazione di Azure](connector-azure-blob-storage.md#linked-service-properties) che faccia riferimento all'account di archiviazione di Azure contenente l'archivio BLOB provvisorio e quindi specificare le proprietà `enableStaging` e `stagingSettings` per l'attività di copia come illustrato nel codice seguente:

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

## <a name="best-practices-when-using-polybase"></a>Procedure consigliate per l'uso di PolyBase

Le sezioni seguenti forniscono procedure consigliate aggiuntive a quelle descritte in [Procedure consigliate per Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Autorizzazione database obbligatoria

Per usare PolyBase, è necessario che l'utente che è solito caricare i dati in SQL Data Warehouse disponga dell'[autorizzazione "CONTROL"](https://msdn.microsoft.com/library/ms191291.aspx) nel database di destinazione. Un modo per ottenere questo risultato consiste nell'aggiungere tale utente come membro del ruolo "db_owner". Informazioni su come eseguire questa operazione sono disponibili nella [sezione](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization) seguente.

### <a name="row-size-and-data-type-limitation"></a>Limitazione alle dimensioni di righe e al tipo di dati

Le operazioni di caricamento di PolyBase sono limitate al caricamento di righe inferiori a **1 MB** che non possono essere caricate in VARCHR(MAX), NVARCHAR(MAX) o VARBINARY(MAX). Vedere [qui](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Se sono presenti dati di origine con righe di dimensioni superiori a 1 MB, è consigliabile suddividere verticalmente le tabelle di origine in tabelle più piccole, in cui le dimensioni massime delle righe di ogni tabella non superano il limite previsto. Le tabelle più piccole possono essere quindi caricate usando PolyBase e unite in Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Classe di risorse di SQL Data Warehouse

Per ottenere le migliori prestazioni possibili, considerare l'assegnazione di una classe di risorse più ampia all'utente che carica i dati in SQL Data Warehouse tramite PolyBase.

### <a name="tablename-in-azure-sql-data-warehouse"></a>tableName in Azure SQL Data Warehouse

La tabella seguente fornisce esempi relativi a come specificare la proprietà **tableName** nel set di dati JSON per diverse combinazioni di nomi di schema e di tabella.

| Schema di database | Nome tabella | Proprietà JSON tableName |
| --- | --- | --- |
| dbo |MyTable |MyTable o dbo.MyTable o [dbo].[MyTable] |
| dbo1 |MyTable |dbo1.MyTable o [dbo1].[MyTable] |
| dbo |My.Table |[My.Table] o [dbo].[My.Table] |
| dbo1 |My.Table |[dbo1].[My.Table] |

Se viene visualizzato l'errore seguente, potrebbe essersi verificato un problema con il valore specificato per la proprietà tableName. Per informazioni sul modo corretto di specificare i valori per la proprietà JSON tableName, vedere la relativa tabella.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Colonne con valori predefiniti

La funzionalità PolyBase in Data Factory accetta attualmente lo stesso numero di colonne disponibili nella tabella di destinazione. Se si ha una tabella con quattro colonne di cui una definita con un valore predefinito, ad esempio, i dati di input dovranno comunque contenere quattro colonne. Se si specifica un set di dati di input con 3 colonne, si verificherà un errore simile al messaggio seguente:

```
All columns of the table must be specified in the INSERT BULK statement.
```

Il valore NULL è una forma speciale di valore predefinito. Se la colonna ammette valori Null, i dati di input (nel BLOB) per tale colonna possono essere vuoti, ma non possono essere mancanti dal set di dati di input. PolyBase inserisce NULL per tali valori in Azure SQL Data Warehouse.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Mapping dei tipi di dati per Azure SQL Data Warehouse

Quando si copiano i dati/in Azure SQL Data Warehouse, vengono usati i mapping seguenti tra i tipi di dati di Azure SQL Data Warehouse e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo di dati di Azure SQL Data Warehouse | Tipo di dati provvisori di Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |boolean |
| char |String, Char[] |
| date |Datetime |
| DateTime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| immagine |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object * |
| text |String, Char[] |
| time |Intervallo di tempo |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).
