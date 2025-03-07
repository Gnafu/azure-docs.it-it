---
title: Inoltro automatico di entità di messaggistica del bus di servizio di Azure | Documentazione Microsoft
description: Come concatenare una coda del bus di servizio o una sottoscrizione a un'altra coda o argomento.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f7060778-3421-402c-97c7-735dbf6a61e8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 1d7b76a58a427b687d0dc36d13cfc00f32196853
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390142"
---
# <a name="chaining-service-bus-entities-with-autoforwarding"></a>Concatenamento di entità del bus di servizio con l'inoltro automatico

La funzionalità di *inoltro automatico* del bus di servizio consente di concatenare una coda o una sottoscrizione a un'altra coda o a un altro argomento che fa parte dello stesso spazio dei nomi. Quando l'inoltro automatico è abilitato, il bus di servizio rimuove automaticamente i messaggi presenti nella prima coda o sottoscrizione (origine) e li inserisce nella seconda coda o argomento (destinazione). È comunque possibile inviare un messaggio direttamente all'entità di destinazione.

## <a name="using-autoforwarding"></a>Uso dell'inoltro automatico

È possibile abilitare l'autoinoltring impostando le proprietà [QueueDescription. ForwardTo][QueueDescription.ForwardTo] o [SubscriptionDescription. ForwardTo][SubscriptionDescription.ForwardTo] sugli oggetti [QueueDescription][QueueDescription] o [SubscriptionDescription][SubscriptionDescription] per l'origine, come nel esempio seguente:

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

L'entità di destinazione deve essere presente al momento della creazione dell'entità di origine. In caso contrario, il bus di servizio restituisce un'eccezione quando gli viene chiesto di creare l'entità di origine.

È possibile usare l'inoltro automatico per aumentare il numero di istanze di un singolo argomento. Il bus di servizio limita il [numero delle sottoscrizioni per un determinato argomento](service-bus-quotas.md) a 2000. Creando argomenti di secondo livello, è possibile aggiungere altre sottoscrizioni. Pur non essendo vincolati dalla limitazione del bus di servizio relativa al numero delle sottoscrizioni, l'aggiunta di un secondo livello di argomenti consente complessivamente di migliorare la velocità effettiva dell'argomento.

![Scenario di inoltro automatico][0]

È possibile usare l'inoltro automatico anche per disaccoppiare i mittenti dei messaggi dai destinatari. Si consideri ad esempio un sistema ERP costituito da tre moduli: elaborazione degli ordini, gestione del magazzino e gestione dei rapporti con i clienti. Ognuno di questi moduli genera messaggi che vengono accodati in un argomento corrispondente. Due dei rappresentanti di vendita sono interessati a tutti i messaggi correlati ai propri clienti. Per ricevere questi messaggi, i due rappresentanti creano una coda personale e una sottoscrizione per ognuno degli argomenti ERP che inoltrano automaticamente tutti i messaggi alla rispettiva coda.

![Scenario di inoltro automatico][1]

Se uno dei rappresentanti si assenta, viene riempita la coda personale, ma non l'argomento ERP. In questo scenario, poiché un rappresentante di vendita non ha ricevuto alcun messaggio, nessuno degli argomenti ERP raggiunge la quota.

> [!NOTE]
> Quando si configura l'autoinoltring, il valore di AutoDeleteOnIdle **sia per l'origine che per la destinazione** viene impostato automaticamente sul valore massimo del tipo di dati.
> 
>   - Sul lato di origine, l'autoinoltring funge da operazione di ricezione. Pertanto, l'origine che ha l'installazione dell'autoinoltring non è mai effettivamente "inattiva".
>   - Sul lato destinazione, questa operazione viene eseguita per garantire che sia sempre disponibile una destinazione a cui inviare il messaggio.

## <a name="autoforwarding-considerations"></a>Considerazioni sull'inoltro automatico

Se l'entità di destinazione accumula troppi messaggi e supera la quota oppure è disabilitata, l'entità di origine aggiunge i messaggi alla relativa [coda dei messaggi non recapitabili](service-bus-dead-letter-queues.md) finché non si libera spazio nella destinazione o l'entità non viene riabilitata. I messaggi restano nella coda dei messaggi non recapitabili, pertanto sarà necessario riceverli ed elaborarli in modo esplicito da tale coda.

Se si concatenano singoli argomenti per ottenere un argomento composito con diverse sottoscrizioni, è consigliabile avere un certo numero di sottoscrizioni nell'argomento di primo livello e un numero più elevato di sottoscrizioni negli argomenti di secondo livello. Ad esempio, un argomento di primo livello con 20 sottoscrizioni, ognuna delle quali concatenata a un argomento di secondo livello con 200 sottoscrizioni, consente una velocità effettiva più alta rispetto a un argomento di primo livello con 200 sottoscrizioni, ognuna delle quali concatenata a un argomento di secondo livello con 20 sottoscrizioni.

Il bus di servizio addebita un'operazione per ogni messaggio inoltrato. Ad esempio, se viene inviato un messaggio a un argomento con 20 sottoscrizioni, ognuna delle quali configurata per l'inoltro automatico dei messaggi a una coda o un argomento differente, vengono addebitate 21 operazioni se tutte le sottoscrizioni di primo livello ricevono una copia del messaggio.

Per creare una sottoscrizione concatenata a una coda o a un argomento diverso, l'autore deve avere le autorizzazioni di **gestione** per l'entità di origine e per quella di destinazione. Per l'invio di messaggi solo all'argomento di origine sono necessarie le autorizzazioni di **invio** per l'argomento di origine.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate sull'inoltro automatico, vedere gli argomenti di riferimento seguenti:

* [ForwardTo][QueueDescription.ForwardTo]
* [QueueDescription][QueueDescription]
* [SubscriptionDescription][SubscriptionDescription]

Per altre informazioni sui miglioramenti delle prestazioni del bus di servizio, vedere 

* [Procedure consigliate per il miglioramento delle prestazioni tramite la messaggistica del bus di servizio](service-bus-performance-improvements.md)
* [Entità di messaggistica partizionate][Partitioned messaging entities].

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
