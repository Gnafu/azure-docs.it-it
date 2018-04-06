---
title: SDK e risorse .NET dell'API di tabella di CosmosDB di Azure | Microsoft Docs
description: Informazioni sull'API Table di Azure Cosmos DB, incluse le date di rilascio, le date di ritiro e le modifiche apportate nelle diverse versioni.
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/26/2018
ms.author: mimig
ms.openlocfilehash: 2afd7df65e7b223845752fc6bea5bc0ab4d3efd8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>API .NET di tabella di CosmosDB di Azure: download e note sulla versione
> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [Java](table-sdk-java.md)
> * [Node.JS](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Download dell'SDK**|[NuGet](https://aka.ms/acdbtablenuget)|
|**Documentazione sull'API**|[Documentazione di riferimento API .NET](https://aka.ms/acdbtableapiref)|
|**Guida introduttiva**|[Azure Cosmos DB: Build an app with .NET and the Table API](create-table-dotnet.md) (Cosmos DB di Azure: compilare un'app con .NET e l'API di tabella)|
|**Esercitazione**|[Azure Cosmos DB: sviluppare con l'API Table in .NET](tutorial-develop-table-dotnet.md)|
|**Framework attualmente supportato**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> Se si è creato un account dell'API di tabella durante l'anteprima, creare un [nuovo account dell'API di tabella](create-table-dotnet.md#create-a-database-account) per usare gli SDK dell'API di tabella disponibili a livello generale.
>

## <a name="release-notes"></a>Note sulla versione

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* È stata aggiunta la convalida per ETag in formato non valido in modalità diretta.
* È stato corretto il bug relativo alle query LINQ in modalità gateway.
* Le API sincrone vengono ora eseguite sul pool di thread con SynchronizationContext.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Aggiunta di TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism e TableQueryContinuationTokenLimitInKb a TableRequestOptions
* Correzioni di bug

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Versione con disponibilità generale

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0-preview
* Versione di anteprima iniziale

## <a name="release-and-retirement-dates"></a>Date di rilascio e di ritiro
Microsoft invia una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata.

Il pacchetto di anteprima [WindowsAzure.Storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) è stato deprecato e sostituito dal pacchetto [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table). L'SDK WindowsAzure.Storage-PremiumTable verrà ritirato il 15 novembre 2018. Da quella data, le richieste all'SDK ritirato non saranno consentite.

Le nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunte solo all'SDK corrente. È quindi consigliabile eseguire sempre l'aggiornamento alla versione più recente dell'SDK quanto prima. 

Qualsiasi richiesta inviata ad Azure Cosmos DB con un SDK ritirato viene rifiutata dal servizio.
<br/>

| Versione | Data di rilascio | Data di ritiro |
| --- | --- | --- |
| [1.1.1](#1.1.1) |26 marzo 2018|--- |
| [1.1.0](#1.1.0) |21 febbraio 2018|--- |
| [1.0.0](#1.0.0) |15 novembre 2017|--- |
| [0.9.0-preview](#0.9.0-preview) |11 novembre 2017 |--- |

## <a name="troubleshooting"></a>risoluzione dei problemi

Se si ottiene l'errore 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

Quando si tenta di usare il pacchetto NuGet Microsoft.Azure.CosmosDB.Table, sono disponibili due opzioni per risolvere il problema:

* Usare la console di gestione pacchetti per installare il pacchetto Microsoft.Azure.CosmosDB.Table e le relative dipendenze. A tale scopo, digitare quanto segue nella console di gestione pacchetti per la soluzione. 
    ```
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```
    
* Usando lo strumento di gestione pacchetti Nuget preferito, installare il pacchetto Microsoft.Azure.Storage.Common Nuget prima di installare Microsoft.Azure.CosmosDB.Table.

## <a name="faq"></a>Domande frequenti

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Vedere anche 
Per altre informazioni sull'API Table di Azure Cosmos DB, vedere [Introduzione ad Azure Cosmos DB: API Table](table-introduction.md). 
