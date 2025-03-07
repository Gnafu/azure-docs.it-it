---
title: Gestione dello stato di Reliable Actors | Documentazione Microsoft
description: Descrive come viene gestito lo stato di Reliable Actors, reso persistente e replicato per la disponibilità elevata.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 65dd47ab21ca4b1c50e0f17b73e7bc4eae8a96e8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60725738"
---
# <a name="reliable-actors-state-management"></a>Gestione dello stato di Reliable Actors
I Reliable Actors sono oggetti a thread singolo che possono incapsulare sia la logica che lo stato. Poiché gli attori vengono eseguiti nei servizi Reliable Services, possono mantenere lo stato in modo affidabile con gli stessi meccanismi di persistenza e replica. In questo modo, gli attori non perdono il proprio stato dopo gli errori, dopo la riattivazione in seguito a un'operazione di garbage collection o quando vengono spostati tra i nodi di un cluster a causa del bilanciamento delle risorse o degli aggiornamenti.

## <a name="state-persistence-and-replication"></a>Replica e persistenza dello stato
Tutti i Reliable Actors vengono considerati *con stato* perché ogni istanza dell'attore è mappata a un ID univoco. Ciò significa che le chiamate ripetute allo stesso ID attore vengono instradate alla stessa istanza dell'attore. In un sistema senza stato invece le chiamate client non vengono instradate ogni volta necessariamente allo stesso server. Per questo motivo, i servizi Actor sono sempre servizi con stato.

Anche se gli attori sono considerati con stato, non significa che devono archiviare lo stato in modo affidabile. Gli attori possono scegliere il livello di replica e persistenza dello stato in base ai requisiti di archiviazione dei dati:

* **Uno stato persistente**: Lo stato è persistente nel disco e viene replicato per tre o più repliche. Questa è l'opzione di archiviazione dello stato più durevole, in cui lo stato può persistere anche in caso di interruzione di un cluster completo.
* **Stato volatile**: Lo stato viene replicato in almeno tre repliche e viene mantenuto solo in memoria. Fornisce la resilienza in caso di errore del nodo, di errore dell'attore e durante gli aggiornamenti e il bilanciamento delle risorse. Lo stato tuttavia non è persistente nel disco. Perciò, se vengono perse contemporaneamente tutte le repliche, si perderà anche lo stato.
* **Stato non persistente**: Stato non è replicato o scritto su disco, usare solo per gli attori che non devono mantenere lo stato affidabile.

Ogni livello di persistenza è semplicemente un altro *provider di stato* e un'altra configurazione di *replica* del servizio. La scrittura su disco dello stato dipende dal provider di stato, ovvero il componente di un servizio Reliable Services che archivia lo stato. La replica dipende dal numero di repliche con cui viene distribuito un servizio. In modo analogo ai servizi Reliable Services, il provider di stato e il numero di repliche possono essere impostati manualmente con facilità. Il framework per gli attori fornisce un attributo che, quando viene usato in un attore, seleziona automaticamente un provider di stato predefinito e genera automaticamente le impostazioni per il numero di repliche in modo da ottenere una di queste tre impostazioni di persistenza. L'attributo StatePersistence non viene ereditato dalla classe derivata e ogni tipo di attore deve specificare il proprio livello StatePersistence.

### <a name="persisted-state"></a>Stato persistente
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl  extends FabricActor implements MyActor
{
}
```  
Questa impostazione usa un provider di stato che archivia i dati su disco e imposta automaticamente il numero di repliche del servizio su 3.

### <a name="volatile-state"></a>Stato volatile
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Volatile)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Questa impostazione usa un provider di stato solo in memoria e imposta il numero di repliche su 3.

### <a name="no-persisted-state"></a>Stato non persistente
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.None)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Questa impostazione usa un provider di stato solo in memoria e imposta il numero di repliche su 1.

### <a name="defaults-and-generated-settings"></a>Impostazioni predefinite e impostazioni generate
Quando si usa l'attributo `StatePersistence`, viene selezionato automaticamente un provider di stato in fase di esecuzione all'avvio del servizio attore. Il numero di repliche, tuttavia, viene impostato in fase di compilazione dagli strumenti di compilazione dell'attore di Visual Studio. Gli strumenti di compilazione generano automaticamente un *servizio predefinito* per il servizio attore in ApplicationManifest.xml. I parametri vengono creati per le **dimensioni minime del set di repliche** e le **dimensioni del set di repliche di destinazione**.

È possibile cambiare questi parametri manualmente. Tuttavia, ogni volta che l'attributo `StatePersistence` viene modificato, i parametri vengono impostati sui valori predefiniti delle dimensioni del set di repliche per l'attributo `StatePersistence` selezionato, eseguendo l'override di eventuali valori precedenti. In altri termini, l'override dei valori impostati in ServiceManifest.xml viene eseguito *solo* in fase di compilazione quando si modifica l'attributo `StatePersistence`.

```xml
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="3" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## <a name="state-manager"></a>State Manager
Ogni istanza dell'attore ha un proprio gestore di stato: una struttura di dati simile a un dizionario che archivia in modo affidabile le coppie chiave-valore. Il gestore di stato è un wrapper per il provider di stato. Può essere usato per archiviare i dati indipendentemente dall'impostazione di persistenza utilizzata. Non garantisce assolutamente che un servizio attore in esecuzione possa essere modificato da un'impostazione di stato volatile (solo in memoria) in un'impostazione di stato persistente tramite un aggiornamento in sequenza mantenendo al tempo stesso i dati. Tuttavia, è possibile modificare il numero di repliche per un servizio in esecuzione.

