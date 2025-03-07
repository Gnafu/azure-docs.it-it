---
title: Reazione a eventi di Servizi multimediali di Azure | Microsoft Docs
description: Usare Griglia di eventi di Azure per sottoscrivere eventi di Servizi multimediali.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/08/2019
ms.author: juliako
ms.openlocfilehash: d8cb8fdebb5a7e4bcbc9f979c98085e90ebd4c68
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147165"
---
# <a name="handling-event-grid-events"></a>Gestione degli eventi di Griglia di eventi

Gli eventi di Servizi multimediali consentono alle applicazioni di reagire a numerosi eventi (ad esempio, l'evento di modifica dello stato del processo) tramite moderne architetture senza server e senza la necessità di usare codice complesso o servizi di polling costosi e inefficienti. Gli eventi vengono invece inviati attraverso [Griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/) ai gestori degli eventi, ad esempio [Funzioni di Azure](https://azure.microsoft.com/services/functions/), [App per la logica di Azure](https://azure.microsoft.com/services/logic-apps/) o anche al webhook in uso, con pagamento in base all'utilizzo. Per informazioni sui prezzi, vedere [Prezzi di Griglia di eventi](https://azure.microsoft.com/pricing/details/event-grid/).

La disponibilità degli eventi di Servizi multimediali è legata alla [disponibilità](../../event-grid/overview.md) di Griglia di eventi. Gli eventi saranno disponibili nelle aree geografiche in cui si renderà disponibile Griglia di eventi.  

## <a name="media-services-events-and-schemas"></a>Eventi e schemi di servizi multimediali

Griglia di eventi usa le [sottoscrizioni di eventi](../../event-grid/concepts.md#event-subscriptions) per instradare i messaggi di evento ai sottoscrittori. Gli eventi di Servizi multimediali contengono tutte le informazioni necessarie per rispondere alle modifiche dei dati. Un evento di Servizi multimediali è riconoscibile perché la proprietà eventType inizia con "Microsoft.Media".

Per altre informazioni, vedere [Schemi di eventi di Servizi multimediali](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Procedure consigliate per l'utilizzo di eventi

Per le applicazioni che gestiscono gli eventi di Servizi multimediali è consigliabile seguire alcune procedure:

* Dal momento che vi possono essere più sottoscrizioni configurate per instradare gli eventi allo stesso gestore eventi, è importante non presupporre che gli eventi derivino da un'origine specifica, ma controllare l'argomento del messaggio per assicurarsi che provengano dall'account di archiviazione previsto.
* Controllare anche che il tipo di evento che si verifica, eventType, sia un oggetto che si è preparati a elaborare e non presupporre che tutti gli eventi che si ricevono siano dei tipi previsti.
* Ignorare i campi che non si conoscono.  Questa procedura consentirà di rimanere flessibili alle nuove funzionalità che potrebbero essere aggiunte in futuro.
* Usare le corrispondenze di prefisso e suffisso "subject" per limitare gli eventi a un determinato evento.

> [!NOTE]
> Gli eventi sono soggetti alla Contratto di servizio di griglia di eventi [(SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/). Per ottenere le notifiche degli eventi usando le API, vedere esempi su come utilizzare gli eventi con [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/ContentProtection/BasicAESClearKey) o [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicAESClearKey).

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare gli eventi-portale](monitor-events-portal-how-to.md)
* [Monitorare gli eventi-interfaccia della riga di comando](job-state-events-cli-how-to.md)
