---
title: Impostare un dominio Internet aziendale in modo che punti a un nome di dominio di Gestione traffico | Documentazione Microsoft
description: In questo articolo vengono fornite istruzioni per scegliere il nome di dominio aziendale per un nome di dominio di Gestione traffico.
services: traffic-manager
documentationcenter: ''
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
ms.openlocfilehash: 45fe4fd8511cd1d725275a5a04bd4b6e13eb68f7
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2018
ms.locfileid: "50138396"
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>Impostare un dominio Internet aziendale in modo che punti a un dominio di Gestione traffico di Azure

Quando si crea un profilo di Gestione traffico, Azure assegna automaticamente un nome DNS per tale profilo. Per usare un nome dalla zona DNS, creare un record DNS CNAME che esegua il mapping al nome di dominio del profilo di Gestione traffico. È possibile visualizzare il nome di dominio di Gestione traffico nella sezione **Generale** nella pagina di configurazione del profilo di Gestione traffico.

Ad esempio, per scegliere il nome `www.contoso.com` come nome DNS di Gestione traffico `contoso.trafficmanager.net` si crea il record di risorse DNS seguente:

    www.contoso.com IN CNAME contoso.trafficmanager.net

Tutto il traffico indirizzato a *www.contoso.com* viene reindirizzato a *contoso.trafficmanager.net*.

> [!IMPORTANT]
> Non è possibile scegliere un dominio di secondo livello, come *contoso.com*, per il dominio di Gestione traffico. Gli standard di protocollo DNS non consentono record CNAME per i nomi di dominio di secondo livello.

## <a name="next-steps"></a>Passaggi successivi

* [Metodi di routing di Gestione traffico](traffic-manager-routing-methods.md)
* [Gestione traffico: disabilitare, abilitare o eliminare un profilo](disable-enable-or-delete-a-profile.md)
* [Gestione traffico: disabilitare o abilitare un endpoint](disable-or-enable-an-endpoint.md)
