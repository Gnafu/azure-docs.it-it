---
title: Panoramica di BGP e gateway VPN di Azure | Microsoft Docs
description: Questo articolo fornisce una panoramica di BGP con i gateway VPN di Azure.
services: vpn-gateway
author: yushwang
manager: rossort
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/25/2019
ms.author: yushwang
ms.openlocfilehash: e85eb756e416f1170a0cb2b19ab68888b5a9422f
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516430"
---
# <a name="about-bgp-with-azure-vpn-gateway"></a>Informazioni su BGP con i gateway VPN di Azure
Questo articolo fornisce una panoramica del supporto BGP (Border Gateway Protocol) nei gateway VPN di Azure.

BGP è il protocollo di routing standard comunemente usato in Internet per lo scambio di informazioni di routing e raggiungibilità tra due o più reti. Quando viene usato nel contesto di reti virtuali di Azure, BGP consente ai gateway VPN di Azure e ai dispositivi VPN locali, detti peer BGP o router adiacenti, lo scambio di "route" che indicano a entrambi i gateway la disponibilità e la raggiungibilità di tali prefissi per il passaggio attraverso i gateway o i router coinvolti. BGP può anche abilitare il routing di transito tra più reti propagando a tutti gli altri peer BGP le route che un gateway BGP apprende da un peer BGP. 

## <a name="why"></a>Perché usare BGP
BGP è una funzionalità facoltativa che può essere usata con i gateway VPN di Azure basati su route. Prima di abilitare tale funzionalità, assicurarsi che i dispositivi VPN locali supportino BGP. È possibile continuare a usare i gateway VPN di Azure e i dispositivi VPN locali senza BGP. In altre parole occorre scegliere tra l'uso delle route statiche, senza BGP, *oppure* l'uso del routing dinamico con BGP tra le reti e Azure.

BGP offre nuove funzionalità e numerosi vantaggi, illustrati di seguito.

### <a name="prefix"></a>Supporto per l'aggiornamento automatico e flessibile dei prefissi
Con BGP, è sufficiente dichiarare un prefisso minimo a un peer BGP specifico attraverso il tunnel VPN S2S IPsec. La dimensione minima consentita è quella di un prefisso dell'host (/32) dell'indirizzo IP del peer BGP per il dispositivo VPN locale. È possibile controllare quali prefissi di rete locale segnalare in Azure per consentire alla rete virtuale di Azure di accedere.

È anche possibile segnalare prefissi più grandi, che possono includere alcuni dei prefissi di indirizzo della rete virtuale, come uno spazio di indirizzi IP privato di grandi dimensioni, ad esempio 10.0.0.0/8. Si noti che i prefissi non possono essere identici ad alcuno dei prefissi della rete virtuale. Le route identiche ai prefissi della rete virtuale verranno rifiutate.

### <a name="multitunnel"></a>Supporto di più tunnel tra una rete virtuale e un sito locale con failover automatico basato su BGP
È possibile stabilire più connessioni tra la rete virtuale di Azure e i dispositivi VPN locali nella stessa località. Questa funzionalità fornisce più tunnel, o percorsi, tra le due reti in una configurazione di tipo attivo/attivo. Se uno dei tunnel viene disconnesso, le route corrispondenti vengono ritirate tramite BGP e il traffico si sposta automaticamente nei tunnel restanti.

Il diagramma seguente mostra un esempio semplice di questa configurazione a disponibilità elevata:

![Più percorsi attivi](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

### <a name="transitrouting"></a>Supporto del routing di transito tra le reti locali e più reti virtuali di Azure
BGP consente a più gateway di apprendere e propagare i prefissi da reti diverse, connesse sia direttamente che indirettamente. Questo consente il routing di transito con i gateway VPN di Azure tra i siti locali o tra più reti virtuali di Azure.

Il diagramma seguente illustra un esempio di una topologia multihop con più percorsi che possono far passare il traffico tra le due reti locali tramite i gateway VPN di Azure all'interno di reti Microsoft:

![Transito multihop](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## <a name="faq"></a>DOMANDE FREQUENTI SU BGP
[!INCLUDE [vpn-gateway-faq-bgp-include](../../includes/vpn-gateway-faq-bgp-include.md)]

## <a name="next-steps"></a>Passaggi successivi
Per i passaggi di configurazione di BGP per le connessioni cross-premise e tra reti virtuali, vedere [Introduzione a BGP nei gateway VPN di Azure](vpn-gateway-bgp-resource-manager-ps.md).

