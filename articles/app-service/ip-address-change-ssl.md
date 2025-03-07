---
title: Preparare un cambio di indirizzo IP SSL - Servizio app di Azure
description: Se l'indirizzo IP SSL sta per essere modificato, questo articolo spiega come procedere in modo che l'app continui a funzionare anche dopo la modifica.
services: app-service\web
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 06/28/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 6c8c86ff6212acc31e961d6ae62836ca2b7b7380
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61268908"
---
# <a name="how-to-prepare-for-an-ssl-ip-address-change"></a>Come preparare un cambio di indirizzo IP SSL

Se si riceve una notifica di modifica dell'indirizzo IP SSL dell'app Servizio app di Azure, seguire le istruzioni riportate in questo articolo per rilasciare un indirizzo IP SSL esistente e assegnarne uno nuovo.

## <a name="release-ssl-ip-addresses-and-assign-new-ones"></a>Rilasciare indirizzi IP SSL e assegnarne di nuovi

1.  Aprire il [portale di Azure](https://portal.azure.com).

2.  Nel menu di spostamento a sinistra selezionare **Servizi app**.

3.  Selezionare il servizio app nell'elenco.

4.  Sotto l'intestazione **Impostazioni** fare clic su **Impostazioni SSL** nel riquadro di spostamento a sinistra.

1. Nella sezione delle associazioni SSL selezionare il record del nome host. Nell'editor che si apre scegliere **SNI SSL** nel menu a discesa **Tipo SSL** e fare clic su **Aggiungi binding**. Quando compare il messaggio che avvisa della riuscita dell'operazione, l'indirizzo IP esistente è stato rilasciato.

6.  Nella sezione **Associazioni SSL** selezionare di nuovo lo stesso record del nome host con il certificato. Nell'editor che si apre questa volta scegliere **SSL basato su IP** nel menu a discesa **Tipo SSL** e fare clic su **Aggiungi binding**. Quando compare il messaggio che avvisa della riuscita dell'operazione, è stato acquisito un nuovo indirizzo IP.

7.  Se nel portale di registrazione di dominio (provider DNS di terze parti o servizio DNS di Azure) è configurato un record A (record DNS che punta direttamente all'indirizzo IP dell'utente), sostituire l'indirizzo IP esistente con quello appena generato. È possibile trovare il nuovo indirizzo IP seguendo le istruzioni nella sezione successiva.

## <a name="find-the-new-ssl-ip-address-in-the-azure-portal"></a>Trovare il nuovo indirizzo IP SSL nel portale di Azure

1.  Attendere qualche minuto e quindi aprire il [portale di Azure](https://portal.azure.com).

2.  Nel menu di spostamento a sinistra selezionare **Servizi app**.

3.  Selezionare il servizio app nell'elenco.

4.  Sotto l'intestazione **Impostazioni** fare clic su **Proprietà** nel menu di spostamento a sinistra e individuare la sezione denominata **Indirizzo IP virtuale**.

5. Copiare l'indirizzo IP e riconfigurare il record di dominio o meccanismo di IP.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come preparare un cambio di indirizzo IP avviato da Azure. Per altre informazioni sugli indirizzi IP in Servizio app di Azure, vedere [Indirizzi IP in ingresso e in uscita in Servizio app di Azure](overview-inbound-outbound-ips.md).
