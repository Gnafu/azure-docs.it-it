---
title: Come configurare il clustering Redis per una Cache Redis di Azure Premium | Microsoft Docs
description: In questo articolo viene illustrato come creare e gestire il clustering Redis per le istanze di Cache Redis di Azure Premium.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 62208eec-52ae-4713-b077-62659fd844ab
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 06/13/2018
ms.author: yegu
ms.openlocfilehash: 602d77f3d4e8ed10c2c964462bc2dc21240cef5c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60541387"
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-cache-for-redis"></a>Come configurare il clustering Redis per una Cache Redis di Azure Premium
Cache Redis di Azure include diverse soluzioni cache che offrono flessibilità di scelta riguardo alle dimensioni e alle funzionalità della cache, tra cui le funzionalità del livello Premium come clustering, persistenza e supporto per reti virtuali. In questo articolo viene descritto come configurare il clustering in un'istanza di Cache Redis di Azure Premium.

Per informazioni su altre funzionalità della cache di livello Premium, vedere [Introduction to the Azure Cache for Redis Premium tier](cache-premium-tier-intro.md) (Introduzione al livello Premium di Cache Redis di Azure).

## <a name="what-is-redis-cluster"></a>Che cos'è un cluster Redis?
Cache Redis di Azure offre le stesse funzionalità di clustering Redis disponibili nell'[implementazione Redis](https://redis.io/topics/cluster-tutorial). Con un cluster Redis si ottengono i vantaggi seguenti: 

* Possibilità di suddividere automaticamente il set di dati tra più nodi. 
* Possibilità di continuare le operazioni quando si verificano errori in un subset dei nodi oppure quando un subset dei nodi non è in grado di comunicare con il resto del cluster. 
* Maggiore velocità effettiva: la velocità effettiva aumenta in modo lineare man mano che aumenta il numero di partizioni. 
* Dimensioni maggiori della memoria: l'incremento è lineare e segue l'aumento del numero di partizioni.  

Il clustering non aumenta il numero di connessioni disponibili per una cache in cluster. Per altre informazioni su dimensioni, velocità effettiva e larghezza di banda delle cache premium, vedere [What Azure Cache for Redis offering and size should I use?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) (Quale versione e dimensioni di Cache Redis di Azure è consigliabile usare?)

In Azure, il cluster Redis viene offerto nel modello primario/replica, in cui ogni partizione dispone di una coppia primario/di replica con la replica, in cui la replica viene gestita dal servizio Cache Redis di Azure. 

## <a name="clustering"></a>Clustering
Le funzionalità di clustering vengono abilitate nel pannello **New Azure Cache for Redis** (Nuova cache Redis di Azure) durante la creazione della cache. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Le funzionalità di clustering vengono configurate nel pannello **Cluster Redis** .

![Clustering][redis-cache-clustering]

È possibile includere fino a 10 partizioni nel cluster. Fare clic su **Abilitato** e far scorrere il dispositivo di scorrimento o digitare un numero compreso tra 1 e 10 per **Numero di partizioni**, quindi fare clic su **OK**.

Ogni partizione è una coppia di cache primaria/di replica gestita da Azure e la dimensione totale della cache viene calcolata moltiplicando il numero di partizioni per la dimensione della cache selezionata nel piano tariffario. 

![Clustering][redis-cache-clustering-selected]

