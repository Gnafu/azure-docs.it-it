---
title: Panoramica del TAP di rete virtuale di Azure | Microsoft Docs
description: Informazioni sul TAP di rete virtuale. Il TAP di rete virtuale offre una copia completa del traffico di rete della macchina virtuale che può essere trasmessa a un agente di raccolta di pacchetti.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: ff5c8c4d3f6a0c87afae67404a5a39d4fe3757d9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60593177"
---
# <a name="virtual-network-tap"></a>TAP di rete virtuale

Un TAP (Terminal Access Point) di rete virtuale di Azure consente di trasmettere il traffico di rete della macchina virtuale come flusso continuo a un agente di raccolta di pacchetti di rete o a uno strumento di analisi. L'agente di raccolta o lo strumento di analisi viene fornito da un partner [appliance virtuale di rete](https://azure.microsoft.com/solutions/network-appliances/). Per un elenco delle soluzioni dei partner convalidate per l'uso con un TAP di rete virtuale, vedere le [soluzioni dei partner](#virtual-network-tap-partner-solutions).

> [!IMPORTANT]
> Rete virtuale scelta è attualmente in anteprima in tutte le aree di Azure. Per usare dispositivo TAP di rete virtuale, è necessario registrare nell'anteprima inviando un messaggio di posta elettronica <azurevnettap@microsoft.com> con l'ID sottoscrizione. Si riceverà un messaggio di posta elettronica dopo la registrazione della sottoscrizione. Non è possibile usare la funzionalità finché non si riceve un messaggio di posta elettronica di conferma. Questa versione di anteprima viene fornito senza un contratto di servizio e non deve essere utilizzata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere le [condizioni per l'utilizzo per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per informazioni dettagliate.

## <a name="virtual-network-tap-partner-solutions"></a>Soluzioni dei partner per TAP di rete virtuale

### <a name="network-packet-brokers"></a>Broker pacchetti di rete

- [Big Switch Big Monitoring Fabric](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)
- [Gigamon GigaSECURE](https://blog.gigamon.com/2018/09/13/why-microsofts-new-vtap-service-works-even-better-with-gigasecure-for-azure)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva Prisms](https://www.nubeva.com/azurevtap)

### <a name="security-analytics-networkapplication-performance-management"></a>Analisi della sicurezza, gestione delle prestazioni di rete/applicazioni

- [Sicurezza attivo](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Stealthwatch Cisco Cloud](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/company/tech-partners/microsoft/)
- [Fidelis Cybersecurity](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/technology-partners/microsoft/azure-vtap)
- [RSA NetWitness® Platform](https://www.rsa.com/azure)
- [Vectra Cognito](https://vectra.ai/microsoftazure)

L'immagine seguente illustra come funziona un TAP di rete virtuale. È possibile aggiungere una configurazione di TAP in un'[interfaccia di rete](virtual-network-network-interface.md) collegata a una macchina virtuale distribuita nella rete virtuale. La destinazione è un indirizzo IP di rete virtuale nella stessa rete virtuale dell'interfaccia di rete monitorata o una rete [virtuale con peering](virtual-network-peering-overview.md). La soluzione dell'agente di raccolta per il TAP di rete virtuale può essere distribuita dietro un [bilanciamento del carico interno di Azure](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#concepts) per garantire disponibilità elevata. Per valutare le opzioni di distribuzione per le singole soluzioni, vedere le [soluzioni dei partner](#virtual-network-tap-partner-solutions).

![Come funziona il TAP di rete virtuale](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Prerequisiti

Prima di creare un dispositivo TAP di rete virtuale, è necessario aver ricevuto un messaggio di conferma che si sono registrati per l'anteprima e è uno o più macchine virtuali create tramite [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) modello di distribuzione e un partner soluzione per aggregare il traffico di TOCCO nella stessa area di azure. Se non è disponibile una soluzione di un partner nella rete virtuale, vedere le [soluzioni dei partner](#virtual-network-tap-partner-solutions) per distribuirne una. È possibile usare la stessa risorsa TAP di rete virtuale per aggregare il traffico da più interfacce di rete nella stessa sottoscrizione o in sottoscrizioni diverse. Se le interfacce di rete monitorate si trovano in sottoscrizioni diverse, le sottoscrizioni devono essere associate allo stesso tenant di Azure Active Directory. Inoltre, le interfacce di rete monitorate e l'endpoint di destinazione per aggregare il traffico TAP possono trovarsi in reti virtuali con peering nella stessa area. Se si usa questo modello di distribuzione, assicurarsi che il [peering di reti virtuali](virtual-network-peering-overview.md) sia abilitato prima di configurare il TAP di rete virtuale.

## <a name="permissions"></a>Autorizzazioni

Gli account usati per applicare la configurazione TAP alle interfacce di rete devono essere assegnati al ruolo [Collaboratore Rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un [ruolo personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le azioni necessarie dalla tabella seguente:

| Azione | NOME |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | Necessaria per creare, aggiornare, leggere ed eliminare una risorsa TAP di rete virtuale |
| Microsoft.Network/networkInterfaces/read | Necessaria per leggere la risorsa interfaccia di rete in cui verrà configurato il TAP |
| Microsoft.Network/tapConfigurations/* | Necessaria per creare, aggiornare, leggere ed eliminare la configurazione TAP per un'interfaccia di rete |

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare un TAP di rete virtuale](tutorial-tap-virtual-network-cli.md).
