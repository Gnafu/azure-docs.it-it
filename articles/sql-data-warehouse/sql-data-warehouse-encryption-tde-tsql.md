---
title: Transparent Data Encryption in SQL Data Warehouse (T-SQL) | Documentazione Microsoft
description: Transparent Data Encryption (TDE) in SQL Data Warehouse (T-SQL).
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/30/2019
ms.author: kavithaj
ms.reviewer: rortloff
ms.openlocfilehash: 1d62bc5cb080f2167f13bd58e9a8a334b8ae5163
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65146078"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Introduzione a Transparent Data Encryption (TDE)
> [!div class="op_single_selector"]
> * [Proteggere un database in SQL Data Warehouse](sql-data-warehouse-overview-manage-security.md)
> * [Autenticazione](sql-data-warehouse-authentication.md)
> * [Introduzione a Transparent Data Encryption (TDE) di SQL Data Warehouse](sql-data-warehouse-encryption-tde.md)
> * [Introduzione a Transparent Data Encryption (TDE)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>Autorizzazioni necessarie
Per abilitare Transparent Data Encryption (TDE), è necessario essere un amministratore o un membro del ruolo dbmanager.

## <a name="enabling-encryption"></a>Abilitazione della crittografia
Per abilitare TDE per un SQL Data Warehouse, seguire questa procedura:

1. Connettere il database *master* sul server che ospita il database usando un account di accesso di un amministratore o di un membro del ruolo **dbmanager** nel database master.
2. Eseguire l'istruzione seguente per crittografare il database.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Disabilitazione della crittografia
Per disabilitare TDE per un SQL Data Warehouse, seguire questa procedura:

1. Connettere il database *master* usando un account di accesso di un amministratore o di un membro del ruolo **dbmanager** nel database master.
2. Eseguire l'istruzione seguente per crittografare il database.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> Prima di modificare le impostazioni TDE, è necessario interrompere la sospensione di SQL Data Warehouse.
> 
> 

## <a name="verifying-encryption"></a>Verifica della crittografia
Per verificare lo stato della crittografia per un SQL Data Warehouse, seguire questa procedura:

1. Connettere il database *master* o dell'istanza usando un account di accesso di un amministratore o di un membro del ruolo **dbmanager** nel database master.
2. Eseguire l'istruzione seguente per crittografare il database.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Il risultato ```1``` indica un database crittografato, ```0``` indica un database non crittografato.

## <a name="encryption-dmvs"></a>Viste a gestione dinamica della crittografia
* [sys.databases][sys.databases] 
* [sys.dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
