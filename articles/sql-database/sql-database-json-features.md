---
title: Uso dei dati JSON nel database SQL di Azure | Microsoft Docs
description: Il database SQL di Azure consente di analizzare, formattare ed eseguire query sui dati nella notazione JavaScript Object Notation (JSON).
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 01/15/2019
ms.openlocfilehash: 3a09fba3f01eec6c712bad67ef10b8b5c55fb33e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567840"
---
# <a name="getting-started-with-json-features-in-azure-sql-database"></a>Introduzione alle funzionalità JSON nel database SQL di Azure
Il database SQL di Azure consente di analizzare ed eseguire query sui dati rappresentati in formato JavaScript Object Notation [(JSON)](https://www.json.org/) e di esportare i dati relazionali come testo JSON. Nel database SQL di Azure sono disponibili gli scenari JSON seguenti:
- [Formattazione di dati relazionali in formato JSON](#formatting-relational-data-in-json-format) tramite la clausola `FOR JSON`.
- [Uso dei dati JSON](#working-with-json-data)
- [Query sui dati JSON](#querying-json-data) tramite le funzioni scalari JSON.
- [Trasformazione del formato JSON in formato tabulare](#transforming-json-into-tabular-format) tramite la funzione `OPENJSON`.

## <a name="formatting-relational-data-in-json-format"></a>Formattazione di dati relazionali in formato JSON
Se si dispone di un servizio Web che accetta dati dal livello di database e fornisce una risposta in formato JSON o framework JavaScript sul lato client o raccolte che accettano dati formattati come JSON, è possibile formattare il contenuto del database nel formato JSON direttamente in una query SQL. Non è più necessario scrivere il codice dell'applicazione che formatta i risultati dal database SQL di Azure come JSON o includere alcune librerie di serializzazione JSON per convertire i risultati della query in formato tabulare e quindi serializzare oggetti in formato JSON. È possibile usare la clausola FOR JSON per formattare i risultati della query SQL come JSON nel database SQL di Azure e usarla direttamente nell'applicazione.

Nell'esempio seguente, le righe della tabella Sales.Customer vengono formattate come JSON usando la clausola FOR JSON:

```
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

La clausola FOR JSON PATH formatta i risultati della query come testo JSON. I nomi di colonna vengono usati come chiavi, mentre i valori di cella vengono generati come valori JSON:

```
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

Il set di risultati viene formattato come array JSON in cui ogni riga viene formattata come un oggetto JSON separato.

PATH indica che è possibile personalizzare il formato di output del risultato JSON usando la notazione del punto negli alias di colonna. La query seguente cambia il nome della chiave "CustomerName" nel formato JSON di output e inserisce i numeri di telefono e fax nell'oggetto secondario "Contatto":

```
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

L'output di questa query è simile al seguente:

```
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

In questo esempio viene restituito un singolo oggetto JSON anziché un array, specificando l'opzione [WITHOUT_ARRAY_WRAPPER](https://msdn.microsoft.com/library/mt631354.aspx). È possibile usare questa opzione se è noto che si sta restituendo un oggetto singolo come risultato della query.

Il valore principale della clausola FOR JSON è che consente di restituire dati gerarchici complessi dal database formattati come array o oggetti JSON annidati. Nell'esempio seguente viene illustrato come includere le righe della tabella `Orders` che appartengono a `Customer` come array annidato di `Orders`:

```
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER

```

Anziché inviare query separate per ottenere i dati dei clienti e quindi per recuperare un elenco di ordini correlati, è possibile ottenere tutti i dati necessari con una singola query, come illustrato nel seguente output di esempio:

```
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
]
}
```

## <a name="working-with-json-data"></a>Uso dei dati JSON
Se non si dispone di dati rigorosamente strutturati, se si dispone di oggetti secondari, array o dati gerarchici complessi, oppure se le strutture di dati si evolvono nel tempo, il formato JSON può essere utile per rappresentare una struttura di dati complessa.

JSON è un formato testuale che può essere usato come qualsiasi altro tipo di stringa nel database SQL di Azure. È possibile inviare o archiviare i dati JSON come NVARCHAR standard:

```
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

I dati JSON usati in questo esempio vengono rappresentati con il tipo NVARCHAR(MAX). Il formato JSON può essere inserito in questa tabella o fornito come argomento della stored procedure usando la sintassi Transact-SQL standard, come illustrato nell'esempio seguente:

```
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Qualsiasi lingua o raccolta client-side compatibile con i dati della stringa nel database SQL di Azure è compatibile anche con i dati JSON. I dati JSON possono essere archiviati in qualsiasi tabella che supporta il tipo NVARCHAR, ad esempio una tabella ottimizzata per la memoria o una tabella con controllo delle versioni del sistema. Il formato JSON non introduce alcun vincolo nel codice lato client o nel livello di database.

## <a name="querying-json-data"></a>Query sui dati JSON
Se si dispone di dati formattati come JSON archiviati in tabelle SQL di Azure, le funzioni JSON consentono di usare i dati in qualsiasi query SQL.

Le funzioni JSON disponibili nel database SQL di Azure consentono di trattare i dati formattati come JSON come qualsiasi altro tipo di dati SQL. È facilmente possibile estrarre i valori dal testo JSON e usare dati JSON in qualsiasi query:

```
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

La funzione JSON_VALUE estrae un valore dal testo JSON archiviato nella colonna Data. Questa funzione usa un percorso in stile JavaScript per fare riferimento a un valore nel testo JSON da estrarre. Il valore estratto può essere usato in qualsiasi parte di una query SQL.

La funzione JSON_QUERY è simile a JSON_VALUE. A differenza di JSON_VALUE, questa funzione estrae l'oggetto secondario complesso, ad esempio array o oggetti che vengono inseriti nel testo JSON.

La funzione JSON_MODIFY consente di specificare il percorso del valore nel testo JSON che deve essere aggiornato, nonché un nuovo valore che sovrascriverà quello precedente. In questo modo è possibile aggiornare facilmente il testo JSON senza ripetere l'analisi dell'intera struttura.

Dal momento che il tasto JSON è archiviato in un testo standard, non vi sono garanzie che i valori archiviati nelle colonne di testo siano formattate correttamente. È possibile verificare che il testo archiviato nella colonna JSON sia formattato correttamente usando i vincoli di controllo standard del database SQL di Azure e la funzione ISJSON:

```
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Se il testo di input è formattato correttamente come JSON, la funzione ISJSON restituisce il valore 1. A ogni inserimento o aggiornamento della colonna JSON, questo vincolo verificherà che nuovo valore di testo non sia un formato JSON non corretto.

## <a name="transforming-json-into-tabular-format"></a>Trasformazione del formato JSON in formato tabulare
Il database SQL di Azure consente anche di trasformare gli insiemi JSON in formato tabulare e di caricare o eseguire query sui dati JSON.

OPENJSON è una funzione con valori di tabella che analizza il testo JSON, individua un array di oggetti JSON, scorre gli elementi dell'array e restituisce una riga nel risultato di output per ogni elemento dell'array.

![JSON tabulare](./media/sql-database-json-features/image_2.png)

Nell'esempio precedente, è possibile specificare dove si trova l'array JSON da aprire (nel percorso $ Orders), quali colonne devono essere restituite come risultato e dove trovare i valori JSON che verranno restituiti come celle.

È possibile trasformare un array JSON nella variabile @orders in un set di righe, analizzare il set di risultati o inserire righe in una tabella standard:

```
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    FROM OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )

END
```
La raccolta di ordini formattata come array JSON e fornita come parametro alla stored procedure può essere analizzata e inserita nella tabella Orders.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come integrare JSON nell'applicazione, consultare le seguenti risorse:

* [Blog di TechNet](https://blogs.technet.microsoft.com/dataplatforminsider/20../../json-in-sql-server-2016-part-1-of-4/)
* [Documentazione di MSDN](https://msdn.microsoft.com/library/dn921897.aspx)
* [Video di Channel 9](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

Per altre informazioni sui vari scenari per l'integrazione di JSON nell'applicazione, vedere le dimostrazioni in questo [video di Channel 9](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) o trovare uno scenario corrispondente al caso d'uso nei [post del blog su JSON](https://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/).

