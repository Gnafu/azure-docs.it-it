---
title: Ripristino di emergenza geografico per il bus di servizio di Azure | Microsoft Docs
description: Come usare le aree geografiche per il failover ed eseguire il ripristino di emergenza nel bus di servizio di Azure
services: service-bus-messaging
documentationcenter: ''
author: christianwolf42
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: sethm
ms.openlocfilehash: 652adcf78add8ae699a7f827a915e90ce1694c61
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
ms.locfileid: "30237346"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Ripristino di emergenza geografico per il bus di servizio di Azure

In caso di tempo di inattività di interi data center o aree di Azure (se non vengono usate [zone di disponibilità](../availability-zones/az-overview.md)), è essenziale che l'elaborazione dei dati continui in un'area o in un data center diverso. Il *ripristino di emergenza geografico* e la *replica geografica* sono quindi funzionalità importanti per qualsiasi azienda. Il bus di servizio di Azure supporta il ripristino di emergenza geografico e la replica geografica a livello di spazio dei nomi. 

La funzionalità di ripristino di emergenza geografico è disponibile a livello globale per lo SKU Premium del bus di servizio. 

## <a name="outages-and-disasters"></a>Emergenze e interruzioni

È importante notare la distinzione tra "interruzioni" ed "emergenze". Un'*interruzione* è l'indisponibilità temporanea del bus di servizio di Azure e può interessare alcuni componenti del servizio, ad esempio un archivio di messaggistica, oppure l'intero data center. Dopo la risoluzione del problema, tuttavia, il bus di servizio torna di nuovo disponibile. In genere, un'interruzione non determina la perdita di messaggi o di altri dati. Un'interruzione può essere provocata ad esempio da un'interruzione dell'alimentazione nel data center. Alcune interruzioni sono solo brevi perdite di connessione dovute a problemi di rete o temporanei. 

Il termine *emergenza* indica la perdita permanente o a lungo termine di un cluster del bus di servizio, un'area di Azure o un data center. L'area o il data center potrebbe o meno tornare di nuovo disponibile oppure potrebbe rimanere inattivo per ore o giorni. Un'emergenza può essere causata, ad esempio, da un incendio, un'inondazione o un terremoto. Una situazione di emergenza che diventa permanente potrebbe causare la perdita di alcuni messaggi, eventi o altri dati. Tuttavia, nella maggior parte dei casi non dovrebbe esserci perdita di dati e i messaggi possono essere ripristinati dopo aver eseguito il backup del data center.

La funzionalità di ripristino di emergenza geografico del bus di servizio di Azure è una soluzione di ripristino di emergenza. I concetti e il flusso di lavoro illustrati in questo articolo sono applicabili a scenari di emergenza, non a interruzioni temporanee. Per una descrizione dettagliata del ripristino di emergenza in Microsoft Azure, vedere [questo articolo](/azure/architecture/resiliency/disaster-recovery-azure-applications).   

## <a name="basic-concepts-and-terms"></a>Concetti e terminologia di base

La funzionalità di ripristino di emergenza implementa il ripristino di emergenza dei metadati e si basa sugli spazi dei nomi primari e secondari di ripristino di emergenza. Si noti che la funzionalità di ripristino di emergenza geografico è disponibile solo per lo [SKU Premium](service-bus-premium-messaging.md). Non è necessario apportare modifiche alla stringa di connessione, perché la connessione viene effettuata tramite un alias.

In questo articolo viene usata la terminologia seguente:

-  *Alias*: nome per una configurazione di ripristino di emergenza impostata. L'alias fornisce una singola stringa di connessione FQDN (nome di dominio completo) stabile. Le applicazioni usano questa stringa di connessione alias per connettersi a uno spazio dei nomi. 

-  *Spazio dei nomi primario/secondario*: spazi dei nomi corrispondenti all'alias. Lo spazio dei nomi primario è "attivo" e riceve i messaggi (può essere uno spazio dei nomi esistente o nuovo). Lo spazio dei nomi secondario è "passivo" e non riceve i messaggi. I metadati vengono sincronizzati tra entrambi gli spazi dei nomi, quindi entrambi possono facilmente accettare messaggi senza modifiche al codice dell'applicazione o alla stringa di connessione. Per fare in modo che solo lo spazio dei nomi attivo riceva i messaggi, è necessario usare l'alias. 

-  *Metadati*: entità come code, argomenti e sottoscrizioni e le relative proprietà del servizio associate allo spazio dei nomi. Si noti che solo le entità e le relative impostazioni vengono replicate automaticamente. I messaggi non vengono replicati. 

-  *Failover*: processo di attivazione dello spazio dei nomi secondario.

## <a name="setup-and-failover-flow"></a>Configurazione e flusso del failover

La sezione seguente è una panoramica del processo di failover e illustra come configurare il failover iniziale. 

![1][]

### <a name="setup"></a>Configurazione

