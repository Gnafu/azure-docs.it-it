---
title: Script dell'interfaccia della riga di comando di Azure - Scaricare i log del server in Database di Azure per PostgreSQL
description: Questo script di esempio dell'interfaccia della riga di comando di Azure mostra come abilitare e scaricare i log di un server di Database di Azure per PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: 21c2ed0e58bddb58633968b426e06bc95c44ee99
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948214"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Abilitare e scaricare i log di query lente di un server di Database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure
Questo script di esempio dell'interfaccia della riga di comando abilita e scarica i log di query lente di un singolo server di Database di Azure per PostgreSQL.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di eseguire l'interfaccia della riga di comando in locale, per questo articolo è necessaria l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Controllare la versione eseguendo `az --version`. Per installare l'interfaccia della riga di comando di Azure o aggiornarne la versione, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script di esempio
In questo script di esempio modificare le righe evidenziate per aggiornare il nome utente e la password amministratore con i propri valori. Sostituire <log_file_name> nei comandi `az monitor` con il nome del file di log del server.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/server-logs.sh?highlight=18-19 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione
Dopo l'esecuzione dello script, usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse a esso associate. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Spiegazione dello script
Questo script usa i comandi illustrati nella tabella seguente:

| **Comando** | **Note** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az postgres server create](/cli/azure/postgres/server#az_msql_server_create) | Crea un server PostgreSQL che ospita i database. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_list) | Elenca i valori di configurazione per un server. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_set) | Aggiorna la configurazione di un server. |
| [az postgres server-logs list](/cli/azure/postgres/server-logs#az_postgres_server_logs_list) | Elenca i file di log per un server. |
| [az postgres server-logs download](/cli/azure/postgres/server-logs#az_postgres_server_logs_download) | Scarica i file di log. |
| [az group delete](/cli/azure/group#az_group_delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sull'interfaccia della riga di comando di Azure: [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).
- Provare a eseguire altri script: [esempi dell'interfaccia della riga di comando di Azure per il database di Azure per PostgreSQL](../sample-scripts-azure-cli.md)
- [Configurare e accedere ai log del server nel portale di Azure](../howto-configure-server-logs-in-portal.md)
