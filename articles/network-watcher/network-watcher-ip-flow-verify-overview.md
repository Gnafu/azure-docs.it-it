---
title: Introduzione alla verifica del flusso IP in Azure Network Watcher | Microsoft Docs
description: Questa pagina fornisce una panoramica della funzionalità di verifica del flusso IP di Network Watcher
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: d352fb2d-4b4f-4ac4-9c2e-1cfccf0e7e03
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2017
ms.author: kumud
ms.openlocfilehash: 5c34fd2b6d354f594ed153647c1bed700566fad6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64709586"
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Introduzione alla verifica del flusso IP in Azure Network Watcher

Il flusso IP controlla se un pacchetto viene accettato o rifiutato in ingresso o in uscita da una macchina virtuale. Le informazioni sono costituite da direzione, protocollo, indirizzo IP locale, indirizzo IP remoto, porta locale e porta remota. Se il pacchetto viene rifiutato da un gruppo di sicurezza, viene restituito il nome della regola che ha rifiutato il pacchetto. Anche se è possibile scegliere qualsiasi indirizzo IP di origine o di destinazione, la verifica del flusso IP permette agli amministratori di diagnosticare rapidamente problemi di connettività in ingresso o in uscita da Internet e in ingresso o in uscita dall'ambiente locale.

La verifica del flusso IP esamina le regole per tutti i gruppi di sicurezza di rete applicate all'interfaccia di rete, ad esempio una subnet o la scheda di interfaccia di rete di una macchina virtuale. Il flusso di traffico in ingresso o in uscita dall'interfaccia di rete viene quindi verificato in base alle impostazioni configurate. La verifica del flusso IP permette di confermare se una regola di un gruppo di sicurezza di rete sta bloccando il traffico in ingresso o in uscita da una macchina virtuale.

È necessario creare un'istanza di Network Watcher in tutte le aree in cui è prevista l'esecuzione della verifica del flusso IP. Network Watcher è un servizio a livello di area e può essere eseguito solo su risorse nella stessa area. L'istanza usata non influisce sui risultati della verifica del flusso IP, perché viene comunque restituita qualsiasi route associata alla scheda di interfaccia di rete o alla subnet.

![1][1]

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come stabilire se un pacchetto viene accettato o rifiutato per una macchina virtuale specifica tramite il portale, vedere l'articolo seguente. [Controllare se il traffico da o verso una macchina virtuale è consentito o negato con la verifica del flusso IP tramite il portale](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png

