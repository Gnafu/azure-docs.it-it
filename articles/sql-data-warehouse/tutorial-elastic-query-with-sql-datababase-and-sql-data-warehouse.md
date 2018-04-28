---
title: 'Esercitazione: query elastiche con Azure SQL Data Warehouse | Microsoft Docs'
description: Questa esercitazione usa la funzionalità di query elastiche per eseguire query su Azure SQL Data Warehouse dal database SQL di Azure.
services: sql-data-warehouse
author: hirokib
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/14/2018
ms.author: elbutter
ms.reviewer: igorstan
ms.openlocfilehash: a31f035b5ec086a046028956c4a9c0de0d6a313d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-use-elastic-query-to-access-data-in-azure-sql-data-warehouse-from-azure-sql-database"></a>Esercitazione: usare query elastiche per accedere ai dati in Azure SQL Data Warehouse dal database SQL di Azure

Questa esercitazione usa la funzionalità di query elastiche per eseguire query su Azure SQL Data Warehouse dal database SQL di Azure. 

## <a name="prerequisites-for-the-tutorial"></a>Prerequisiti per l'esercitazione

Prima di iniziare questa esercitazione, sono necessari i prerequisiti seguenti:

1. SQL Server Management Studio (SSMS) installato.
2. Server Azure SQL creato con un database e un data warehouse all'interno del server.
3. Regole del firewall configurate per l'accesso al server Azure SQL.

## <a name="set-up-connection-between-sql-data-warehouse-and-sql-database-instances"></a>Connessione configurata tra istanze di SQL Data Warehouse e del database SQL 

1. Usando SSMS o un altro client di query, aprire una nuova query per il database **master** nel server logico.

2. Creare un account di accesso e un utente che rappresentino la connessione dal database SQL al data warehouse.

   ```sql
   CREATE LOGIN SalesDBLogin WITH PASSWORD = 'aReallyStrongPassword!@#';
   ```

3. Usando SSMS o un altro client di query, aprire una nuova query per l'**istanza di SQL Data Warehouse** nel server logico.

4. Creare un utente nell'istanza del data warehouse con l'account di accesso creato nel passaggio 2

   ```sql
   CREATE USER SalesDBUser FOR LOGIN SalesDBLogin;
   ```

5. Concedere le autorizzazioni desiderate all'utente creato nel passaggio 4 per l'esecuzione del database SQL. In questo esempio viene concessa solo l'autorizzazione SELECT per uno schema specifico, mostrando in che modo limitare le query dal database SQL a un dominio specifico. 

   ```sql
   GRANT SELECT ON SCHEMA :: [dbo] TO SalesDBUser;
   ```

6. Usando SSMS o un altro client di query, aprire una nuova query per l'**istanza di database SQL** nel server logico.

7. Creare una chiave master, se non ne esiste già una. 

   ```sql
   CREATE MASTER KEY; 
   ```

8. Creare una credenziale con ambito di database usando le credenziali create nel passaggio 2.

   ```sql
   CREATE DATABASE SCOPED CREDENTIAL SalesDBElasticCredential
   WITH IDENTITY = 'SalesDBLogin',
   SECRET = 'aReallyStrongPassword@#!';
   ```

9. Creare un'origine dati esterna che punti all'istanza del data warehouse.

   ```sql
   CREATE EXTERNAL DATA SOURCE EnterpriseDwSrc WITH 
       (TYPE = RDBMS, 
       LOCATION = '<SERVER NAME>.database.windows.net', 
       DATABASE_NAME = '<SQL DATA WAREHOUSE NAME>', 
       CREDENTIAL = SalesDBElasticCredential, 
   ) ;
   ```

10. È ora possibile creare le tabelle esterne che fanno riferimento a questa origine dati esterna. Le query che usano queste tabelle vengono inviate all'istanza del data warehouse per l'elaborazione e quindi restituite all'istanza del database.


## <a name="elastic-query-from-sql-database-to-sql-data-warehouse"></a>Query elastica dal database SQL a SQL Data Warehouse

Nei prossimi passaggi verrà creata una tabella nell'istanza del data warehouse con diversi valori. Verrà quindi mostrato come configurare una tabella esterna per eseguire query sull'istanza del data warehouse dall'istanza di database.

1. Usando SSMS o un altro client di query, aprire una nuova query per **SQL Data Warehouse** nel server logico.

2. Inviare la query seguente per creare una tabella **OrdersInformation** nell'istanza del data warehouse.

   ```sql
   CREATE TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL 
   ,   [CustomerID] [int] NOT NULL 
   ) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)
   ```

3. Usando SSMS o un altro client di query, aprire una nuova query per il **database SQL** nel server logico.

4. Inviare la query seguente per creare una definizione della tabella esterna che punti alla tabella **OrdersInformation** nell'istanza del data warehouse.

   ```sql
   CREATE EXTERNAL TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL
   ,   [CustomerID] [int] NOT NULL 
   ) 
   WITH 
   (
        DATA_SOURCE = EnterpriseDwSrc
   ,    SCHEMA_NAME = N'dbo'
   ,    OBJECT_NAME = N'OrderInformation'
   )
   ```

5. Osservare che è ora presente una definizione della tabella esterna nell'**istanza di database SQL**.

   ![Definizione della tabella esterna della query elastica](media/sql-data-warehouse-elastic-query-with-sql-database/elastic-query-external-table.png)


6. Inviare la query seguente, che viene eseguita sull'istanza del data warehouse. Si dovrebbero ricevere i cinque valori inseriti nel passaggio 2. 

```sql
SELECT * FROM [dbo].[OrderInformation];
```

> [!NOTE]
>
> Nonostante i valori siano pochi, la query impiega una quantità di tempo notevole per restituire i risultati. Quando si usa una query elastica con il data warehouse, è consigliabile tenere conto dei costi generali dell'elaborazione delle query e dello spostamento in rete. Usare l'esecuzione remota di query elastiche quando la potenza di elaborazione, anziché la latenza, è l'aspetto prioritario.

Sono stati configurati gli elementi di base delle query elastiche. 

## <a name="next-steps"></a>Passaggi successivi
Per altre raccomandazioni, vedere [Procedure consigliate per l'uso delle query elastiche nel database SQL di Azure](how-to-use-elastic-query-with-sql-data-warehouse.md).