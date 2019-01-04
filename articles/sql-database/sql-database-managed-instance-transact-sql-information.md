---
title: Differenze T-SQL di Istanza gestita del database SQL di Azure | Microsoft Docs
description: Questo articolo illustra le differenze T-SQL tra Istanza gestita di database SQL di Azure e SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlrab, bonova
manager: craigg
ms.date: 12/03/2018
ms.openlocfilehash: 489eccf1b73e7f5df76a3ce681b4479893a9e0ac
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52843207"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Differenze T-SQL tra Istanza gestita del database SQL di Azure e SQL Server

Istanza gestita di database SQL di Azure assicura una compatibilità elevata con il motore di database di Microsoft SQL Server locale. La maggior parte delle funzionalità del motore di database di Microsoft SQL Server è supportata in Istanza gestita. Esistono tuttavia alcune differenze nella sintassi e nel comportamento, riepilogate e illustrate in questo articolo.

- [Differenze T-SQL e funzionalità non supportate](#Differences)
- [Funzionalità con un comportamento diverso in Istanza gestita](#Changes)
- [Limitazioni temporanee e problemi noti](#Issues)

## <a name="Differences"></a> Differenze T-SQL rispetto a SQL Server

Questa sezione riepiloga le principali differenze nella sintassi T-SQL e nel comportamento tra Istanza gestita e il motore di database di SQL Server locale, oltre alle funzionalità non supportate.

### <a name="always-on-availability"></a>Disponibilità Always On

La [disponibilità elevata](sql-database-high-availability.md) è incorporata in Istanza gestita e non può essere controllata dagli utenti. Le istruzioni seguenti non sono supportate:

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREATE AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- Clausola [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) dell'istruzione [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql)

### <a name="auditing"></a>Controllo

Le principali differenze tra il controllo SQL in Istanza gestita, il database SQL di Azure e SQL Server locale sono le seguenti:

- In Istanza gestita il controllo SQL lavora a livello del server e archivia i file di `.xel` nell'account di archiviazione BLOB di Azure.  
- Nel database SQL di Azure il controllo SQL lavora a livello del database.
- In SQL Server locale o nella macchina virtuale il controllo SQL lavora a livello del server, ma memorizza gli eventi nei log eventi del file system o di Windows.  
  
Il controllo XEvent in Istanza gestita supporta le destinazioni di archiviazione BLOB di Azure. I log di file e di Windows non sono supportati.

Le principali differenze nella sintassi `CREATE AUDIT` per il controllo nell'archivio BLOB di Azure sono le seguenti:

- È disponibile una nuova sintassi `TO URL` che consente di specificare l'URL del contenitore di archiviazione BLOB di Azure in cui verranno inseriti i file di `.xel`.
- La sintassi `TO FILE` non è supportata perché Istanza gestita non può accedere alle condivisioni file di Windows.

Per altre informazioni, vedere:  

- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)  
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Controllo](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="backup"></a>Backup

Istanza gestita ha backup automatici e consente agli utenti di creare backup `COPY_ONLY` di database completi. I backup differenziali, di log e dello snapshot dei file non sono supportati.

- Istanza gestita può eseguire il backup di un database solo in un account di archiviazione BLOB di Azure:
  - È supportato solo `BACKUP TO URL`
  - I dispositivi di backup, `FILE` e `TAPE` non sono supportati  
- La maggior parte delle opzioni `WITH` generali è supportata
  - `COPY_ONLY` è obbligatorio
  - `FILE_SNAPSHOT` non è supportato
  - Le opzioni nastro `REWIND`, `NOREWIND`, `UNLOAD` e `NOUNLOAD` non sono supportate
  - Le opzioni specifiche dei log `NORECOVERY`, `STANDBY` e `NO_TRUNCATE` non sono supportate

 Limitazioni:  

- Istanza gestita può eseguire il backup di un database su un supporto di backup con un massimo di 32 set di stripe, sufficienti per database fino a 4 TB se si usa la compressione del backup.
- La dimensione massima dei set di stripe di backup è 195 GB (dimensioni massime del BLOB). Aumentare il numero di set di stripe nel comando backup per ridurre le dimensioni dei singoli set di stripe e restare nel limite consentito.

> [!TIP]
> Per evitare questa limitazione in locale, eseguire il backup su `DISK` anziché su `URL`, caricare il file di backup nel BLOB e quindi eseguire il ripristino. Il ripristino supporta file di dimensioni maggiori perché usa un tipo di BLOB diverso.  

Per informazioni sui backup con T-SQL, vedere [BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

### <a name="buffer-pool-extension"></a>Estensione del pool di buffer

- L'[estensione del pool di buffer](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) non è supportata.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` non è supportato. Vedere [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="bulk-insert--openrowset"></a>Inserimento bulk/openrowset

Istanza gestita non può accedere a condivisioni file e cartelle di Windows e pertanto i file devono essere importati dall'archivio BLOB di Azure:

- `DATASOURCE` è obbligatorio nel comando `BULK INSERT` durante l'importazione di file dall'archivio BLOB di Azure. Vedere [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` è obbligatorio nella funzione `OPENROWSET` quando si legge il contenuto di un file dall'archivio BLOB di Azure. Vedere [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).

### <a name="certificates"></a>Certificati

Istanza gestita non può accedere a condivisioni file e cartelle di Windows, quindi vengono applicati i vincoli seguenti:

- `CREATE FROM`/`BACKUP TO` file non è supportato per i certificati
- `CREATE`/`BACKUP` certificato da `FILE`/`ASSEMBLY` non è supportato. I file di chiavi private non possono essere usati.  

Vedere [CREATE CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) e [BACKUP CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql).  
  
**Soluzione alternativa**: creare uno script per il certificato/chiave privata, archiviare come file con estensione sql e creare il certificato dal file binario:

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="clr"></a>CLR

Istanza gestita non può accedere a condivisioni file e cartelle di Windows, quindi vengono applicati i vincoli seguenti:

- È supportato solo `CREATE ASSEMBLY FROM BINARY`. Vedere [CREATE ASSEMBLY FROM BINARY](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).  
- `CREATE ASSEMBLY FROM FILE` non è supportato. Vedere [CREATE ASSEMBLY FROM FILE](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` non può fare riferimento a file. Vedere [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="compatibility-levels"></a>Livelli di compatibilità

- I livelli di compatibilità supportati sono: 100, 110, 120, 130, 140  
- I livelli di compatibilità inferiori a 100 non sono supportati.
- Il livello di compatibilità predefinito per i nuovi database è 140. Per i database ripristinati, il livello di compatibilità rimane invariato se era uguale o superiore a 100.

Vedere [Livello di compatibilità ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="credential"></a>Credenziali

Sono supportati solo l'insieme di credenziali delle chiavi di Azure e le identità `SHARED ACCESS SIGNATURE`. Gli utenti di Windows non sono supportati.

Vedere [CREATE CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) e [ALTER CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Provider del servizio di crittografia

Istanza gestita non può accedere ai file, quindi non è possibile creare provider del servizio di crittografia:

- `CREATE CRYPTOGRAPHIC PROVIDER` non è supportato. Vedere [CREATE CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` non è supportato. Vedere [ALTER CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="collation"></a>Collation

Le regole di confronto di istanza predefinita sono `SQL_Latin1_General_CP1_CI_AS` e possono essere specificate come un parametro di creazione. Vedere [Regole di confronto](https://docs.microsoft.com/sql/t-sql/statements/collations).

### <a name="database-options"></a>Opzioni di database

- Non sono supportati più file di log.
- Gli oggetti in memoria non sono supportati nel livello di servizio per utilizzo generico.  
- Esiste un limite di 280 file per istanza, che quindi implica un massimo di 280 file per database. I file di dati e i file di log vengono conteggiati per il limite.  
- I database non possono contenere filegroup che contengono a loro volta dati FileStream.  Se il file con estensione bak contiene dati `FILESTREAM`, il ripristino non riesce.  
- Ogni file viene inserito in Archiviazione Premium di Azure. L'I/O e la velocità effettiva per file dipendono dalle dimensioni di ogni singolo file, esattamente come per i dischi di Archiviazione Premium di Azure. Vedere [Prestazioni di Archiviazione Premium](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes).  

#### <a name="create-database-statement"></a>Istruzione CREATE DATABASE

Le limitazioni di `CREATE DATABASE` sono le seguenti:

- Non possono essere definiti file e filegroup.  
- L'opzione `CONTAINMENT` non è supportata.  
- Le opzioni `WITH` non sono supportate.  
   > [!TIP]
   > Come soluzione alternativa, usare `ALTER DATABASE` dopo `CREATE DATABASE` per impostare le opzioni di database per l'aggiunta di file o l'impostazione del contenimento.  

- L'opzione `FOR ATTACH` non è supportata
- L'opzione `AS SNAPSHOT OF` non è supportata

Per altre informazioni, vedere [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Istruzione ALTER DATABASE

Alcune proprietà di file non possono essere impostate o modificate:

- Il percorso file non può essere specificato nell'istruzione T-SQL `ALTER DATABASE ADD FILE (FILENAME='path')`. Rimuovere `FILENAME` dallo script in quanto Istanza gestita inserisce i file automaticamente.  
- Il nome file non può essere modificato mediante l'istruzione `ALTER DATABASE`.

Le opzioni seguenti sono attive per impostazione predefinita e non possono essere modificate:

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

Le opzioni seguenti non possono essere modificate:

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE`  
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

La modifica del nome non è supportata.

Per altre informazioni, vedere [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="database-mirroring"></a>Mirroring del database

Il mirroring del database non è supportato.

- Le opzioni `ALTER DATABASE SET PARTNER` e `SET WITNESS` non sono supportate.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` non è supportato.

Per altre informazioni, vedere [ALTER DATABASE SET PARTNER e SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) e [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="dbcc"></a>DBCC

Le istruzioni DBCC non documentate abilitate in SQL Server non sono supportate in Istanza gestita.

- I `Trace Flags` non sono supportati. Vedere [Flag di traccia](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- `DBCC TRACEOFF` non è supportato. Vedere [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql).
- `DBCC TRACEON` non è supportato. Vedere [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql).

### <a name="distributed-transactions"></a>Transazioni distribuite

Né MSDTC, né le [transazioni elastiche](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-transactions-overview) sono attualmente supportate in Istanza gestita.

### <a name="extended-events"></a>Eventi estesi

Alcune destinazioni specifiche di Windows per XEvents non sono supportate:

- `etw_classic_sync target` non è supportato. Archiviare i file `.xel` nell'archivio BLOB di Azure. Vedere [Destinazione etw_classic_sync_target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etwclassicsynctarget-target).
- `event_file target` non è supportato. Archiviare i file `.xel` nell'archivio BLOB di Azure. Vedere [Destinazione event_file](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#eventfile-target).

### <a name="external-libraries"></a>Librerie esterne

Le librerie esterne In-database R e Python non sono ancora supportate. Vedere [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FileStream e FileTable

- I dati FileStream non sono supportati.
- I database non possono contenere filegroup con dati `FILESTREAM`
- `FILETABLE` non è supportato
- Le tabelle non possono contenere tipi `FILESTREAM`
- Le funzioni seguenti non sono supportate:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Per altre informazioni, vedere [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) e [FileTable](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Ricerca semantica full-text

La [ricerca semantica](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) non è supportata.

### <a name="linked-servers"></a>Server collegati

I server collegati in Istanza gestita supportano un numero limitato di destinazioni:

- Destinazioni supportate: SQL Server e Database SQL
- Destinazioni non supportate: file, Analysis Services e altri sistemi di gestione di database relazionali (RDBMS).

Operazioni

- Le transazioni di scrittura tra istanze non sono supportate.
- `sp_dropserver` è supportato per l'eliminazione di un server collegato. Vedere [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- La funzione `OPENROWSET` può essere usata per eseguire query solo su istanze di SQL Server (gestite, locali o in macchine virtuali). Vedere [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- La funzione `OPENDATASOURCE` può essere usata per eseguire query solo su istanze di SQL Server (gestite, locali o in macchine virtuali). Sono supportati come provider solo i valori `SQLNCLI`, `SQLNCLI11` e `SQLOLEDB`. Ad esempio: `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Vedere [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).

### <a name="logins--users"></a>Account di accesso/utenti

- Gli account di accesso SQL creati `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY` e `FROM SID` sono supportati. Vedere [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- Sono supportati gli accessi ad Azure Active Directory (AAD) creati con la sintassi [CREA ACCESSO](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) o con la sintassi [CREA UTENTE](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) (**anteprima pubblica**).
- Gli account di accesso Windows creati con la sintassi `CREATE LOGIN ... FROM WINDOWS` non sono supportati. Usare gli accessi e gli utenti di Azure Active Directory.
- L'utente di Azure Active Directory (Azure AD) che ha creato l'istanza ha [privilegi amministrativi illimitati](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins#unrestricted-administrative-accounts).
- Gli utenti non amministratori di Azure Active Directory (Azure AD) a livello di database possono essere creati con la sintassi `CREATE USER ... FROM EXTERNAL PROVIDER`. Vedere [CREATE USER ... FROM EXTERNAL PROVIDER](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins#non-administrator-users)

### <a name="polybase"></a>PolyBase

Le tabelle esterne che fanno riferimento ai file in HDFS o nell'archivio BLOB di Azure non sono supportate. Per informazioni su Polybase, vedere [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replica

La replica è disponibile in anteprima pubblica in Istanza gestita. Per informazioni sulla replica, vedere [Replica di SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance).

### <a name="restore-statement"></a>Istruzione RESTORE

- Sintassi supportata
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Sintassi non supportata
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Sorgente  
  - `FROM URL` (archivio BLOB di Azure) è l'unica opzione supportata.
  - `FROM DISK`/`TAPE`/dispositivo di backup non è supportato.
  - I set di backup non sono supportati.
- Le opzioni `WITH` (`DIFFERENTIAL`, `STATS` ecc.) non sono supportate
- `ASYNC RESTORE` - il ripristino continua anche se la connessione del client si interrompe. Se la connessione viene interrotta, è possibile controllare nella vista `sys.dm_operation_status` lo stato di un'operazione di ripristino (oltre che di CREATE e DROP DATABASE). Vedere [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).  

Le opzioni di database seguenti sono impostate/sottoposte a override e non possono essere modificate in seguito:  

- `NEW_BROKER` (se il broker non è abilitato nel file con estensione bak)  
- `ENABLE_BROKER` (se il broker non è abilitato nel file con estensione bak)  
- `AUTO_CLOSE=OFF` (se un database nel file con estensione bak ha l'opzione `AUTO_CLOSE=ON`)  
- `RECOVERY FULL` (se un database nel file con estensione bak è in modalità di ripristino `SIMPLE` o `BULK_LOGGED`)
- Il filegroup ottimizzato per la memoria viene aggiunto e denominato XTP se non era incluso nel file con estensione bak di origine  
- Qualsiasi filegroup ottimizzato per la memoria esistente viene rinominato in XTP  
- Le opzioni `SINGLE_USER` e `RESTRICTED_USER` vengono convertite in `MULTI_USER`

 Limitazioni:  

- I file `.BAK` che contengono più set di backup non possono essere ripristinati.
- I file `.BAK` che contengono più file di log non possono essere ripristinati.
- Se il file con estensione bak contiene dati `FILESTREAM`, il ripristino non riesce.
- I backup che contengono database con oggetti in memoria attivi non possono attualmente essere ripristinati.  
- I backup che contengono database in cui esistevano oggetti in memoria non possono attualmente essere ripristinati.
- I backup che contengono database in modalità di sola lettura non possono attualmente essere ripristinati. Questa limitazione verrà rimossa a breve.

Per informazioni sulle istruzioni Restore, vedere [Istruzioni RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

### <a name="service-broker"></a>Broker di servizio

Il broker di servizio tra istanze non è supportato:

- `sys.routes` - prerequisito: selezionare un indirizzo da sys.routes. L'indirizzo deve essere LOCALE in ogni route. Vedere [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE` - non è possibile usare `CREATE ROUTE` con `ADDRESS` diverso da `LOCAL`. Vedere [CREATE ROUTE](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE` - non è possibile eseguire un'istruzione `ALTER ROUTE` con un `ADDRESS` diverso da `LOCAL`. Vedere [ALTER ROUTE](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql).  

### <a name="service-key-and-service-master-key"></a>Chiave del servizio e chiave master del servizio

- Il [backup della chiave master](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) non è supportato (gestito dal servizio del database SQL)
- Il [ripristino della chiave master](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) non è supportato (gestito dal servizio del database SQL)
- Il [backup della chiave master del servizio](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) non è supportato (gestito dal servizio del database SQL)
- Il [ripristino della chiave master del servizio](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) non è supportato (gestito dal servizio del database SQL)

### <a name="stored-procedures-functions-triggers"></a>Stored procedure, funzioni, trigger

- `NATIVE_COMPILATION` non è attualmente supportato.
- Le opzioni [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) seguenti non sono supportate:
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `max text repl size`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` non è supportato. Vedere [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` non è supportato. Vedere [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- Le `Extended stored procedures` non sono supportate, incluse `sp_addextendedproc`  e `sp_dropextendedproc`. Vedere [Stored procedure estese](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db` e `sp_detach_db` non sono supportati. Vedere [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) e [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).
- `sp_renamedb` non è supportato. Vedere [sp_renamedb](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-renamedb-transact-sql).

### <a name="sql-server-agent"></a>Agente SQL Server

- Le impostazioni dell'agente SQL sono di sola lettura. La routine `sp_set_agent_properties` non è supportata in Istanza gestita.  
- Processi
  - I passaggi dei processi T-SQL sono supportati.
  - I processi di replica seguenti sono supportati:
    - Lettore di log delle transazioni.  
    - Snapshot.
    - Database di distribuzione
  - I passaggi dei processi SSIS sono supportati.
  - Altri tipi di passaggi dei processi non sono attualmente supportati, tra cui:
    - Il passaggio del processo di replica di tipo merge non è supportato.  
    - La lettura coda non è supportata.  
    - La shell dei comandi non è ancora supportata.
  - Istanza gestita non può accedere a risorse esterne (ad esempio a condivisioni di rete tramite robocopy).  
  - PowerShell non è ancora supportato.
  - Analysis Services non è supportato.
- Le notifiche sono supportate in modo parziale.
- La notifica tramite posta elettronica è supportata e richiede la configurazione di un profilo di posta elettronica database. Può esistere un solo profilo di posta elettronica database, che deve essere denominato `AzureManagedInstance_dbmail_profile` nell'anteprima pubblica (limitazione temporanea).  
  - Il cercapersone non è supportato.  
  - NetSend non è supportato.
  - Gli avvisi non sono ancora supportati.
  - I proxy non sono supportati.  
- EventLog non è supportato.

Le funzionalità seguenti non sono attualmente supportate ma saranno abilitate in futuro:

- Proxy
- Pianificazione di processi su una CPU inattiva
- Abilitazione/disabilitazione dell'agente
- Avvisi

Per informazioni su SQL Server Agent, vedere [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabelle

Le tabelle seguenti non sono supportate:

- `FILESTREAM`
- `FILETABLE`
- `EXTERNAL TABLE`
- `MEMORY_OPTIMIZED`  

Per informazioni sulla creazione e la modifica di tabelle, vedere [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) e [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="Changes"></a> Modifiche nel comportamento

Le variabili, funzioni e viste seguenti restituiscono risultati diversi:

- `SERVERPROPERTY('EngineEdition')` restituisce il valore 8. Questa proprietà identifica Istanza gestita in maniera univoca. Vedere [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` restituisce NULL, poiché il concetto di istanza valido per SQL Server non si applica a Istanza gestita. Vedere [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` restituisce il nome DNS completo "collegabile", ad esempio my-managed-instance.wcus17662feb9ce98.database.windows.net. Vedere [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql).  
- `SYS.SERVERS` restituisce il nome DNS completo "collegabile", ad esempio `myinstance.domain.database.windows.net` per le proprietà 'name' e 'data_source'. Vedere [SYS.SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` restituisce NULL, poiché il concetto di servizio valido per SQL Server non si applica a Istanza gestita. Vedere [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` è supportato. Restituisce NULL se l'account di accesso AAD non è presente in sys.syslogins. Vedere [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql).  
- `SUSER_SID` non è supportato. Restituisce dati errati (problema noto temporaneo). Vedere [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql).
- `GETDATE()` e altre funzioni di data/ora predefinite restituiscono sempre la data e ora nel fuso orario UTC. Vedere [GETDATE](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql).

## <a name="Issues"></a> Problemi noti e limitazioni

### <a name="tempdb-size"></a>Dimensioni di TEMPDB

`tempdb` è suddiviso in 12 file, ognuno con dimensioni massime di 14 GB. Le dimensioni massime per file non possono essere modificate e non è possibile aggiungere nuovi file a `tempdb`. Questa limitazione verrà rimossa a breve. Alcune query potrebbero restituire un errore se necessitano di più di 168 GB in `tempdb`.

### <a name="exceeding-storage-space-with-small-database-files"></a>Superamento dello spazio di archiviazione con file di database di piccole dimensioni

Ogni istanza gestita dispone di una risorsa di archiviazione da 35 TB riservata allo spazio su disco Premium di Azure e ogni file di database si trova su un disco fisico separato. I dischi possono essere da 128 GB, 256 GB, 512 GB, 1 TB o 4 TB. Lo spazio inutilizzato su disco non viene conteggiato, ma la somma delle dimensioni dei dischi Premium di Azure non può superare 35 TB. In alcuni casi, un'istanza gestita che non necessita di 8 TB in totale può superare il limite di Azure di 35 TB per le dimensioni delle risorse di archiviazione, a causa della frammentazione interna.

Per esempio, un'istanza gestita può avere un file da 1,2 TB all'interno di un disco da 4 TB e 248 file (da 1 GB ciascuno) all'interno di dischi da 128 GB separati. Esempio:

- la dimensione totale della risorsa di archiviazione sul disco allocato è 1 x 4 TB + 248 x 128 GB = 35 TB.
- Lo spazio totale riservato per i database nell'istanza è 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Ciò dimostra che, in determinate circostanze, a causa di una distribuzione specifica dei file, un'istanza gestita può raggiungere i 35 TB riservati ad Azure Premium Disk allegato anche quando non previsto.

In questo esempio, i database esistenti continueranno a funzionare e potranno crescere senza alcun problema fino a quando non vengono aggiunti nuovi file. È possibile tuttavia che non vengano creati o ripristinati nuovi database a causa dello spazio insufficiente per le nuove unità disco, anche se le dimensioni totali di tutti i database non raggiungono il limite di dimensioni dell'istanza. L'errore restituito in questo caso potrebbe non essere chiaro.

### <a name="incorrect-configuration-of-sas-key-during-database-restore"></a>Configurazione non corretta della chiave di firma di accesso condiviso durante il ripristino del database

L'istruzione `RESTORE DATABASE` che legge il file con estensione bak potrebbe cercare continuamente di leggere il file e restituire un errore dopo un lungo periodo di tempo se la firma di accesso condiviso in `CREDENTIAL` non è corretta. Eseguire RESTORE HEADERONLY prima di ripristinare un database per avere la certezza che la chiave di firma di accesso condiviso sia corretta.
Assicurarsi di rimuovere il carattere `?` iniziale dalla chiave SAS generata con il portale di Azure.

### <a name="tooling"></a>Strumenti

SQL Server Management Studio (SSMS) ed SQL Server Data Tools (SSDT) potrebbero riscontrare problemi durante l'accesso a Istanza gestita.

- L'uso degli accessi e degli utenti di Azure AD (**anteprima pubblica**) con SSDT non è attualmente supportato.
- Gli script per gli accessi e gli utenti di Azure AD (**anteprima pubblica**) non sono supportati in SSMS.

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>Nomi di database errati in alcune viste, log e messaggi

In numerose viste di sistema, contatori delle prestazioni, messaggi di errore, XEvent e voci del log degli errori sono visualizzati gli identificatori GUID dei database anziché i nomi effettivi. Non fare affidamento su questi identificatori GUID, in quanto potrebbero essere sostituiti dai nomi effettivi dei database in futuro.

### <a name="database-mail-profile"></a>Profilo di posta elettronica database

Può esistere un solo profilo di posta elettronica database, che deve essere denominato `AzureManagedInstance_dbmail_profile`.

### <a name="error-logs-are-not-persisted"></a>I log degli errori non sono persistenti

I log degli errori che sono disponibili nell'istanza gestita non sono persistenti e le relative dimensioni non sono incluse nel limite di archiviazione massimo. I log degli errori potrebbero essere cancellati automaticamente in caso di failover.

### <a name="error-logs-are-verbose"></a>I log degli errori sono di tipo dettagliato

Istanza gestita inserisce informazioni dettagliate nei log degli errori e molte di esse non sono pertinenti. La quantità di informazioni nei log degli errori verrà ridotta in futuro.

**Soluzione alternativa**: usare una procedura personalizzata per la lettura dei log degli errori che filtrano alcune voci non pertinenti. Per informazioni dettagliate, vedere [Istanza gestita di database SQL di Azure - sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/).

### <a name="transaction-scope-on-two-databases-within-the-same-instance-is-not-supported"></a>L'ambito della transazione in due database nella stessa istanza non è supportato

La classe `TransactionScope` in .Net non funziona se vengono inviate due query ai due database nella stessa istanza all'interno del medesimo ambito della transazione:

```C#
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

Sebbene questo codice funzioni con i dati nella stessa istanza, è necessario MSDTC.

**Soluzione alternativa**: usare [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) per usare un altro database nel contesto della connessione invece di usare due connessioni.

### <a name="clr-modules-and-linked-servers-sometime-cannot-reference-local-ip-address"></a>I moduli CLR e i server collegati talvolta non riescono a fare riferimento all'indirizzo IP locale

I moduli CLR inseriti in Istanza gestita e i server collegati o le query distribuite che fanno riferimento all'istanza corrente talvolta non riescono a risolvere l'indirizzo IP dell'istanza locale. Questo errore è un problema temporaneo.

**Soluzione alternativa**: usare connessioni di contesto nel modulo CLR, se possibile.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni dettagliate in proposito, vedere [Informazioni su Istanza gestita](sql-database-managed-instance.md).
- Per un elenco di confronto delle funzionalità, vedere [Confronto tra le funzionalità: database SQL di Azure e SQL Server](sql-database-features.md).
- Per una guida introduttiva che illustra come creare una nuova istanza gestita, vedere [Creare un'istanza gestita](sql-database-managed-instance-get-started.md).
