---
title: Risoluzione delle differenze di T-SQL durante la migrazione al database SQL di Azure | Microsoft Docs
description: Istruzioni Transact-SQL non completamente supportate nel Database SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/03/2018
ms.openlocfilehash: 0f64642d04504770415c0d2243ec77b44bde05f2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566295"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Risoluzione delle differenze di Transact-SQL durante la migrazione al database SQL

Durante la [migrazione del database](sql-database-single-database-migrate.md) da SQL Server ad Azure SQL Server, si potrebbe scoprire che il database richiede alcune operazioni di riprogettazione prima di poter eseguire la migrazione di SQL Server. Questo articolo offre indicazioni utili per eseguire la riprogettazione e capire i motivi che la rendono necessaria. Per rilevare le incompatibilità, usare [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Panoramica

La maggior parte delle funzionalità Transact-SQL usate dalle applicazioni è supportata in Microsoft SQL Server e nel database SQL di Azure. Ad esempio, i componenti SQL principali, come tipi di dati, operatori, funzioni di stringa, funzioni aritmetiche, logiche e del cursore funzionano allo stesso modo in SQL Server e nel database SQL. T-SQL presenta, tuttavia, alcune differenze negli elementi DDL (Data Definition Language) e DML (Data Manipulation Language), di conseguenza alcune istruzioni e query T-SQL sono supportate solo in parte (questo verrà descritto più avanti in questo articolo).

Inoltre, alcune funzionalità e sintassi non sono affatto supportate, perché il database SQL di Azure è progettato in modo da isolare le funzionalità dalle dipendenze nel database master e nel sistema operativo. Di conseguenza, la maggior parte delle attività a livello di server non è appropriata al database SQL. Le istruzioni T-SQL non sono disponibili se configurano opzioni a livello di server e componenti del sistema operativo o se specificano una configurazione del file system. Quando sono necessarie tali funzionalità, spesso è disponibile un'alternativa appropriata dal database SQL o da un'altra funzionalità o un altro servizio di Azure.

Ad esempio, la disponibilità elevata è integrata nel database SQL di Azure mediante una tecnologia simile a [Gruppi di disponibilità AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Le istruzioni T-SQL relative ai gruppi di disponibilità non sono supportate dal database SQL, così come le viste a gestione dinamica correlate ai gruppi di disponibilità AlwaysOn.

Per un elenco delle funzionalità supportate e non supportate dal database SQL, vedere  [Confronto delle funzionalità del database SQL di Azure](sql-database-features.md). L'elenco in questa pagina integra le linee guida e l'articolo sulle funzionalità e si concentra sulle istruzioni Transact-SQL.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Istruzioni di sintassi Transact-SQL con differenze parziali

Sono disponibili le istruzioni DDL (data definition language) di base, tuttavia alcune istruzioni DDL contengono estensioni relative al posizionamento del disco e a funzionalità non supportate.

- Le istruzioni CREATE e ALTER DATABASE offrono numerose opzioni. Le istruzioni includono le opzioni di posizionamento dei file, FILESTREAM e service broker che si applicano solo a SQL Server. Questo potrebbe non essere rilevante se si creano database prima di eseguire la migrazione, ma se si esegue la migrazione di codice T-SQL che crea i database è necessario confrontare [CREATE DATABASE (database SQL di Azure)](https://msdn.microsoft.com/library/dn268335.aspx) con la sintassi di SQL Server in [CREATE DATABASE (Transact-SQL di SQL Server)](https://msdn.microsoft.com/library/ms176061.aspx) per assicurarsi che tutte le opzioni in uso siano supportate. CREATE DATABASE per il database SQL di Azure ha anche opzioni di obiettivo di servizio e di scalabilità elastica che si applicano solo al database SQL.
- Le istruzioni CREATE e ALTER TABLE hanno opzioni FileTable che non possono essere usate nel database SQL perché FILESTREAM non è supportato.
- Le istruzioni di accesso CREATE e ALTER sono supportate, ma il database SQL non offre tutte le opzioni. Per aumentare la portabilità del database, il database SQL invita a usare utenti contenuti nel database anziché gli account di accesso tutte le volte che è possibile. Per altre informazioni, vedere [CREATE/ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx) e [Controllo e concessione dell'accesso al database](sql-database-manage-logins.md).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Sintassi di Transact-SQL non supportata nel database SQL di Azure

Oltre alle istruzioni Transact-SQL correlate alle funzioni non supportate descritte in  [Confronto delle funzionalità del database SQL di Azure](sql-database-features.md), non sono supportati le istruzioni e i gruppi di istruzioni seguenti. Di conseguenza, se il database di cui deve essere eseguita la migrazione usa le funzionalità seguenti, è necessario riprogettare T-SQL in modo da eliminare queste funzionalità e istruzioni T-SQL.

- Regole di confronto di oggetti di sistema
- Istruzioni di endpoint correlate alla connessione. Database SQL non supporta l'autenticazione di Windows, ma supporta l'analoga autenticazione di Azure Active Directory. Alcuni tipi di autenticazione richiedono l'ultima versione di SQL Server Management Studio. Per ulteriori informazioni, vedere [Connessione al database SQL oppure a SQL Data Warehouse con l'autenticazione di Azure Active Directory](sql-database-aad-authentication.md).
- Query tra database mediante nomi composti da tre o quattro parti. Le query tra database di sola lettura sono supportate mediante [query di database elastici](sql-database-elastic-query-overview.md).
- Concatenamento della proprietà tra database, impostazione `TRUSTWORTHY`
- `EXECUTE AS LOGIN` Usare invece 'EXECUTE AS USER'.
- La crittografia è supportata, ad eccezione della gestione delle chiavi estendibile
- Gestione degli eventi: eventi, notifiche degli eventi, notifiche di query
- Posizionamento dei file: Sintassi relativa al posizionamento dei file di database, dimensioni e file di database che vengono gestiti automaticamente da Microsoft Azure.
- Disponibilità elevata: Sintassi relativa a disponibilità elevata, gestita tramite l'account Microsoft Azure. Questa include la sintassi per backup, ripristino, AlwaysOn, mirroring del database, log shipping e modalità di ripristino.
- Agente di lettura log: Sintassi che si basa sull'agente di lettura log, non disponibile nel database SQL: replica push, Change Data Capture. Il database SQL può essere iscritto a un articolo di replica push.
- Funzioni: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Hardware: sintassi delle impostazioni del server relative all'hardware: memoria, thread di lavoro, affinità di CPU, flag di traccia. Usare i livelli di servizio e le dimensioni di calcolo.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE` e nomi in quattro parti
- .NET Framework: integrazione CLR con SQL Server
- Ricerca semantica
- Credenziali del server: usare invece le [credenziali con ambito database](https://msdn.microsoft.com/library/mt270260.aspx).
- Elementi a livello di server: ruoli del server, `sys.login_token`. `GRANT`, `REVOKE` e `DENY` delle autorizzazioni a livello di server non sono disponibili anche se alcune vengono sostituite da autorizzazioni a livello di database. Alcune viste a gestione dinamica a livello di server dispongono di una vista a gestione dinamica equivalente a livello di database.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- Opzioni `sp_configure` e `RECONFIGURE`. Con [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx)sono disponibili alcune opzioni.
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server Agent: Sintassi che si basa su SQL Server Agent o sul database MSDB: avvisi, operatori, server di gestione centrale. Usare invece gli script, ad esempio Azure PowerShell.
- Controllo di SQL Server: Usare in alternativa il controllo di Database SQL.
- Traccia SQL Server
- Flag di traccia: Alcuni elementi dei flag di traccia sono stati spostati in modalità di compatibilità.
- Debug di Transact-SQL
- Trigger: con ambito server o trigger di accesso
- Istruzione `USE`: per modificare il contesto del database in un database diverso è necessario creare una nuova connessione al nuovo database.

## <a name="full-transact-sql-reference"></a>Riferimento completo di Transact-SQL

Per altre informazioni sulla grammatica e l'uso di Transact-SQL e per alcuni esempi, vedere  [Guida di riferimento a Transact-SQL (Motore di database)](https://msdn.microsoft.com/library/bb510741.aspx)  nella documentazione online di SQL Server.

### <a name="about-the-applies-to-tags"></a>Informazioni sui tag "Si applica a"

Le informazioni di riferimento su Transact-SQL includono articoli correlati alle versioni di SQL Server dalla 2008 a quella attuale. Sotto il titolo dell'articolo è presente una barra di icone in cui sono elencate le quattro piattaforme SQL Server e la relativa applicabilità. Ad esempio, i gruppi di disponibilità sono stati introdotti in SQL Server 2012. L'articolo  [CREATE AVAILABILITY GROUP](https://msdn.microsoft.com/library/ff878399.aspx)  indica che l'istruzione si applica a  **SQL Server (a partire dalla versione 2012)** . L'istruzione non si applica a SQL Server 2008, SQL Server 2008 R2, Database SQL di Azure, Azure SQL Data Warehouse o Parallel Data Warehouse.

In alcuni casi, l'oggetto generale di un articolo può essere usato in un prodotto, ma esistono differenze minime tra i prodotti. Le differenze sono indicate in punti centrali nell'articolo come appropriato. In alcuni casi, l'oggetto generale di un articolo può essere usato in un prodotto, ma esistono differenze minime tra i prodotti. Le differenze sono indicate in punti centrali nell'articolo come appropriato. Ad esempio l'articolo CREATE TRIGGER è disponibile nel database SQL. Tuttavia, l'opzione **ALL SERVER** per i trigger a livello di server indica che i trigger a livello di server non possono essere usati nel database SQL. Usare i trigger a livello di database.

## <a name="next-steps"></a>Passaggi successivi

Per un elenco delle funzionalità supportate e non supportate dal database SQL, vedere  [Confronto delle funzionalità del database SQL di Azure](sql-database-features.md). L'elenco in questa pagina integra le linee guida e l'articolo sulle funzionalità e si concentra sulle istruzioni Transact-SQL.
