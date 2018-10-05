---
title: Autenticazione di Azure Active Directory - Azure SQL (Panoramica) | Documentazione Microsoft
description: Informazioni su come usare Azure Active Directory per l'autenticazione al database SQL, a Istanza gestita e a SQL Data Warehouse
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 06/13/2018
ms.openlocfilehash: c11ba5fd88beeeb9b895abb1ee258c3109c40807
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47064068"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql-database-managed-instance-or-sql-data-warehouse"></a>Usare Azure Active Directory per l'autenticazione al database SQL, a Istanza gestita e a SQL Data Warehouse
L'autenticazione di Azure Active Directory è un meccanismo di connessione al [database SQL](sql-database-technical-overview.md) di Azure e a [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) tramite le identità di Azure Active Directory (Azure AD). 

> [!NOTE]
> Questo argomento è applicabile al server SQL di Azure e ai database SQL e di SQL Data Warehouse creati nel server SQL di Azure. Per semplicità, "database SQL" viene usato per fare riferimento sia al database SQL che al database di SQL Data Warehouse.

Con l'autenticazione di Azure AD è possibile gestire centralmente le identità degli utenti del database e altri servizi Microsoft. La gestione centrale degli ID consente di gestire gli utenti del database da un unico punto e semplifica la gestione delle autorizzazioni. Ecco alcuni vantaggi:

* Offre un'alternativa all'autenticazione di SQL Server.
* Contribuisce ad arrestare la proliferazione delle identità utente nei server di database.
* Consente la rotazione delle password in un'unica posizione.
* I clienti possono gestire le autorizzazioni del database usando gruppi (Azure AD) esterni.
* Può eliminare l'archiviazione delle password abilitando l'autenticazione integrata di Windows e altre forme di autenticazione supportate da Azure Active Directory.
* L'autenticazione di Azure SD usa gli utenti di un database indipendente per autenticare le identità a livello di database.
* Azure AD supporta l'autenticazione basata su token per le applicazioni che si connettono a database SQL.
* L'autenticazione di Azure AD supporta la federazione dei domini di AD FS o l'autenticazione utente/password nativa per un'istanza locale di Azure Active Directory senza la sincronizzazione del dominio.  
* Azure AD supporta le connessioni da SQL Server Management Studio che utilizzano l'autenticazione universale di Active Directory, che include l'MFA (Multi-Factor Authentication).  L'MFA include funzionalità avanzate di autenticazione con una serie di semplici opzioni di verifica, tra cui: chiamata telefonica, SMS, smart card con pin o notifica tramite app per dispositivi mobili. Per altre informazioni [Supporto di SQL Server Management Studio (SSMS) per l'autenticazione MFA di Azure AD con il database SQL e SQL Data Warehouse](sql-database-ssms-mfa-authentication.md).  

>  [!NOTE]  
>  La connessione a SQL Server in esecuzione su una macchina virtuale di Azure non è supportata con un account Azure Active Directory. Usare un account Active Directory di dominio.  

I passaggi di configurazione includono le procedure seguenti per configurare e usare l'autenticazione di Azure Active Directory.

