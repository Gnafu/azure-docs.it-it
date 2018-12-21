---
title: App Node.js e Angular con l'API MongoB (parte 1)
titleSuffix: Azure Cosmos DB
description: Informazioni su come creare un'app MongoDB con Angular e Node in Azure Cosmos DB mediante le stesse API usate per MongoDB con questa serie di esercitazioni basate su video.
author: johnpapa
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: jopapa
ms.custom: seodec18
ms.openlocfilehash: ea69e4c8ba74dc685dd0b0fb7ac61cc6d061a26f
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53137651"
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db"></a>Creare un'app MongoDB con Angular e Azure Cosmos DB 

Questa esercitazione in più parti illustra come creare una nuova app per le [API MongoDB](mongodb-introduction.md) con Express, Angular e Node.js (lo stack MEAN) e quindi connetterla al database di Azure Cosmos DB. Poiché Azure Cosmos DB supporta le connessioni client MongoDB, è possibile usare Azure Cosmos DB invece di MongoDB, usando però lo stesso codice usato per le app MongoDB, ma con più vantaggi. I vantaggi offerti da Azure Cosmos DB includono una distribuzione nel cloud, ridimensionamento, sicurezza, dati replicati a livello globale, supporto multimodello e operazioni di lettura e scrittura estremamente rapide. 

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. Consente di creare ed eseguire rapidamente query su database di documenti, coppie chiave/valore e grafi, sfruttando i vantaggi offerti dalle funzionalità di scalabilità orizzontale e distribuzione globale alla base di Azure Cosmos DB. 

Questa esercitazione in più parti illustra le attività seguenti:

> [!div class="checklist"]
> * [Creare un'app Node.js Express con l'interfaccia della riga di comando di Angular](tutorial-develop-mongodb-nodejs-part2.md)
> * [Compilare l'interfaccia utente con Angular](tutorial-develop-mongodb-nodejs-part3.md)
> * [Creare un account Azure Cosmos DB mediante l'interfaccia della riga di comando di Azure](tutorial-develop-mongodb-nodejs-part4.md) 
> * [Usare Mongoose per connettersi ad Azure Cosmos DB](tutorial-develop-mongodb-nodejs-part5.md)
> * [Aggiungere le funzioni Post, Put e Delete all'app](tutorial-develop-mongodb-nodejs-part6.md)

Per compilare questa stessa app con React, vedere la [serie di video di esercitazione su React](tutorial-develop-mongodb-react.md).

## <a name="video-walkthrough"></a>Procedura dettagliata video

> [!VIDEO https://www.youtube.com/embed/vlZRP0mDabM]

## <a name="finished-project"></a>Progetto finito 

Questa esercitazione illustra in modo dettagliato la procedura per la creazione dell'applicazione. Se si vuole scaricare il progetto finito, è possibile ottenere l'applicazione completa dal [repository angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) in GitHub.

## <a name="next-steps"></a>Passaggi successivi

In questa parte dell'esercitazione sono state eseguite le operazioni seguenti:

> [!div class="checklist"]
> * È stata visualizzata una panoramica dei passaggi necessari per creare un'app MEAN.js con Azure Cosmos DB. 

È possibile passare alla parte successiva dell'esercitazione per creare l'app Node.js Express.

> [!div class="nextstepaction"]
> [Creare un'app Node.js Express con l'interfaccia della riga di comando di Angular](tutorial-develop-mongodb-nodejs-part2.md)
