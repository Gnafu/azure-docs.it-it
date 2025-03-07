---
title: Livelli di coerenza e API di Azure Cosmos DB
description: Riconoscimento dei livelli di coerenza nelle API in Azure Cosmos DB.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 26cea6243a8b6d06c132325f0b2fe830c4030e9d
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467769"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Livelli di coerenza e API di Azure Cosmos DB

Azure Cosmos DB fornisce il supporto nativo per le API compatibili con il protocollo wire per i database più diffusi. Sono inclusi MongoDB, Apache Cassandra, Gremlin e archiviazione tabelle di Azure. Questi database non offrono modelli di coerenza definiti in modo accurato o garanzie basate sul contratto di contratto per i livelli di coerenza. In genere forniscono solo un subset dei cinque modelli di coerenza offerti da Cosmos DB. 

Quando si usa l'API SQL, l'API Gremlin e API Tabella, viene usato il livello di coerenza predefinito configurato nell'account Azure Cosmos. 

Quando si usa API Cassandra o l'API Azure Cosmos DB per MongoDB, le applicazioni ottengono un set completo di livelli di coerenza offerti rispettivamente da Apache Cassandra e MongoDB, con garanzie di coerenza e durabilità ancora più solide. Questo documento illustra i livelli di coerenza Azure Cosmos DB corrispondenti per i livelli di coerenza Apache Cassandra e MongoDB.


## <a id="cassandra-mapping"></a>Eseguire il mapping tra i livelli di coerenza di Azure Cosmos DB e Apache Cassandra

A differenza del database AzureCosmos, Apache Cassandra non fornisce in modo nativo garanzie di coerenza definite in modo nativo.  Apache Cassandra fornisce invece un livello di coerenza di scrittura e un livello di coerenza di lettura per consentire i compromessi di disponibilità elevata, coerenza e latenza. Quando si usa API Cassandra di Azure Cosmos DB: 

* Il livello di coerenza di scrittura di Apache Cassandra viene mappato al livello di coerenza predefinito configurato nell'account Azure Cosmos. 

* Azure Cosmos DB eseguirà il mapping dinamico del livello di coerenza di lettura specificato dal driver del client Cassandra a uno dei livelli di coerenza Azure Cosmos DB configurati in modo dinamico in una richiesta di lettura. 

La tabella seguente illustra come viene eseguito il mapping dei livelli di coerenza di Cassandra nativi ai livelli di coerenza del Azure Cosmos DB quando si usa API Cassandra:  

[![Mapping del modello di coerenza Cassandra](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a id="mongo-mapping"></a>Mapping tra MongoDB e livelli di coerenza Azure Cosmos DB

A differenza Azure Cosmos DB, MongoDB nativo non fornisce garanzie di coerenza definite in modo accurato. Al contrario, MongoDB nativo consente agli utenti di configurare le garanzie di coerenza seguenti: un problema di scrittura, un problema di lettura e la direttiva Master per indirizzare le operazioni di lettura alle repliche primarie o secondarie per ottenere il livello di coerenza desiderato. 

Quando si usa l'API di Azure Cosmos DB per MongoDB, il Driver MongoDB considera l'area di scrittura come replica primaria e tutte le altre aree sono in lettura replica. È possibile scegliere l'area associata all'account Azure Cosmos come replica primaria. 

Quando si usa l'API di Azure Cosmos DB per MongoDB:

* Il problema di scrittura viene mappato al livello di coerenza predefinito configurato nell'account Azure Cosmos.
 
* Azure Cosmos DB eseguirà il mapping dinamico del problema di lettura specificato dal driver client MongoDB a uno dei livelli di coerenza Azure Cosmos DB configurati in modo dinamico in una richiesta di lettura. 

* È possibile aggiungere annotazioni a un'area specifica associata all'account Azure Cosmos come "Master" rendendo la regione come prima area scrivibile. 

La tabella seguente illustra come viene eseguito il mapping dei problemi di scrittura/lettura MongoDB nativi ai livelli di coerenza di Azure Cosmos quando si usa l'API di Azure Cosmos DB per MongoDB:

[![Mapping del modello di coerenza MongoDB](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

Leggere altre informazioni sui livelli di coerenza e la compatibilità tra le API di Azure Cosmos DB e le API open source. Vedere gli articoli seguenti:

* [Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza](consistency-levels-tradeoffs.md)
* [Funzionalità di MongoDB supportate dall'API Azure Cosmos DB per MongoDB](mongodb-feature-support.md)
* [Funzionalità di Apache Cassandra supportate dall'API Cassandra di Azure Cosmos DB](cassandra-support.md)