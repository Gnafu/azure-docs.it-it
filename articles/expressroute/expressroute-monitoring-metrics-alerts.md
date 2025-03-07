---
title: Monitoraggio, metriche e avvisi - Azure ExpressRoute | Microsoft Docs
description: Questa pagina fornisce informazioni sul monitoraggio di ExpressRoute
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: dbe03ef29bd28d465fa671abc915d63d4b038cb2
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71154774"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Monitoraggio, metriche e avvisi di ExpressRoute

Questo articolo illustra il monitoraggio, le metriche e gli avvisi di ExpressRoute usando Monitoraggio di Azure. Monitoraggio di Azure è uno strumento centralizzato per tutte le metriche, gli avvisi, i log di diagnostica in Azure.
 
>[!NOTE]
>L'uso delle **metriche classiche** non è consigliato.
>

## <a name="expressroute-metrics"></a>Metriche ExpressRoute

Per visualizzare le **metriche**, passare alla pagina *monitoraggio di Azure* e fare clic su *metriche*. Per visualizzare le metriche **ExpressRoute** , il filer in base al tipo di risorsa *circuiti ExpressRoute*. Per visualizzare **copertura globale** metriche, filtrare in base al tipo di risorsa *circuiti ExpressRoute* e selezionare una risorsa del circuito ExpressRoute con copertura globale abilitata. Per visualizzare le metriche **dirette di ExpressRoute** , filtrare il tipo di risorsa per le *porte ExpressRoute*. 

Una volta selezionata la metrica, verrà applicata l'aggregazione predefinita. Facoltativamente, è possibile applicare la suddivisione, che mostrerà la metrica con dimensioni diverse.

### <a name="available-metrics"></a>Metriche disponibili
|**Metrica**|**Categoria**|**Dimensione/i**|**Funzionalità**|
| --- | --- | --- | --- |
|Disponibilità ARP|Disponibilità|<ui><li>Peer (router ExpressRoute primario/secondario)</ui></li><ui><li> Tipo di peering (privato/pubblico/Microsoft)</ui></li>|ExpressRoute|
|Disponibilità BGP|Disponibilità|<ui><li> Peer (router ExpressRoute primario/secondario)</ui></li><ui><li> Tipo di peering</ui></li>|ExpressRoute|
|BitsInPerSecond|Traffico|<ui><li> Tipo di peering (ExpressRoute)</ui></li><ui><li>Collegamento (ExpressRoute Direct)</ui></li>| <li> ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|Traffico| <ui><li>Tipo di peering (ExpressRoute)</ui></li><ui><li> Collegamento (ExpressRoute Direct) | <ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li> |
|GlobalReachBitsInPerSecond|Traffico|<ui><li>Skey del circuito con peering (chiave del servizio)</ui></li>|Copertura globale|
|GlobalReachBitsOutPerSecond|Traffico|<ui><li>Skey del circuito con peering (chiave del servizio)</ui></li>|Copertura globale|
|AdminState|Connettività fisica|Collegamento|ExpressRoute Direct|
|LineProtocol|Connettività fisica|Collegamento|ExpressRoute Direct|
|RxLightLevel|Connettività fisica|<ui><li>Collegamento</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
|TxLightLevel|Connettività fisica|<ui><li>Collegamento</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
>[!NOTE]
>L'uso di *GlobalGlobalReachBitsInPerSecond* e *GlobalGlobalReachBitsOutPerSecond* sarà visibile solo se viene stabilita almeno una connessione copertura globale.
>

## <a name="circuits-metrics"></a>Metriche circuiti

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>BITS in e out-metriche in tutti i peering

È possibile visualizzare le metriche in tutti i peering in un circuito ExpressRoute specifico.

![metrica del circuito](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

### <a name="bits-in-and-out---metrics-per-peering"></a>BITS in e out-metriche per peering

È possibile visualizzare le metriche in base al tipo di peering privato, pubblico e Microsoft in bit al secondo.

![metrica per peering](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

### <a name="bgp-availability---split-by-peer"></a>Disponibilità BGP-suddivisione per peer  

È possibile visualizzare la disponibilità quasi in tempo reale di BGP tra peering e peer (router ExpressRoute primari e secondari). Questo dashboard mostra la sessione BGP primaria per il peering privato e la seconda sessione BGP per il peering privato. 

![Disponibilità BGP per peer](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

### <a name="arp-availability---split-by-peering"></a>Disponibilità ARP-suddivisione per peering  

È possibile visualizzare la disponibilità quasi in tempo reale di [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) tra peering e peer (router ExpressRoute primari e secondari). Questo dashboard mostra la sessione ARP per il peering privato in entrambi i peer, ma è stata completata per il peering Microsoft nei peering. L'aggregazione predefinita (media) è stata utilizzata in entrambi i peer.  

![Disponibilità ARP per peer](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Connessioni gateway di ExpressRoute in bit al secondo

![connessioni gateway](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg ) 

## <a name="alerts-for-expressroute-gateway-connections"></a>Avvisi per le connessioni gateway di ExpressRoute

1. Per configurare gli avvisi, passare a **Monitoraggio di Azure**, quindi fare clic su **Avvisi**.

   ![avvisi](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. Fare clic su **+Seleziona destinazione** e selezionare la risorsa di connessione gateway di ExpressRoute.

   ![target]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. Definire i dettagli dell'avviso.

   ![gruppo di azioni](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. Definire e aggiungere il gruppo di azioni.

   ![aggiungi gruppo di azioni](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>Avvisi basati su ciascun peering

 ![Dettagli](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Configurare avvisi per i log attività sui circuiti

In **Criteri di avviso** è possibile selezionare **Log attività** per il tipo di segnale e selezionare il segnale.

  ![altro](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>Passaggi successivi

Configurare la connessione ExpressRoute.
  
  * [Creare e modificare un circuito](expressroute-howto-circuit-arm.md)
  * [Creare e modificare la configurazione di peering](expressroute-howto-routing-arm.md)
  * [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)
