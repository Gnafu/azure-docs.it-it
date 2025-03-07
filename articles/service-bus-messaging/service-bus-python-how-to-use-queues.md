---
title: Come usare le code del bus di servizio di Azure con Python | Microsoft Docs
description: Informazioni su come usare le code del bus di servizio da Python.
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 9bb53a8e68866e2ed346277171e2706f5907e8af
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141919"
---
# <a name="how-to-use-service-bus-queues-with-python"></a>Come usare le code del bus di servizio con Python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

In questa esercitazione si apprenderà come creare applicazioni Python per inviare e ricevere messaggi da una coda del bus di servizio. 

## <a name="prerequisites"></a>Prerequisiti
1. Una sottoscrizione di Azure. Per completare l'esercitazione, è necessario un account Azure. È possibile attivare i [benefici](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) per gli abbonati MSDN o iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Seguire i passaggi nell'articolo [usare portale di Azure per creare una coda del bus di servizio](service-bus-quickstart-portal.md) .
    1. Leggere la breve **Panoramica** delle **Code**del bus di servizio. 
    2. Creare uno **spazio dei nomi**del bus di servizio. 
    3. Ottenere la **stringa di connessione**. 

        > [!NOTE]
        > In questa esercitazione verrà creata una **coda** nello spazio dei nomi del bus di servizio usando Python. 
1. Installare Python o il [pacchetto del bus di servizio di Azure per Python][Python Azure Service Bus package], vedere la Guida all'installazione di [Python](/azure/python/python-sdk-azure-install). Vedere la documentazione completa di Python SDK di Service Bus [qui](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="create-a-queue"></a>Crea una coda
L'oggetto **ServiceBusClient** consente di usare le code. Aggiungere il seguente codice all'inizio di ogni file Python da cui si desidera accedere al bus di servizio a livello di codice:

```python
from azure.servicebus import ServiceBusClient
```

Il codice seguente crea un oggetto **ServiceBusClient** . Sostituire `<CONNECTION STRING>` con il ServiceBus ConnectionString.

```python
sb_client = ServiceBusClient.from_connection_string('<CONNECTION STRING>')
```

I valori per il nome e il valore della chiave di firma di accesso condiviso sono disponibili nelle informazioni di connessione [portale di Azure][Azure portal] o nel riquadro **Proprietà** di Visual Studio quando si seleziona lo spazio dei nomi del Bus di servizio in Esplora server (come illustrato nella sezione precedente).

```python
sb_client.create_queue("taskqueue")
```

Il metodo `create_queue` supporta anche opzioni aggiuntive che consentono di eseguire l'override delle impostazioni predefinite delle code, come ad esempio la durata (TTL) dei messaggi o la dimensione massima della coda. L'esempio seguente illustra come impostare la dimensione massima della coda su 5 GB e il valore TTL su 1 minuto:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

Per altre informazioni, vedere la documentazione di Python per il [bus di servizio di Azure](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="send-messages-to-a-queue"></a>Inviare messaggi a una coda
Per inviare un messaggio a una coda del bus di servizio, l'applicazione `send` chiama il metodo `ServiceBusClient` sull'oggetto.

L'esempio seguente illustra come inviare un messaggio di prova alla coda denominata `taskqueue` usando `send_queue_message`:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string(
    "<CONNECTION STRING>", "<QUEUE NAME>")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

Le code del bus di servizio supportano messaggi di dimensioni fino a 256 KB nel [livello Standard](service-bus-premium-messaging.md) e fino a 1 MB nel [livello Premium](service-bus-premium-messaging.md). Le dimensioni massime dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non possono superare 64 KB. Non esiste alcun limite al numero di messaggi mantenuti in una coda, mentre è prevista una limitazione alla dimensione totale dei messaggi di una coda. Questa dimensione della coda viene definita al momento della creazione, con un limite massimo di 5 GB. Per altre informazioni sulle quote, vedere [Quote del bus di servizio][Service Bus quotas].

Per altre informazioni, vedere la documentazione di Python per il [bus di servizio di Azure](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="receive-messages-from-a-queue"></a>Ricevere messaggi da una coda
I messaggi vengono ricevuti da una coda usando `get_receiver` il metodo `ServiceBusService` sull'oggetto:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string(
    "<CONNECTION STRING>", "<QUEUE NAME>")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

Per altre informazioni, vedere la documentazione di Python per il [bus di servizio di Azure](/python/api/overview/azure/servicebus?view=azure-python).


I messaggi vengono eliminati dalla coda non appena vengono letti, quando il parametro `peek_lock` è impostato su **False**. È possibile leggere e bloccare il messaggio senza eliminarlo dalla coda impostando il parametro `peek_lock` su **True**.

Il comportamento di lettura ed eliminazione del messaggio nell'ambito dell'operazione di ricezione costituisce il modello più semplice ed è adatto per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come utilizzato, quando l'applicazione viene riavviata e inizia a utilizzare nuovamente i messaggi, il messaggio utilizzato prima dell'arresto anomalo risulterà perso.

Se il parametro `peek_lock` è impostato su **True**, l'operazione di ricezione viene suddivisa in due fasi, in modo da consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio, o averlo archiviato in modo affidabile per una successiva elaborazione, l'applicazione esegue la seconda fase del processo di ricezione chiamando il metodo **delete** per l'oggetto **Message**. Il metodo **delete** contrassegna il messaggio come usato e lo rimuove dalla coda.

```python
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Come gestire arresti anomali e messaggi illeggibili dell'applicazione
Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevitore non è in grado di elaborare il messaggio per un qualsiasi motivo, può chiamare il metodo **unlock** per l'oggetto **Message**. In questo modo, il bus di servizio sbloccherà il messaggio nella coda rendendolo nuovamente disponibile per la ricezione da parte della stessa o da un'altra applicazione consumer.

Al messaggio bloccato nella coda è inoltre associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima della chiamata del metodo **delete**, il messaggio verrà nuovamente recapitato all'applicazione al riavvio. Questa operazione viene spesso definita **elaborazione at-least-once**, ovvero ogni messaggio verrà elaborato almeno una volta, ma in determinate situazioni lo stesso messaggio potrebbe essere recapitato nuovamente. Se lo scenario non tollera la doppia elaborazione, gli sviluppatori dovranno aggiungere logica aggiuntiva all'applicazione per gestire il secondo recapito del messaggio. A tale scopo viene spesso usata la proprietà **MessageId** del messaggio, che rimane costante in tutti i tentativi di recapito.

> [!NOTE]
> È possibile gestire le risorse del bus di servizio con [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer consente agli utenti di connettersi a uno spazio dei nomi del bus di servizio e di amministrare le entità di messaggistica in modo semplice. Lo strumento offre caratteristiche avanzate, tra cui funzionalità di importazione/esportazione o la possibilità di testare argomenti, code, sottoscrizioni, servizi di inoltro, hub di notifica e hub eventi. 

## <a name="next-steps"></a>Passaggi successivi
Dopo aver appreso le nozioni di base sulle code del bus di servizio, vedere gli articoli seguenti per altre informazioni.

* [Code, argomenti e sottoscrizioni del bus di servizio][Queues, topics, and subscriptions]

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md

