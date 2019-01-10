---
title: Raccolte di connessioni per database SQL | Documentazione Microsoft
description: Contiene collegamenti per il download di moduli che consentono la connessione a SQL Server e a database SQL da una vasta gamma di linguaggi di programmazione client.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: ''
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 29a6c7107a57babb5dc81fa22146897d3958f0f0
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54036342"
---
# <a name="connectivity-libraries-and-frameworks-for-sql-server"></a>Librerie di connettività e framework per SQL Server

Per iniziare rapidamente a usare linguaggi di programmazione quali C#, Java, Node.js, PHP e Python, consultare le [esercitazioni introduttive](https://aka.ms/sqldev). Creare quindi un'app usando SQL Server in Linux o Windows oppure Docker in macOS.

La tabella seguente elenca le librerie di connettività o *driver* che le applicazioni client possono usare, partendo da una vasta gamma di linguaggi, per connettersi SQL Server in esecuzione in locale o nel cloud. È possibile usare le librerie in Linux, Windows o Docker per connettersi al database SQL di Azure e ad Azure SQL Data Warehouse. 

| Linguaggio | Piattaforma | Risorse aggiuntive | Download | Attività iniziali |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET per SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [Scaricare](https://www.microsoft.com/net/download/) | [Introduzione](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Microsoft JDBC Driver per SQL Server](https://msdn.microsoft.com/library/mt484311.aspx) | [Scaricare](https://go.microsoft.com/fwlink/?linkid=852460) |  [Introduzione](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Driver SQL PHP per SQL Server](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [Scaricare](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [Introduzione](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Driver Node.js per SQL Server](https://msdn.microsoft.com/library/mt652093.aspx) | [Installa](https://msdn.microsoft.com/library/mt652094.aspx) |  [Introduzione](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Driver SQL Python](https://msdn.microsoft.com/library/mt652092.aspx) | Opzioni di installazione: <br/> \*[pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [Introduzione](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Driver Ruby per SQL Server](https://msdn.microsoft.com/library/mt691981.aspx) | [Installa](https://msdn.microsoft.com/library/mt711041.aspx) | [Introduzione](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Microsoft ODBC Driver for SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Scaricare](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

La tabella seguente elenca esempi di framework ORM (Object Relational Mapping) e framework Web che possono essere usati dalle applicazioni client con SQL Server in esecuzione in locale o nel cloud. È possibile usare i framework in Linux, Windows o Docker per connettersi al database SQL e a SQL Data Warehouse. 

| Linguaggio | Piattaforma | ORM |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernate ORM](http://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (Eloquent)](https://laravel.com/docs/5.0/eloquent) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](http://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](http://rubyonrails.org/) |
||||

## <a name="related-links"></a>Collegamenti correlati
- [Driver di SQL Server](https://msdn.microsoft.com/library/mt654049.aspx) per la connessione da applicazioni client
- Connessione al database SQL:
    - [Connettersi al database SQL tramite .NET (C#)](sql-database-connect-query-dotnet.md)
    - [Connettersi al database SQL tramite PHP](sql-database-connect-query-php.md)
    - [Connettersi al Database SQL tramite Node.js](sql-database-connect-query-nodejs.md)
    - [Connettersi al database SQL tramite Java](sql-database-connect-query-java.md)
    - [Connettersi al database SQL tramite Python](sql-database-connect-query-python.md)
    - [Connettersi al database SQL tramite Ruby](sql-database-connect-query-ruby.md)
- Esempi di codice di logica di ripetizione dei tentativi:
    - [Connettersi in modo resiliente a SQL con ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
    - [Connettersi in modo resiliente a SQL tramite PHP][step-4-connect-resiliently-to-sql-with-php-p42h]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

