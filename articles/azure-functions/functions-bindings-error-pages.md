---
title: Linee guida per la gestione degli errori di Funzioni di Azure | Microsoft Docs
description: Indicazioni generali per la gestione degli errori che si verificano durante l'esecuzione delle funzioni e collegamenti agli argomenti sugli errori specifici.
services: functions
cloud: ''
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: glenga; cfowler
ms.openlocfilehash: 5a8dae73c164b319b4c291685deff402f9798364
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44091839"
---
# <a name="azure-functions-error-handling"></a>Gestione degli errori di Funzioni di Azure

Questo argomento offre indicazioni generali per la gestione degli errori che si verificano durante l'esecuzione delle funzioni. Sono inoltre disponibili collegamenti ad argomenti che descrivono gli errori di associazione specifici che possono verificarsi. 

## <a name="handing-errors-in-functions"></a>Gestione degli errori nelle funzioni
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>Codici degli errori di associazione

In caso di integrazione con i servizi di Azure, è possibile che vengano generati errori originati dalle API dei servizi sottostanti. I collegamenti alla documentazione sui codici di errore per questi servizi sono disponibili nella sezione **Eccezioni e codici restituiti** degli argomenti di riferimento su trigger e associazione seguenti:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Archiviazione BLOB](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Hub eventi](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Hub di notifica di Azure](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Archiviazione code](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Bus di servizio](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Archiviazione tabelle](functions-bindings-storage-table.md#exceptions-and-return-codes)