Dopo aver creato la cache è possibile connettersi alla cache e usarla come una cache non in cluster e Redis distribuisce i dati tra le partizioni della cache. Se la diagnostica è [abilitata](cache-how-to-monitor.md#enable-cache-diagnostics), le metriche vengono acquisite separatamente per ogni partizione e possono essere [visualizzate](cache-how-to-monitor.md) nel pannello Azure Cache for Redis (Cache Redis di Azure). 

> [!NOTE]
> 
> Sono richieste alcune lievi modifiche all'applicazione client quando le funzionalità di clustering vengono configurate. Per altre informazioni, vedere [È necessario apportare modifiche all'applicazione client per usare il clustering?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

Per un codice di esempio sull'uso del clustering con il client StackExchange.Redis, vedere la porzione [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) dell'esempio [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>Modificare la dimensione del cluster in una cache premium in esecuzione
Per modificare le dimensioni di un cluster per una cache premium in esecuzione con clustering abilitato, fare clic su **Dimensioni cluster Redis** nel menu **Risorsa**.

> [!NOTE]
> Si noti che mentre il livello Premium di Cache Redis di Azure è stato rilasciato pubblicamente, la funzionalità Dimensioni cluster Redis è attualmente in anteprima.
> 
> 

![Dimensione del cluster Redis][redis-cache-redis-cluster-size]

Per modificare la dimensione del cluster, usare il dispositivo di scorrimento oppure digitare un numero compreso tra 1 e 10 nella casella di testo **Numero di partizioni** e fare clic su **OK** per salvare.

Aumentando la dimensione del cluster si aumentano la massima velocità effettiva e la dimensione massima della cache, ma non il numero massimo di connessioni disponibili per i client.

> [!NOTE]
> Se si ridimensiona un cluster, viene eseguito il comando [MIGRATE](https://redis.io/commands/migrate), che usa un elevato numero di risorse e di conseguenza, per ridurre l'impatto, si consiglia di eseguire questa operazione durante le ore non di punta. Durante il processo di migrazione, si noterà un picco di carico nel server. Il ridimensionamento di un cluster è un processo di lunga esecuzione e la quantità di tempo necessario dipende dal numero di chiavi e dalle dimensioni dei valori associati a tali chiavi.
> 
> 

## <a name="clustering-faq"></a>Domande frequenti sul clustering
L'elenco seguente include risposte a domande frequenti sul clustering di Cache Redis di Azure.

* [È necessario apportare modifiche all'applicazione client per usare il clustering?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Come vengono distribuite le chiavi in un cluster?](#how-are-keys-distributed-in-a-cluster)
* [Quali sono le dimensioni massime della cache che è possibile creare?](#what-is-the-largest-cache-size-i-can-create)
* [Tutti i client Redis supportano il clustering?](#do-all-redis-clients-support-clustering)
* [Come ci si connette alla cache quando il clustering è abilitato?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [È possibile connettersi direttamente a singole partizioni della cache?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [È possibile configurare il clustering per una cache creata in precedenza?](#can-i-configure-clustering-for-a-previously-created-cache)
* [È possibile configurare il clustering per una cache Basic o Standard?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [È possibile usare il clustering con il provider di stato della sessione ASP.NET Redis e il provider di cache di output?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [Usando StackExchange.Redis e il clustering si ottengono eccezioni MOVE. Cosa fare?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>È necessario apportare modifiche all'applicazione client per usare il clustering?
* Quando il clustering è abilitato, sarà disponibile solo il database 0. Se l'applicazione client usa più database e prova a leggere o scrivere in un database diverso da 0, verrà generata l'eccezione seguente. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->``StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  Per altre informazioni, vedere la [specifica sul cluster Redis: subset implementato](https://redis.io/topics/cluster-spec#implemented-subset).
* Se si usa [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/), è necessario usare la versione 1.0.481 o successiva. Connettersi alla cache usando gli stessi [endpoint, porte e chiavi](cache-configure.md#properties) usati per la connessione a una cache senza clustering abilitato. L'unica differenza consiste nel fatto che tutte le operazioni di lettura e scrittura devono essere eseguite nel database 0.
  
  * Altri client possono avere requisiti diversi. Vedere [Tutti i client Redis supportano il clustering?](#do-all-redis-clients-support-clustering)
* Se l'applicazione usa più operazioni chiave raggruppate in un singolo comando, tutte le chiavi devono trovarsi nella stessa partizione. Per individuare le chiavi nella stessa partizione, vedere [Come vengono distribuite le chiavi in un cluster?](#how-are-keys-distributed-in-a-cluster)
* Se si usa il provider di stato della sessione ASP.NET Redis, è necessario usare la versione 2.0.1 o successiva. Vedere [È possibile usare il clustering con il provider di stato della sessione ASP.NET Redis e i provider di output caching?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Come vengono distribuite le chiavi in un cluster?
In base alla documentazione relativa al [modello di distribuzione delle chiavi](https://redis.io/topics/cluster-spec#keys-distribution-model) di Redis: lo spazio delle chiavi è suddiviso in 16384 slot. Viene eseguito l'hashing di ogni chiave e le chiavi vengono assegnate a uno di questi slot, che vengono distribuiti in tutti i nodi del cluster. È possibile configurare la parte della chiave sottoposta a hashing, per assicurare che più chiavi vengano inserite nella stessa partizione mediante i tag hash.

* Chiavi con tag hash: se una parte della chiave è racchiusa tra `{` e `}`, solo tale parte della chiave verrà sottoposta a hashing allo scopo di determinare lo slot hash di una chiave. Ad esempio, le 3 chiavi seguenti, `{key}1`, `{key}2` e `{key}3`, si troveranno nella stessa partizione poiché solo la parte `key` del nome viene sottoposta a hashing. Per un elenco completo di specifiche dei tag hash per le chiavi, vedere la pagina relativa ai [tag hash per le chiavi](https://redis.io/topics/cluster-spec#keys-hash-tags).
* Chiavi senza tag hash: l'intero nome della chiave viene usato per l'hashing. Si otterrà una distribuzione statisticamente uniforme nelle partizioni della cache.

Per prestazioni e velocità effettiva ottimali, è consigliabile distribuire le chiavi in modo uniforme. Se si usano chiavi con un tag hash, l'applicazione dovrà assicurare che le chiavi vengano distribuite in modo uniforme.

Per altre informazioni, vedere le pagine relative al [modello di distribuzione delle chiavi](https://redis.io/topics/cluster-spec#keys-distribution-model), al [partizionamento orizzontale del cluster Redis](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding) e ai [tag hash per le chiavi](https://redis.io/topics/cluster-spec#keys-hash-tags).

Per un codice di esempio sull'uso del clustering e sul posizionamento delle chiavi nella stessa partizione con il client StackExchange.Redis, vedere la porzione [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) dell'esempio [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Quali sono le dimensioni massime della cache che è possibile creare?
Le dimensioni massime per la cache Premium sono 53 GB. È possibile creare fino a 10 partizioni, per una dimensione massima di 530 GB. Se è necessaria una dimensione maggiore, è possibile [farne richiesta](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Per altre informazioni, vedere [Azure Cache for Redis Pricing](https://azure.microsoft.com/pricing/details/cache/) (Prezzi di Cache Redis di Azure).

### <a name="do-all-redis-clients-support-clustering"></a>Tutti i client Redis supportano il clustering?
Non tutti i client supportano attualmente il clustering Redis. StackExchange.Redis è uno dei client che supporta questa funzionalità. Per altre informazioni su altri client, vedere la sezione dedicata alle [prove con il cluster](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) nell'[esercitazione per il cluster Redis](https://redis.io/topics/cluster-tutorial). 

Il protocollo di clustering Redis richiede che ogni client si connetta a ogni partizione direttamente in modalità di clustering. Tentare di usare un client che non supporta il clustering determinerà probabilmente numerose [eccezioni di reindirizzamento MOVED](https://redis.io/topics/cluster-spec#moved-redirection).

> [!NOTE]
> Se si usa StackExchange.Redis come client, assicurarsi di usare la versione più recente di [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 o versione successiva per il corretto funzionamento del clustering. Per altre informazioni in caso di problemi con le eccezioni MOVE, vedere la sezione relativa alle [eccezioni MOVE](#move-exceptions) .
> 
> 

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Come ci si connette alla cache quando il clustering è abilitato?
È possibile connettersi alla cache con gli stessi [endpoint](cache-configure.md#properties), [porte](cache-configure.md#properties) e [chiavi](cache-configure.md#access-keys) usati per la connessione a una cache senza clustering abilitato. Redis gestisce il clustering sul back-end in modo che non sia necessario gestirlo dal client.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>È possibile connettersi direttamente a singole partizioni della cache?
Il protocollo di clustering richiede che il client stabilisca connessioni di partizione corrette. Pertanto il client deve eseguire questa operazione correttamente per l'utente. Ciò premesso, ogni partizione è costituita da una coppia di cache primaria/di replica nota complessivamente come un'istanza della cache. È possibile connettersi tramite l'utilità cli redis in queste istanze di cache nel ramo [instabile](https://redis.io/download) del repository Redis in GitHub. Questa versione implementa il supporto di base quando avviata con il passaggio `-c` . Per altre informazioni, vedere [Playing with the cluster](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) (Usare il cluster) all'indirizzo [https://redis.io](https://redis.io) nell'[esercitazione sul cluster Redis](https://redis.io/topics/cluster-tutorial).

Per non-ssl usare i comandi seguenti.

    Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
    Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
    Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
    ...
    Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

Per ssl, sostituire `1300N` con `1500N`.

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>È possibile configurare il clustering per una cache creata in precedenza?
Al momento è possibile solo abilitare il clustering durante la creazione di una cache. Dopo aver creato la cache, è possibile modificarne la dimensione, ma non è possibile aggiungere il clustering a una cache premium o rimuoverlo da una cache. Una cache premium con il clustering abilitato e una sola partizione è diversa da una cache premium delle stesse dimensioni senza cluster abilitato.

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>È possibile configurare il clustering per una cache Basic o Standard?
Il clustering è disponibile esclusivamente per le cache Premium.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>È possibile usare il clustering con il provider di stato della sessione ASP.NET Redis e il provider di cache di output?
* **Provider di cache di output Redis** : nessuna modifica necessaria.
* **Provider di stato della sessione Redis**: per usare il clustering, è necessario usare [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 o versione successiva. In caso contrario, verrà generata un'eccezione. Si tratta di una modifica significativa. Per altre informazioni, vedere la pagina relativa ai [dettagli delle modifiche significative della versione 2.0.0](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Usando StackExchange.Redis e il clustering si ottengono eccezioni MOVE. Cosa fare?
Se si usa StackExchange.Redis e si ricevono eccezioni `MOVE` quando si usa il clustering, assicurarsi di usare [StackExchange.Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) o versioni successive. Per le istruzioni di configurazione delle applicazioni .NET per l'uso di StackExchange.Redis, vedere l'articolo sulla [configurazione dei client della cache](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come usare altre funzionalità di cache premium.

* [Introduzione al livello Premium di Cache Redis di Azure](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png







