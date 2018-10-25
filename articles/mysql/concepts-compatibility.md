---
title: Compatibilità degli strumenti di gestione e dei driver MySQL
description: Questo articolo descrive i driver MySQL e gli strumenti di gestione compatibili con Database di Azure per MySQL.
services: mysql
author: ajlam
ms.author: andrela
editor: jasonwhowell
manager: kfile
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 9e56c2bd65f8a9a517a7cdebe02a1d051c689df6
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985890"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>Driver MySQL e strumenti di gestione compatibili con Database di Azure per MySQL
Questo articolo descrive i driver e gli strumenti di gestione compatibili con il Database di Azure per MySQL.

## <a name="mysql-drivers"></a>Driver di MySQL
Database di Azure per MySQL usa la versione di community del database MySQL più diffusa al mondo. Pertanto è compatibile con un'ampia gamma di linguaggi di programmazione e driver. L'obiettivo è supportare le tre versioni più recenti dei driver MySQL e continuare l'attività  con gli autori della community open source per migliorare costantemente le funzionalità  e l'usabilità  dei driver MySQL. Nella tabella seguente è riportato un elenco di driver che sono stati testati e che risultano compatibili con Database di Azure per MySQL 5.6 e 5.7:

| **Driver** | **Collegamenti** | **Versioni compatibili** | **Versioni non compatibili** | **Note** |
| :-------- | :------------------------ | :----------- | :---------------------- | :--------------------------------------- |
| PHP | https://secure.php.net/downloads.php | 5.5 5.6 7.x | 5.3 | Per la connessione PHP 7.0 con SSL MySQLi, aggiungere MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT nella stringa di connessione. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Impostazione PDO: opzione ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` su false.|
| .Net | [MySqlConnector su GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Pacchetto di installazione di Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0.27 e successive | 0.26.5 e precedenti | |
| NodeJS |  [MySQLjs su GitHub](https://github.com/mysqljs/mysql/releases) <br> Pacchetto di installazione di NPM:<br> Eseguire `npm install mysql` da NPM | 2.15 | 2.14.1 e precedenti | |
| GO | https://github.com/go-sql-driver/mysql/releases | 1.3 | 1.2 e precedenti | Usare allowNativePasswords=true nella stringa di connessione |
| Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 e precedenti | |
| Java | https://downloads.mariadb.org/connector-java/ | 2.1 2.0 1.6 | 1.5.5 e precedenti | |

## <a name="management-tools"></a>Strumenti di gestione
Il vantaggio della compatibilità si estende anche agli strumenti di gestione del database. Gli strumenti esistenti continueranno a funzionare con Database di Azure per MySQL, purché la modifica del database operi entro i confini di autorizzazione dell'utente. Nella tabella seguente sono elencati tre strumenti comuni di gestione del database che sono stati testati e che risultano compatibili con il Database di Azure per MySQL 5.6 e 5.7:

|                                     | **MySQL Workbench 6.x e versioni successive** | **Navicat 12** | **PHPMyAdmin 4.x e versioni successive** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| Creare, aggiornare, leggere, scrivere, eliminare | X | X | X |
| Connessione SSL | X | X | X |
| Completamento automatico della query SQL | X | X |  |
| Importare ed esportare dati | X | X | X |
| Esportare in più formati | X | X | X |
| Backup e ripristino |  | X |  |
| Visualizzare i parametri del server | X | X | X |
| Visualizzare le connessioni client | X | X | X |
