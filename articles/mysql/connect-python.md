---
title: Connettersi a Database di Azure per MySQL da Python
description: Questa guida introduttiva fornisce diversi esempi di codice Python che è possibile usare per connettersi ai dati ed eseguire query da Database di Azure per MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 08/08/2019
ms.openlocfilehash: 0940d307d78236fea1a232c1e7c60a296ba46c62
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195168"
---
# <a name="azure-database-for-mysql-use-python-to-connect-and-query-data"></a>Database di Azure per MySQL: usare Python per connettersi ai dati ed eseguire query sui dati
Questa guida introduttiva illustra come usare [Python](https://python.org) per connettersi a un database di Azure per MySQL. Usa istruzioni SQL per eseguire query, inserire, aggiornare ed eliminare dati nel database dalle piattaforme Mac OS, Ubuntu Linux e Windows. Questo argomento presuppone che si abbia familiarità con lo sviluppo con Python, ma non con Database di Azure per MySQL.

## <a name="prerequisites"></a>Prerequisiti
Questa guida introduttiva usa le risorse create in una delle guide seguenti come punto di partenza:
- [Create an Azure Database for MySQL server using Azure portal](./quickstart-create-mysql-server-database-using-azure-portal.md) (Creare un database di Azure per il server MySQL usando il portale di Azure)
- [Creare un database di Azure per il server MySQL tramite l'interfaccia della riga di comando di Azure](./quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="install-python-and-the-mysql-connector"></a>Installare Python e il connettore MySQL
Installare [Python](https://www.python.org/downloads/) e il [connettore MySQL per Python](https://dev.mysql.com/downloads/connector/python/) nel computer. A seconda della piattaforma, seguire le istruzioni nella sezione appropriata tra quelle riportate di seguito. 

> [!NOTE]
> Questa guida introduttiva utilizza un approccio basato su una query SQL non elaborata per connettersi a MySQL ed eseguire le query. Se si usa un framework Web, usare il connettore consigliato per questi framework. Per il framework Django, ad esempio, è consigliato il connettore [mysqlclient](https://pypi.org/project/mysqlclient/).
>

### <a name="windows"></a>Windows
1. Scaricare e installare Python 3.7 da [python.org](https://www.python.org/downloads/windows/). 
2. Verificare l'installazione di Python avviando il prompt dei comandi. Eseguire il comando `C:\python37\python.exe -V` usando l'opzione della V maiuscola per visualizzare il numero di versione.
3. Installare il connettore Python per MySQL corrispondente alla versione di Python da [mysql.com](https://dev.mysql.com/downloads/connector/python/).

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. In Linux (Ubuntu), Python viene in genere installato nell'ambito dell'installazione predefinita.
2. Verificare l'installazione di Python avviando la shell Bash. Eseguire il comando `python -V` usando l'opzione della V maiuscola per visualizzare il numero di versione.
3. Verificare l'installazione di PIP eseguendo il comando `pip show pip -V` per visualizzare il numero di versione. 
4. PIP può essere incluso in alcune versioni di Python. Se PIP non è installato, è possibile installare il pacchetto [PIP](https://pip.pypa.io/en/stable/installing/) eseguendo il comando `sudo apt-get install python-pip`.
5. Aggiornare PIP alla versione più recente eseguendo il comando `pip install -U pip`.
6. Installare il connettore di MySQL per Python e le relative dipendenze usando il comando PIP:

   ```bash
   sudo pip install mysql-connector-python-rf
   ```
 
### <a name="macos"></a>MacOS
1. In Mac OS, Python viene in genere installato nell'ambito dell'installazione del sistema operativo predefinita.
2. Verificare l'installazione di Python avviando la shell Bash. Eseguire il comando `python -V` usando l'opzione della V maiuscola per visualizzare il numero di versione.
3. Verificare l'installazione di PIP eseguendo il comando `pip show pip -V` per visualizzare il numero di versione.
4. PIP può essere incluso in alcune versioni di Python. Se PIP non è installato, è possibile installare il pacchetto [PIP](https://pip.pypa.io/en/stable/installing/).
5. Aggiornare PIP alla versione più recente eseguendo il comando `pip install -U pip`.
6. Installare il connettore di MySQL per Python e le relative dipendenze usando il comando PIP:

   ```bash
   pip install mysql-connector-python-rf
   ``` 

## <a name="get-connection-information"></a>Ottenere informazioni di connessione
Ottenere le informazioni di connessione necessarie per connettersi al database di Azure per MySQL. Sono necessari il nome del server completo e le credenziali di accesso.

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Dal menu a sinistra nel portale di Azure scegliere **Tutte le risorse** e quindi cercare il server creato, ad esempio **mydemoserver**.
3. Selezionare il nome del server.
4. Nel pannello **Panoramica** del server prendere nota dei valori riportati in **Nome server** e **Nome di accesso dell'amministratore server**. Se si dimentica la password, in questo pannello è anche possibile reimpostarla.
 ![Nome del server del database di Azure per MySQL](./media/connect-python/1_server-overview-name-login.png)

## <a name="run-python-code"></a>Eseguire il codice Python
- Incollare il codice in un file di testo e quindi salvarlo in una cartella di progetto con estensione py, ad esempio C:\pythonmysql\createtable.py o /home/username/pythonmysql/createtable.py.
- Per eseguire il codice, avviare il prompt dei comandi o la shell Bash. Passare alla cartella del progetto `cd pythonmysql`. Per eseguire l'applicazione, digitare quindi il comando python seguito dal nome del file `python createtable.py`. Nel sistema operativo Windows, se python.exe non viene trovato, potrebbe essere necessario specificare il percorso completo del file eseguibile oppure aggiungere il percorso di Python nella variabile di ambiente PATH. `C:\python27\python.exe createtable.py`

## <a name="connect-create-table-and-insert-data"></a>Connettersi, creare tabelle e inserire dati
Usare il codice seguente per connettersi al server, creare una tabella e caricare i dati usando un'istruzione SQL **INSERT**. 

Nel codice viene importata la libreria mysql.connector. La funzione [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) viene usata per connettersi a Database di Azure per MySQL usando gli [argomenti di connessione](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) presenti nella raccolta di configurazione. Il codice usa un cursore per la connessione, mentre il metodo [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) esegue la query SQL sul database MySQL. 

Sostituire i parametri `host`, `user`, `password` e `database` con i valori specificati al momento della creazione del server e del database.

```Python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'mydemoserver.mysql.database.azure.com',
  'user':'myadmin@mydemoserver',
  'password':'yourpassword',
  'database':'quickstartdb'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Drop previous table of same name if one exists
  cursor.execute("DROP TABLE IF EXISTS inventory;")
  print("Finished dropping table (if existed).")

  # Create table
  cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
  print("Finished creating table.")

  # Insert some data into table
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
  print("Inserted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="read-data"></a>Leggere i dati
Usare il codice seguente per connettersi e leggere i dati usando un'istruzione SQL **SELECT**. 

Nel codice viene importata la libreria mysql.connector. La funzione [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) viene usata per connettersi a Database di Azure per MySQL usando gli [argomenti di connessione](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) presenti nella raccolta di configurazione. Il codice usa un cursore per la connessione, mentre il metodo [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) esegue l'istruzione SQL sul database MySQL. Le righe di dati vengono lette usando il metodo [fetchall()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html). Il set di risultati viene conservato in una riga della raccolta e viene usata un'istruzione di iterazione for per eseguire il ciclo sulle righe.

Sostituire i parametri `host`, `user`, `password` e `database` con i valori specificati al momento della creazione del server e del database.

```Python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'mydemoserver.mysql.database.azure.com',
  'user':'myadmin@mydemoserver',
  'password':'yourpassword',
  'database':'quickstartdb'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="update-data"></a>Aggiornare i dati
Usare il codice seguente per connettersi e aggiornare i dati usando un'istruzione SQL **UPDATE**. 

Nel codice viene importata la libreria mysql.connector.  La funzione [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) viene usata per connettersi a Database di Azure per MySQL usando gli [argomenti di connessione](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) presenti nella raccolta di configurazione. Il codice usa un cursore per la connessione, mentre il metodo [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) esegue l'istruzione SQL sul database MySQL. 

Sostituire i parametri `host`, `user`, `password` e `database` con i valori specificati al momento della creazione del server e del database.

```Python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'mydemoserver.mysql.database.azure.com',
  'user':'myadmin@mydemoserver',
  'password':'yourpassword',
  'database':'quickstartdb'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="delete-data"></a>Eliminare i dati
Usare il codice seguente per connettersi e rimuovere i dati usando un'istruzione SQL **DELETE**. 

Nel codice viene importata la libreria mysql.connector.  La funzione [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) viene usata per connettersi a Database di Azure per MySQL usando gli [argomenti di connessione](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) presenti nella raccolta di configurazione. Il codice usa un cursore per la connessione, mentre il metodo [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) esegue la query SQL sul database MySQL. 

Sostituire i parametri `host`, `user`, `password` e `database` con i valori specificati al momento della creazione del server e del database.

```Python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'mydemoserver.mysql.database.azure.com',
  'user':'myadmin@mydemoserver',
  'password':'yourpassword',
  'database':'quickstartdb'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established.")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password.")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist.")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Eseguire la migrazione del database usando le funzionalità di esportazione e importazione](./concepts-migrate-import-export.md)
