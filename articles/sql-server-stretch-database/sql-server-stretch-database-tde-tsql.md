---
title: Abilitare Transparent Data Encryption per Stretch Database (T-SQL) - Azure | Microsoft Docs
description: Abilitare Transparent Data Encryption (TDE) per SQL Server Stretch Database su Azure TSQL
services: sql-server-stretch-database
documentationcenter: ''
ms.assetid: 27753d91-9ca2-4d47-b34d-b5e2c2f029bb
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
author: blazem-msft
ms.author: blazem
ms.reviewer: jroth
manager: jroth
ms.openlocfilehash: 9718db18ea675fa744262f0736aff3c07732e1d1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002880"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Abilitare Transparent Data Encryption (TDE) per Stretch Database su Azure (Transact-SQL)
> [!div class="op_single_selector"]
> * [Portale di Azure](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

La funzionalità Transparent Data Encryption (TDE) consente di proteggersi da attività dannose eseguendo in tempo reale la crittografia e la decrittografia dei database, dei backup associati e dei file di log delle transazioni inattivi, senza dover apportare modifiche all'applicazione.

TDE esegue la crittografia dell'archiviazione di un intero database usando una chiave simmetrica detta "chiave di crittografia del database". La chiave di crittografia del database è protetta da un certificato server incorporato. Il certificato server incorporato è univoco per ogni server Azure. Microsoft ruota automaticamente questi certificati almeno ogni 90 giorni. Per una descrizione generale della funzionalità TDE, vedere [Transparent Data Encryption (TDE)].

## <a name="enabling-encryption"></a>Abilitazione della crittografia
Per abilitare la funzionalità TDE per un database di Azure che archivia i dati migrati da un database SQL Server con Estensione abilitata, eseguire le operazioni seguenti:

1. Connettere il database *master* sul server Azure che ospita il database usando un account di accesso di un amministratore o di un membro del ruolo **dbmanager** nel database master
2. Eseguire l'istruzione seguente per crittografare il database.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Disabilitazione della crittografia
Per disabilitare TDE per un database di Azure che archivia i dati migrati da un database SQL Server con Estensione abilitata, eseguire le operazioni seguenti:

1. Connettere il database *master* usando un account di accesso di un amministratore o di un membro del ruolo **dbmanager** nel database master.
2. Eseguire l'istruzione seguente per crittografare il database.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>Verifica della crittografia
Per verificare lo stato della crittografia per un database di Azure che archivia i dati migrati da un database SQL Server con Estensione abilitata, eseguire le operazioni seguenti:

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

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
