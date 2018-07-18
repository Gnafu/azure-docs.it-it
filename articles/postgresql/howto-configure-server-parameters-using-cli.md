---
title: Configurare i parametri del servizio in Database di Azure per PostgreSQL
description: Questo articolo descrive come configurare i parametri del servizio in Database di Azure per PostgreSQL usando l'interfaccia della riga di comando di Azure.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: df32578c92b1973568c9b811cb7f9e9db067915f
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
ms.locfileid: "29691394"
---
# <a name="customize-server-configuration-parameters-using-azure-cli"></a>Personalizzare i parametri di configurazione server usando l'interfaccia della riga di comando di Azure
È possibile elencare, visualizzare e aggiornare i parametri di configurazione per un server PostgreSQL di Azure usando l'interfaccia della riga di comando di Azure. Un subset delle configurazioni del motore viene esposto a livello di server e può essere modificato. 

## <a name="prerequisites"></a>prerequisiti
Per proseguire con questa guida, si richiedono:
- Creare un server e un database di Database di Azure per PostgreSQL seguendo le istruzioni riportate nell'articolo [Creare un database di Azure per PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Installare l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli) nel computer oppure usare [Azure Cloud Shell](../cloud-shell/overview.md) nel portale di Azure tramite il browser.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Elencare i parametri di configurazione del server per Database di Azure per il server PostgreSQL
Per elencare tutti i parametri modificabili in un server e i relativi valori, eseguire il comando [az postgres server configuration list](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_list).

È possibile elencare i parametri di configurazione per il server **mydemoserver.postgres.database.azure.com** nel gruppo di risorse **myresourcegroup**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>Visualizzare i dettagli dei parametri di configurazione server
Per visualizzare i dettagli di un determinato parametro di configurazione per un server, eseguire il comando [az postgres server configuration show](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_show).

Questo esempio mostra i dettagli del parametro di configurazione del server **log\_min\_messages** per il server **mydemoserver.postgres.database.azure.com** nel gruppo di risorse **myresourcegroup.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>Modificare il valore di un parametro di configurazione server
È anche possibile modificare il valore di un determinato parametro di configurazione del server, che aggiorna il valore di configurazione sottostante del motore del server PostgreSQL. Per aggiornare la configurazione, usare il comando [az postgres server configuration set](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_set). 

Per aggiornare il parametro di configurazione del server **log\_min\_messages** per il server **mydemoserver.postgres.database.azure.com** nel gruppo di risorse **myresourcegroup**.
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver --value INFO
```
Per reimpostare il valore di un parametro di configurazione, è sufficiente non inserire il parametro facoltativo `--value`. In questo caso, il servizio applica il valore predefinito. Nell'esempio precedente sarà simile a quanto segue:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
Questo comando reimposta il parametro di configurazione **log\_min\_messages** sul valore predefinito **AVVISO**. Per altre informazioni sulla configurazione server e sui valori consentiti, vedere la documentazione di PostgreSQL in [Server Configuration](https://www.postgresql.org/docs/9.6/static/runtime-config.html) (Configurazione server).

## <a name="next-steps"></a>Passaggi successivi
- Per configurare e accedere ai log del server, vedere [Server Logs in Azure Database for PostgreSQL](concepts-server-logs.md) (Log del server in Database di Azure per PostgreSQL)