Le chiavi di gestione dello stato devono essere stringhe. I valori sono di tipo generico e possono essere di qualsiasi tipo, inclusi i tipi personalizzati. I valori archiviati nel gestore di stato devono essere serializzabili in base al contratto dati perché possono essere trasmessi in rete ad altri nodi durante la replica e possono essere scritti su disco, a seconda dell'impostazione di persistenza dello stato di un attore.

Il gestore di stato espone i metodi di dizionario comuni per la gestione dello stato, simili a quelli disponibili in Reliable Dictionary.

Per esempi di gestione dello stato degli attori, vedere [Accedere, salvare e rimuovere lo stato di Reliable Actors](service-fabric-reliable-actors-access-save-remove-state.md).

## <a name="best-practices"></a>Procedure consigliate
Di seguito vengono proposti alcuni suggerimenti per la risoluzione dei problemi e procedure consigliate per la gestione dello stato dell'attore.

### <a name="make-the-actor-state-as-granular-as-possible"></a>Rendere lo stato dell'attore il più granulare possibile
Questo è un aspetto cruciale per le prestazioni e l'utilizzo delle risorse dell'applicazione. Per ogni scrittura/aggiornamento dello "stato denominato" di un attore, l'intero valore corrispondente a tale "stato denominato" viene serializzato e inviato alle repliche secondarie tramite la rete.  Le repliche secondarie lo scrivono nel disco locale e rispondono alla replica primaria. Quando la replica primaria riceve i riconoscimenti da un quorum di repliche secondarie, scrive lo stato nel disco locale. Si supponga, ad esempio, che il valore sia una classe con 20 membri e dimensioni pari a 1 MB. Anche se si modifica solo uno dei membri della classe con dimensioni di 1 KB, occorre sostenere i costi della serializzazione e delle scritture in rete e su disco per l'intero MB. Analogamente, se il valore è una raccolta (ad esempio un elenco, una matrice o un dizionario), si paga il costo per l'intera raccolta anche se si modifica uno solo dei membri. L'interfaccia di StateManager della classe dell'attore è simile a un dizionario. È sempre necessario modellare la struttura dei dati che rappresenta lo stato dell'attore sulla base di questo dizionario.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>Gestire correttamente il ciclo di vita dell'attore
È necessario definire criteri chiari per la gestione delle dimensioni dello stato in ogni partizione di un servizio Actor. Il servizio Actor deve avere un numero fisso di attori e riusarli il più possibile. Se si creano continuamente nuovi attori, è necessario eliminarli quando hanno terminato le operazioni. Il framework del servizio Actor archivia alcuni metadati per ogni attore esistente. L'eliminazione di tutto lo stato di un attore non comporta la rimozione dei relativi metadati. È necessario eliminare l'attore (vedere [Eliminazione di attori e del relativo stato](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)) per rimuovere tutte le informazioni su tale attore archiviate nel sistema. A titolo di controllo aggiuntivo, è necessario eseguire query sul servizio Actor (vedere [Enumerare gli attori](service-fabric-reliable-actors-enumerate.md)) regolarmente per assicurarsi che il numero di attori sia compreso nell'intervallo previsto.
 
Se si riscontra un aumento delle dimensioni del file di database di un servizio Actor oltre il limite previsto, assicurarsi di seguire le linee guida precedenti. Se si seguono queste linee guida ed esistono comunque problemi di dimensioni del file di database, è necessario [aprire un ticket di supporto](service-fabric-support.md) per richiedere assistenza al team del prodotto.

## <a name="next-steps"></a>Passaggi successivi

Lo stato archiviato in Reliable Actors deve essere serializzato prima di essere scritto sul disco e replicato per la disponibilità elevata. Altre informazioni sulla [serializzazione del tipo di attore](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Vedere anche [Diagnostica e monitoraggio delle prestazioni per Reliable Actors](service-fabric-reliable-actors-diagnostics.md).
