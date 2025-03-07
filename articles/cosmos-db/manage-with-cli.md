---
title: Gestire le risorse di Azure Cosmos DB usando l'interfaccia della riga di comando di Azure
description: Usare l'interfaccia della riga di comando di Azure per gestire l'account, il database e i contenitori di Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: f9d8bf9161343e4b36a3c16209873962b69d8af5
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615218"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Gestire le risorse di Azure Cosmos DB usando l'interfaccia della riga di comando di Azure

La guida seguente illustra i comandi comuni per automatizzare la gestione degli account Azure Cosmos DB, dei database e dei contenitori tramite l'interfaccia della riga di comando di Azure. Per tutti i comandi dell'interfaccia della riga di comando di Azure Cosmos DB sono disponibili pagine di riferimento in [Informazioni di riferimento sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/cosmosdb). Altri esempi sono disponibili in [Esempi dell'interfaccia della riga di comando di Azure Cosmos DB](cli-samples.md), incluse le procedure per creare e gestire gli account, i database e i contenitori di Cosmos DB per MongoDB, Gremlin, Cassandra e API Tabella.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>Creare un account Azure Cosmos DB

Per creare un account di Azure Cosmos DB con l'API SQL, la coerenza di sessione nelle aree Stati Uniti orientali e Stati Uniti occidentali, eseguire il comando seguente:

```azurecli-interactive
az cosmosdb create \
   --name mycosmosdbaccount # must be lowercase and < 31 characters \
   --resource-group myResourceGroup \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations regionName=EastUS failoverPriority=0 isZoneRedundant=False \
   --locations regionName=WestUS failoverPriority=1 isZoneRedundant=False \
   --enable-multiple-write-locations false
```

> [!IMPORTANT]
> Il nome dell'account Azure Cosmos deve essere minuscolo.

## <a name="create-a-database"></a>Creazione di un database

Per creare un database Cosmos, eseguire il comando seguente:

```azurecli-interactive
az cosmosdb database create \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup
```

## <a name="create-a-container"></a>Creare un contenitore

Per creare un contenitore Cosmos con ur/s di 400 e una chiave di partizione, eseguire il comando seguente:

```azurecli-interactive
# Create a container
az cosmosdb collection create \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --partition-key-path /myPartitionKey \
   --throughput 400
```

## <a name="change-the-throughput-of-a-container"></a>Cambiare la velocità effettiva di un contenitore

Per modificare la velocità effettiva di un contenitore Cosmos su 1000 ur/s, eseguire il comando seguente:

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --throughput 1000
```

## <a name="list-account-keys"></a>Elencare le chiavi dell'account

Per ottenere le chiavi per l'account Cosmos, eseguire il comando seguente:

```azurecli-interactive
# List account keys
az cosmosdb keys list \
   --name  mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="list-connection-strings"></a>Elencare le stringhe di connessione

Per ottenere le stringhe di connessione per l'account Cosmos, eseguire il comando seguente:

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="regenerate-account-key"></a>Rigenerare una chiave dell'account

Per rigenerare una nuova chiave primaria per l'account Cosmos, eseguire il comando seguente:

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --key-kind primary
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere:

- [Installare l'interfaccia da riga di comando di Azure](/cli/azure/install-azure-cli)
- [Informazioni di riferimento sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Esempi dell'interfaccia della riga di comando di Azure Cosmos DB](cli-samples.md)
