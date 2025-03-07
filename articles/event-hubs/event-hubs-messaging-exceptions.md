---
title: Eccezioni di messaggistica - Hub eventi di Azure| Microsoft Docs
description: Questo articolo fornisce un elenco delle eccezioni di messaggistica di Hub eventi di Azure e le relative azioni consigliate.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 7b6323e02225be3d954e4ee91ea06952bb3ce396
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66001773"
---
# <a name="event-hubs-messaging-exceptions"></a>Eccezioni della messaggistica di Hub eventi

Questo articolo elenca alcune eccezioni generate dalla raccolta di API di messaggistica del bus di servizio di Azure, che include le API di Hub eventi .NET Framework. Il documento è soggetto a modifica ed è quindi opportuno controllare periodicamente la presenza di aggiornamenti.

## <a name="exception-categories"></a>Categorie di eccezioni

Le API di Hub eventi generano eccezioni che possono essere raggruppate nelle categorie seguenti e a ognuna delle quali è associata un'azione che è possibile eseguire per tentare di risolverla.

1. Errore di codifica utente: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Azione generale: provare a correggere il codice prima di continuare.
2. Errore di installazione/configurazione: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Azione generale: controllare la configurazione e modificarla, se necessario.
3. Eccezioni temporanee: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Azione generale: ripetere l'operazione o inviare una notifica agli utenti.
4. Altre eccezioni: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Azione generale: specifica per il tipo di eccezione. Fare riferimento alla tabella nella sezione seguente. 

## <a name="exception-types"></a>Tipi di eccezioni
La tabella seguente elenca i tipi di eccezioni di messaggistica, ne riporta le possibili cause, e indica l'azione suggerita che è possibile eseguire.