È prima di tutto necessario creare uno spazio dei nomi primario o usarne uno esistente e creare un nuovo spazio dei nomi secondario, quindi associare i due spazi dei nomi. L'associazione fornisce un alias che può essere usato per la connessione. Poiché si usa un alias, non è necessario modificare le stringhe di connessione. È possibile aggiungere solo nuovi spazi dei nomi all'associazione di failover. Infine, è necessario aggiungere funzionalità di monitoraggio per rilevare i casi in cui è necessario un failover. Nella maggior parte dei casi, il servizio fa parte di un ecosistema di grandi dimensioni, quindi i failover automatici sono raramente possibili, in quanto molto spesso i failover devono essere eseguiti in sincronizzazione con il sottosistema o l'infrastruttura rimanente.

### <a name="example"></a>Esempio

In un esempio di questo scenario, si consideri una soluzione POS che genera messaggi o eventi. Il bus di servizio passa gli eventi a una soluzione di mapping o riformattazione, che quindi inoltra i dati mappati a un altro sistema per un'ulteriore elaborazione. A questo punto, tutti questi sistemi possono essere ospitati nella stessa area di Azure. La decisione relativa a quando eseguire il failover o di quale parte del sistema dipende dal flusso di dati nell'infrastruttura. 

È possibile automatizzare il failover con sistemi di monitoraggio o con soluzioni di monitoraggio personalizzate. Tale automazione, tuttavia, richiede pianificazione e lavoro aggiuntivi che esulano dall'ambito di questo articolo.

### <a name="failover-flow"></a>Flusso del failover

Se si avvia il failover, sono necessari due passaggi:

1. È necessario potere eseguire di nuovo il failover nel caso in cui si verifichi un'altra interruzione. Configurare quindi un altro spazio dei nomi passivo e aggiornare l'associazione. 

2. Eseguire il pull dei messaggi dallo spazio dei nomi primario precedente quando è di nuovo disponibile. Successivamente, usare tale spazio dei nomi per la messaggistica regolare di fuori della configurazione del ripristino geografico oppure eliminare lo spazio dei nomi primario precedente.

> [!NOTE]
> È supportata solo la semantica di inoltro in caso di errore. In questo scenario, si esegue il failover e quindi si esegue di nuovo l'associazione con un nuovo spazio dei nomi. Il failback, ad esempio in un cluster SQL, non è supportato. 

![2][]

## <a name="management"></a>Gestione

Se si commette un errore, ad esempio associando le aree non corrette durante la configurazione iniziale, è possibile interrompere l'associazione dei due spazi dei nomi in qualsiasi momento. Per usare gli spazi dei nomi associati come normali spazi dei nomi, eliminare l'alias.

## <a name="use-existing-namespace-as-alias"></a>Usare lo spazio dei nomi esistente come alias

Se si ha uno scenario in cui non è possibile modificare le connessioni di producer e consumer, è possibile riutilizzare il nome dello spazio dei nomi come nome dell'alias. Vedere il [codice di esempio su GitHub qui](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>Esempi

Gli [esempi su GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) mostrano come configurare e avviare un failover. Questi esempi illustrano i concetti seguenti:

- Un esempio .NET e le impostazioni necessarie in Azure Active Directory per usare Azure Resource Manager con il bus di servizio per configurare e abilitare il ripristino di emergenza geografico.
- I passaggi necessari per eseguire il codice di esempio.
- La modalità d'uso di uno spazio dei nomi esistente come alias.
- I passaggi per abilitare alternativamente il ripristino di emergenza geografico tramite PowerShell o l'interfaccia della riga di comando.
- [L'invio e la ricezione](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) dallo spazio dei nomi primario o secondario corrente tramite l'alias.

## <a name="considerations"></a>Considerazioni

Tenere presente le considerazioni seguenti per questa versione:

1. Quando si pianifica il failover, è consigliabile considerare anche il fattore tempo. Ad esempio, se si perde la connettività per più di 15-20 minuti, è possibile decidere di avviare il failover. 
 
2. Il fatto che non vengano replicati dati significa che le sessioni attive non vengono replicate. Il rilevamento dei duplicati e i messaggi pianificati potrebbero inoltre non funzionare. Le nuove sessioni, i nuovi messaggi pianificati e i nuovi duplicati funzioneranno. 

3. È necessario [provare a effettuare](/azure/architecture/resiliency/disaster-recovery-azure-applications#disaster-simulation) il failover di un'infrastruttura distribuita complessa almeno una volta. 

4. La sincronizzazione delle entità può richiedere tempo, circa un minuto per 50-100 entità. Anche le sottoscrizioni e le regole contano come entità. 

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni di riferimento sull'API REST per il ripristino di emergenza geografico, vedere [qui](/rest/api/servicebus/disasterrecoveryconfigs).
- Eseguire l'[esempio di GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2) sul ripristino di emergenza geografico.
- Vedere l'[esempio che invia messaggi a un alias](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) per il ripristino di emergenza geografico.

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli articoli seguenti:

* [Dati fondamentali del bus di servizio](service-bus-fundamentals-hybrid-solutions.md)
* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [API REST](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geo1.png
[2]: ./media/service-bus-geo-dr/geo2.png
