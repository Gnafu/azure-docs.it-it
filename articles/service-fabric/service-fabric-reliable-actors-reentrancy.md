---
title: Reentrancy in Azure Service Fabric Actors | Microsoft Docs
description: Introduzione alla reentrancy per Reliable Actors di Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: amanbha
ms.assetid: be23464a-0eea-4eca-ae5a-2e1b650d365e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 46682787bac2d60d188384a4078ca2fa1f46ae7a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60725415"
---
# <a name="reliable-actors-reentrancy"></a>Rientranza di Reliable Actors
Per impostazione predefinita, il runtime di Reliable Actors consente la reentrancy basata sul contesto di chiamata logico. Ciò consente agli attori di essere rientranti se si trovano nella stessa catena del contesto di chiamata. Ad esempio, l'attore A invia un messaggio all'attore B che invia un messaggio all'attore C. Durante l'elaborazione del messaggio, se l'attore C chiama l'attore A, il messaggio è rientrante e sarà quindi consentito. Tutti gli altri messaggi che fanno parte di un contesto di chiamata diverso verranno bloccati sull'attore A fino al completamento dell'elaborazione.

Per la reentrancy degli attori sono disponibili due opzioni, definite nell'enumerazione `ActorReentrancyMode` :

* `LogicalCallContext` (comportamento predefinito)
* `Disallowed` : disabilita la reentrancy

```csharp
public enum ActorReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```
```Java
public enum ActorReentrancyMode
{
    LogicalCallContext(1),
    Disallowed(2)
}
```
La reentrancy può essere configurata nelle impostazioni di `ActorService`durante la registrazione. L'impostazione si applica a tutte le istanze degli attori create nel servizio Actor.

L'esempio seguente illustra un servizio Actor che imposta la modalità di reentrancy su `ActorReentrancyMode.Disallowed`. In questo caso, se un attore invia un messaggio rientrante a un altro attore verrà generata un'eccezione di tipo `FabricException` .

```csharp
static class Program
{
    static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<Actor1>(
                (context, actorType) => new ActorService(
                    context,
                    actorType, () => new Actor1(),
                    settings: new ActorServiceSettings()
                    {
                        ActorConcurrencySettings = new ActorConcurrencySettings()
                        {
                            ReentrancyMode = ActorReentrancyMode.Disallowed
                        }
                    }))
                .GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}
```
```Java
static class Program
{
    static void Main()
    {
        try
        {
            ActorConcurrencySettings actorConcurrencySettings = new ActorConcurrencySettings();
            actorConcurrencySettings.setReentrancyMode(ActorReentrancyMode.Disallowed);

            ActorServiceSettings actorServiceSettings = new ActorServiceSettings();
            actorServiceSettings.setActorConcurrencySettings(actorConcurrencySettings);

            ActorRuntime.registerActorAsync(
                Actor1.getClass(),
                (context, actorType) -> new FabricActorService(
                    context,
                    actorType, () -> new Actor1(),
                    null,
                    stateProvider,
                    actorServiceSettings, timeout);

            Thread.sleep(Long.MAX_VALUE);
        }
        catch (Exception e)
        {
            throw e;
        }
    }
}
```


## <a name="next-steps"></a>Passaggi successivi
* Acquisire altre informazioni sulla reentrancy nella [documentazione di riferimento delle API di Actors](https://msdn.microsoft.com/library/azure/dn971626.aspx)