| Tipo di eccezione | Descrizione/Causa/Esempi | Azione suggerita | Nota sulla ripetizione automatica/immediata |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Il server non ha risposto all'operazione richiesta entro l'intervallo di tempo specificato, controllato dalla proprietà [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Il server può aver completato l'operazione richiesta. Questa eccezione può verificarsi a causa di ritardi a livello di rete o di infrastruttura. |Controllare lo stato del sistema per verificarne la coerenza e, se necessario, ripetere l'operazione.<br /> Vedere [TimeoutException](#timeoutexception). | In alcuni casi può essere utile ripetere l'operazione; aggiungere al codice la logica di ripetizione dei tentativi. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |L'operazione richiesta dall'utente non è consentita nell'ambito del server o del servizio. Per informazioni dettagliate, vedere il messaggio di eccezione. Ad esempio, [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) genera questa eccezione se il messaggio è stato ricevuto in modalità [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) . | Controllare il codice e la documentazione. Assicurarsi che l'operazione richiesta sia valida. | Ripetere l'operazione non serve. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | È stato eseguito un tentativo di richiamare un'operazione su un oggetto già chiuso, interrotto o eliminato. In alcuni casi rari, la transazione di ambiente è già stata eliminata. | Controllare il codice e assicurarsi che non richiami operazioni su un oggetto eliminato. | Ripetere l'operazione non serve. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | L'oggetto [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) non è riuscito ad acquisire un token oppure il token non è valido o non contiene le attestazioni necessarie per eseguire l'operazione. | Assicurarsi che il provider di token sia stato creato con i valori corretti. Controllare la configurazione del servizio di controllo di accesso. | In alcuni casi può essere utile ripetere l'operazione; aggiungere al codice la logica di ripetizione dei tentativi. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | Uno o più argomenti forniti al metodo non sono validi. L'URI fornito a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) o [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) contiene segmenti di percorso. Lo schema URI fornito a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) o [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) non è valido. Il valore della proprietà è maggiore di 32 KB. | Controllare il codice chiamante e assicurarsi che gli argomenti siano corretti. | Ripetere l'operazione non serve. |
| [Microsoft.ServiceBus.Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft.Azure.EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | L'entità associata all'operazione non esiste o è stata eliminata. | Assicurarsi che l'entità esista. | Ripetere l'operazione non serve. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | Il client non riesce a stabilire una connessione con Hub eventi. |Assicurarsi che il nome host fornito sia corretto e l'host sia raggiungibile. | Se sono presenti problemi di connettività intermittente, può essere utile ripetere l'operazione. |
| [Microsoft.ServiceBus.Messaging ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft.Azure.EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | In questo momento il servizio non è in grado di elaborare la richiesta. | Il client può attendere per un certo periodo di tempo ed è quindi opportuno ripetere l'operazione. <br /> Vedere [ServerBusyException](#serverbusyexception). | Il client può riprovare dopo un determinato intervallo. Se viene generata un'eccezione diversa, controllare il comportamento di ripetizione del tentativo della nuova eccezione. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Eccezione di messaggistica generica che può essere generata nei casi seguenti: È stato eseguito un tentativo di creare una classe [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) usando un nome o un percorso appartenente a un tipo di entità diverso, ad esempio un argomento. È stato eseguito un tentativo di inviare un messaggio di dimensioni superiori a 1 MB. Si è verificato un errore nel server o nel servizio durante l'elaborazione della richiesta. Per informazioni dettagliate, vedere il messaggio di eccezione. Si tratta in genere di un'eccezione temporanea. | Controllare il codice e verificare che per il corpo del messaggio siano stati usati solo oggetti serializzabili (oppure usare un serializzatore personalizzato). Consultare la documentazione per identificare i tipi di valori delle proprietà supportati e usare solo quelli. Controllare la proprietà [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) . Se è **true**, è possibile ripetere l'operazione. | Il comportamento di ripetizione dei tentativi non è definito e ripetere l'operazione può non essere utile. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | È stato eseguito un tentativo di creare un'entità con un nome già usato da un'altra entità dello stesso spazio dei nomi del servizio. | Eliminare l'entità esistente o scegliere un nome diverso per l'entità da creare. | Ripetere l'operazione non serve. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | L'entità di messaggistica ha raggiunto le dimensioni massime consentite. Questa eccezione può verificarsi se a livello di gruppo di consumer è già stato aperto il numero massimo di ricevitori, ovvero 5. | Creare spazio nell'entità mediante la ricezione di messaggi dall'entità o dalle relative code secondarie. <br /> Vedere [QuotaExceededException](#quotaexceededexception) | Se nel frattempo sono stati rimossi i messaggi, può essere utile ripetere l'operazione. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | È stata inoltrata una richiesta per un'operazione di runtime su un'entità disattivata. |Attivare l'entità. | Se nel frattempo l'entità è stata attivata, può essere utile ripetere l'operazione. |
| [Microsoft.ServiceBus.Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft.Azure.EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Un payload del messaggio supera il limite di 1 MB. Il limite di 1 MB si riferisce all'intero messaggio, che può includere proprietà di sistema ed eventuali overhead .NET. | Ridurre le dimensioni del payload del messaggio e quindi ripetere l'operazione. |Ripetere l'operazione non serve. |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indica che è stata superata la quota di un'entità specifica.

Questa eccezione può verificarsi se a livello di gruppo di consumer è già stato aperto il numero massimo di ricevitori (5).

### <a name="event-hubs"></a>Hub eventi
Hub eventi ha un limite di 20 gruppi di utenti per Hub eventi. Quando si tenta di creare più gruppi, si riceve un'eccezione di tipo [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

## <a name="timeoutexception"></a>TimeoutException
Un'eccezione di tipo [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indica che un'operazione avviata dall'utente richiede più tempo rispetto al timeout dell'operazione. 

Per Hub eventi, il timeout è specificato come parte della stringa di connessione o tramite [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Il messaggio di errore stesso può variare, ma contiene sempre il valore di timeout specificato per l'operazione corrente. 

### <a name="common-causes"></a>Cause comuni
Per questo errore, esistono due cause comuni: una configurazione errata o un errore temporaneo del servizio.

1. **Configurazione errata** : il valore di timeout dell'operazione è troppo piccolo per la condizione operativa. Il valore predefinito per il timeout dell'operazione dell'SDK client è 60 secondi. Verificare se il codice contiene un valore troppo piccolo. Si noti che la condizione relativa all'utilizzo della rete e della CPU può influire sul tempo necessario per il completamento di una determinata operazione, pertanto il timeout dell'operazione non deve essere impostato su un valore basso.
2. **Errore temporaneo del servizio** : a volte il servizio di Hub eventi può subire ritardi nell'elaborazione delle richieste, ad esempio durante periodi di traffico elevato. In questi casi, è possibile ritentare l'operazione dopo un ritardo fino a quando l'operazione ha esito positivo. Se la stessa operazione continua ad avere esito negativo dopo diversi tentativi, visitare il [sito sullo stato dei servizi Azure](https://azure.microsoft.com/status/) per verificare se esistono casi noti di interruzioni del servizio.

## <a name="serverbusyexception"></a>ServerBusyException

[Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) o [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) indicano che il server è sovraccarico. Esistono due codici di errore relativi a questa eccezione.

### <a name="error-code-50002"></a>Codice di errore 50002

Questo errore può verificarsi per uno dei due motivi:

1. Il carico non viene distribuito uniformemente su tutte le partizioni nell'Hub eventi e una partizione raggiunge il limite dell'unità di velocità effettiva locale.
    
    Risoluzione: rivedere la strategia di distribuzione della partizione o provare con [EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient).

2. Lo spazio dei nomi di Hub eventi non dispone di unità di velocità effettiva sufficienti (per conferma, è possibile controllare la schermata **Metrica** nella finestra dello spazio dei nomi di Hub eventi nel [Portale di Azure](https://portal.azure.com)). Il portale mostra le informazioni aggregate (1 minuto), ma viene misurata la velocità effettiva in tempo reale, quindi si tratta solo di una stima.

    Risoluzione: aumentare le unità di velocità effettiva nello spazio dei nomi. È possibile eseguire questa operazione nel portale, nella finestra **Scalabilità** della schermata dello spazio dei nomi di Hub eventi. In alternativa, è possibile usare [Aumento automatico](event-hubs-auto-inflate.md).

### <a name="error-code-50001"></a>Codice errore 50001

Questo errore si verifica raramente. Si verifica quando il contenitore che esegue il codice per lo spazio dei nomi è insufficiente per la CPU, occorrono pochi secondi prima che il bilanciamento del carico dell'Hub eventi inizi.

### <a name="limit-on-calls-to-the-getruntimeinformation-method"></a>Limite per le chiamate al metodo GetRuntimeInformation
Hub eventi di Azure supporta fino a 50 chiamate al secondo per il GetRuntimeInfo al secondo. Una volta raggiunto il limite, si può ricevere un'eccezione simile a quello riportato di seguito:

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Panoramica di Hub eventi](event-hubs-what-is-event-hubs.md)
* [Create an Event Hub](event-hubs-create.md) (Creare un Hub eventi)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)
