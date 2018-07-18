---
title: Accedere ai log del server in Database di Azure per MySQL usando l'interfaccia della riga di comando di Azure
description: Questo articolo descrive come accedere ai log del server in Database di Azure per MySQL usando l'utilità dell'interfaccia della riga di comando di Azure.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 85c7840c0e919e77e807e6114c4d0c65601ff334
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265827"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Configurare e accedere ai log del server usando l'interfaccia della riga di comando di Azure
È possibile scaricare i log del server di Database di Azure per MySQL usando l'interfaccia della riga di comando di Azure, l'utilità della riga di comando di Azure.

## <a name="prerequisites"></a>prerequisiti
Per proseguire con questa guida, si richiedono:
- [Server del Database di Azure per MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli) o Azure Cloud Shell nel browser

## <a name="configure-logging-for-azure-database-for-mysql"></a>Configurare la registrazione per Database di Azure per MySQL
Per configurare il server per l'accesso al log delle query lente di MySQL, seguire questa procedura:
1. Attivare la registrazione impostando il parametro **slow\_query\_log** su ON.
2. Regolare gli altri parametri, ad esempio **long\_query\_time** e **log\_slow\_admin\_statements**.

Per informazioni su come impostare il valore di questi parametri tramite l'interfaccia della riga di comando di Azure, vedere [Personalizzare i parametri di configurazione server usando l'interfaccia della riga di comando di Azure](howto-configure-server-parameters-using-cli.md). 

Il comando dell'interfaccia della riga di comando seguente, ad esempio, attiva il log delle query lente, imposta la durata di una query prolungata su 10 secondi e quindi disattiva la registrazione dell'istruzione per un amministratore lento. Infine elenca le opzioni di configurazione da verificare.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Elencare i log per il database di Azure per il server MySQL
Per elencare i file di log disponibili per il server, eseguire il comando [az mysql server-logs list](/cli/azure/mysql/server-logs#az_mysql_server_logs_list).

È possibile elencare i file di log per il server **mydemoserver.mysql.database.azure.com** nel gruppo di risorse **myresourcegroup**. Quindi indirizzare l'elenco dei file di log a un file di testo denominato **log\_files\_list.txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Scaricare i log dal server
Tramite il comando [az mysql server-logs download](/cli/azure/mysql/server-logs#az_mysql_server_logs_download) è possibile scaricare i singoli file di log per il server. 

Usare l'esempio seguente per scaricare il file di log specifico per il server **mydemoserver.mysql.database.azure.com** nel gruppo di risorse **myresourcegroup** nell'ambiente locale.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sui [log del server in Database di Azure per MySQL](concepts-server-logs.md).