1. Creare e popolare un'istanza di Azure AD.
2. Facoltativo: associare o modificare l'istanza di Active Directory attualmente associata alla sottoscrizione di Azure.
3. Creare un amministratore di Azure Active Directory per il server di database SQL di Azure, Istanza gestita o [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
4. Configurare i computer client.
5. Creare gli utenti di database indipendente nel database di cui è stato eseguito il mapping alle identità di Azure AD.
6. Connettersi al database usando le identità di Azure AD.

> [!NOTE]
> Per informazioni su come creare e popolare Azure AD e quindi configurare Azure AD con il database SQL di Azure, con Istanza gestita e con SQL Data Warehouse, vedere [Configurare Azure AD con il database SQL di Azure](sql-database-aad-authentication-configure.md).
>

## <a name="trust-architecture"></a>Architettura di attendibilità
Il diagramma generale seguente riepiloga l'architettura della soluzione relativa all'uso dell'autenticazione di Azure AD con il database SQL di Azure SQL. Gli stessi concetti sono validi per SQL Data Warehouse. Per supportare la password utente nativa di Azure AD, viene considerata solo la parte cloud e Azure AD o il database SQL di Azure. Per supportare l'autenticazione federata o l'autenticazione utente/password per le credenziali di Windows, è necessaria la comunicazione con il blocco AD FS. Le frecce indicano i percorsi di comunicazione.

![diagramma di autenticazione di aad][1]

Il diagramma seguente indica le relazioni federative, di trust e di hosting che consentono a un client di connettersi a un database inviando un token, che viene autenticato da Azure Active Directory e considerato attendibile dal database. Il cliente 1 può rappresentare un'istanza di Azure Active Directory con utenti nativi o con utenti federati. Il cliente 1 può rappresentare una Azure Active Directory con utenti nativi o una Azure AD con utenti federati. In questo esempio provengono da un'istanza federata di Azure Active Directory con AD FS sincronizzato con Azure Active Directory. È importante comprendere che l'accesso a un database con l'autenticazione di Azure AD richiede che la sottoscrizione di hosting sia associata ad Azure AD. La stessa sottoscrizione deve essere utilizzata per creare il Server SQL che ospita l'Azure SQL Data Warehouse o il database SQL di Azure.

![relazione di sottoscrizione][2]

## <a name="administrator-structure"></a>Struttura dell'account amministratore
Quando si usa l'autenticazione di Azure AD, sono disponibili due account amministratore per il server di database SQL e Istanza gestita, l'amministratore di SQL Server originale e l'amministratore di Azure AD. Gli stessi concetti sono validi per SQL Data Warehouse. Solo l'amministratore basato su un account Azure AD può creare il primo utente di database indipendente di Azure AD in un database utente. L'account di accesso dell'amministratore di Azure AD può essere un utente o un gruppo di Azure AD. Quando l'amministratore è un account di gruppo, può essere usato da qualsiasi membro del gruppo, abilitando quindi più amministratori di Azure AD per l'istanza di SQL Server. L'uso dell'account di gruppo come amministratore migliora la gestibilità, perché consente di aggiungere e rimuovere a livello centrale i membri del gruppo in Azure AD senza apportare modifiche a utenti o autorizzazioni nel database SQL. È possibile configurare un solo amministratore di Azure AD (utente o gruppo) alla volta.

![struttura di amministrazione][3]

## <a name="permissions"></a>Autorizzazioni
Per creare nuovi utenti, è necessario avere l'autorizzazione `ALTER ANY USER` sul database. L'autorizzazione `ALTER ANY USER` può esser concessa a qualsiasi utente di database. L'autorizzazione `ALTER ANY USER` è assegnata anche agli account amministratore del server, agli utenti di database con l'autorizzazione `CONTROL ON DATABASE` o `ALTER ON DATABASE` per tale database e ai membri del ruolo del database `db_owner`.

Per creare un utente di database indipendente nel database SQL di Azure, in Istanza gestita o in SQL Data Warehouse, è necessario connettersi al database o all'istanza con un'identità di Azure AD. Per creare il primo utente di database indipendente, è necessario connettersi al database tramite un amministratore di Azure AD (che corrisponde al proprietario del database). Questa procedura è illustrata in [Configurare e gestire l'autenticazione di Azure Active Directory con il database SQL oppure con SQL Data Warehouse](sql-database-aad-authentication-configure.md). L'autenticazione di Azure AD è possibile unicamente se l'amministratore di Azure AD è stato creato per il server di database SQL di Azure o SQL Data Warehouse. Se l'amministratore di Azure Active Directory è stato rimosso dal server, gli utenti di Azure Active Directory esistenti creati in precedenza all'interno di SQL Server non possono più connettersi al database con le credenziali di Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Funzionalità e limitazioni di Azure AD
È possibile eseguire il provisioning dei membri di Azure AD seguenti nel server di Azure SQL o in SQL Data Warehouse:

- Membri nativi: un membro creato in Azure AD nel dominio gestito o in un dominio del cliente. Per altre informazioni, vedere [Aggiungere un nome di dominio personalizzato ad Azure AD](../active-directory/active-directory-domains-add-azure-portal.md).
- Membri del dominio federato: un membro creato in Azure AD con un dominio federato. Per altre informazioni, vedere il post di blog relativo al [nuovo supporto per la federazione in Microsoft Azure con Active Directory di Windows Server](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/).
- Membri importati da altre istanze di Azure AD che sono membri nativi o del dominio federato.
- Gruppi di Active Directory creati come gruppi di sicurezza.

Limitazioni di Azure AD correlate a Istanza gestita:
- Solo l'amministratore di Azure AD può creare database. Gli utenti di Azure AD hanno un ambito limitato a un singolo database e non dispongono di questa autorizzazione.
- Proprietà del database:
  - L'entità di sicurezza di Azure AD non può modificare il proprietario del database (ALTER AUTHORIZATION ON DATABASE) né può essere impostata come proprietario.
  - Per i database creati dall'amministratore di Azure AD, il proprietario non è impostato. Il campo owner_sid in sys.sysdatabases è 0x1.
- Non è possibile gestire SQL Agent se si è eseguito l'accesso usando le entità di sicurezza di Azure AD. 
- L'amministratore di Azure AD non può essere rappresentato usando EXECUTE AS.
- La connessione DAC non è supportata con le entità di sicurezza di Azure AD. 

Queste funzioni di sistema restituiscono valori NULL quando vengono eseguite nell'ambito di entità di sicurezza di Azure AD:
- `SUSER_ID()`
- `SUSER_NAME(<admin ID>)`
- `SUSER_SNAME(<admin SID>)`
- `SUSER_ID(<admin name>)`
- `SUSER_SID(<admin name>)`

## <a name="connecting-using-azure-ad-identities"></a>Connettersi usando le identità di Azure AD

L'autenticazione di Azure Active Directory supporta i metodi seguenti per la connessione a un database con le identità di Azure AD:

* Con l'autenticazione integrata di Windows
* Con un nome di entità e una password di Azure AD
* Con l'autenticazione del token dell'applicazione

### <a name="additional-considerations"></a>Ulteriori considerazioni

* Per migliorare la gestibilità, è consigliabile effettuare il provisioning di un gruppo di Azure AD dedicato come amministratore.   
* È possibile configurare un solo amministratore di Azure AD (utente o gruppo) per un server di database SQL di Azure, per Istanza gestita o per Azure SQL Data Warehouse in qualsiasi momento.   
* Un solo amministratore di Azure AD per SQL Server può connettersi inizialmente al server di database SQL di Azure, a Istanza gestita o ad Azure SQL Data Warehouse usando un account Azure Active Directory. L'amministratore di Active Directory può configurare gli utenti del database di Azure AD successivi.   
* È consigliabile impostare il timeout di connessione su 30 secondi.   
* SQL Server 2016 Management Studio e SQL Server Data Tools per Visual Studio 2015, versione 14.0.60311.1 di aprile 2016 o successiva, supportano l'autenticazione di Azure Active Directory. L'autenticazione di Azure AD è supportata dal **provider di dati .NET Framework per server SQL** a partire da .NET Framework versione 4.6. Di conseguenza, le versioni più recenti di questi strumenti e applicazioni del livello dati (DAC e BACPAC) possono usare l'autenticazione di Azure AD.   
* [ODBC versione 13.1](https://www.microsoft.com/download/details.aspx?id=53339) supporta l'autenticazione di Azure Active Directory, tuttavia `bcp.exe` non può connettersi mediante l'autenticazione di Azure Active Directory perché usa un provider ODBC meno recente.   
* `sqlcmd` supporta l'autenticazione di Azure Active Directory iniziando con la versione 13.1 disponibile in [Download Center](http://go.microsoft.com/fwlink/?LinkID=825643).   
* SQL Server Data Tools per Visual Studio 2015 richiede almeno la versione 14.0.60311.1 di aprile 2016. Gli utenti di Azure AD non sono attualmente visualizzati in Esplora oggetti di SSDT. Come soluzione alternativa è possibile visualizzare gli utenti in [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
* [Microsoft JDBC Driver 6.0 per server SQL](https://www.microsoft.com/download/details.aspx?id=11774) supporta l'autenticazione di Azure AD. Vedere anche l'argomento su come [impostare le proprietà della connessione](https://msdn.microsoft.com/library/ms378988.aspx).   
* PolyBase non può eseguire l'autenticazione di Azure AD.   
* L'autenticazione di Azure AD è supportata per il database SQL nei pannelli **Importa database** ed **Esporta database** del portale di Azure. L'importazione e l'esportazione tramite l'autenticazione di Azure AD è supportata anche dal comando PowerShell.   
* L'autenticazione di Azure AD è supportata per il database SQL, per Istanza gestita e per SQL Data Warehouse tramite l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [Configurare e gestire l'autenticazione di Azure Active Directory con il database SQL oppure con SQL Data Warehouse](sql-database-aad-authentication-configure.md) e [SQL Server - az sql server](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="next-steps"></a>Passaggi successivi
- Per informazioni su come creare e popolare Azure AD e quindi configurare Azure AD con il database SQL di Azure o con Azure SQL Data Warehouse, vedere [Configurare e gestire l'autenticazione di Azure Active Directory con il database SQL, con Istanza gestita oppure con SQL Data Warehouse](sql-database-aad-authentication-configure.md).
- Per una panoramica dell'accesso e del controllo nel database SQL, vedere l'articolo relativo al [controllo dell'accesso al database SQL](sql-database-control-access.md).
- Per una panoramica degli account di accesso, degli utenti e dei ruoli del database nel database SQL, vedere l'articolo relativo ad [account di accesso, utenti e ruoli del database](sql-database-manage-logins.md).
- Per altre informazioni sulle entità di database, vedere [Entità](https://msdn.microsoft.com/library/ms181127.aspx).
- Per altre informazioni sui ruoli del database, vedere [Ruoli a livello di database](https://msdn.microsoft.com/library/ms189121.aspx).
- Per informazioni generali sulle regole del firewall, vedere l'articolo relativo alle [regole del firewall per il database SQL](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png

