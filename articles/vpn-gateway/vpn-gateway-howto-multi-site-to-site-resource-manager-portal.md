---
title: 'Aggiungere più connessioni da sito a sito gateway VPN a una rete virtuale: Portale di Azure: Resource Manager | Microsoft Docs'
description: Come aggiungere più connessioni da sito a sito (S2S) a un gateway VPN con una connessione esistente
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: f3e8b165-f20a-42ab-afbb-bf60974bb4b1
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: 4b9f007e00d0912687b723bd4f7e747da893948d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60760463"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Aggiungere una connessione da sito a sito a una rete virtuale con una connessione gateway VPN esistente

> [!div class="op_single_selector"]
> * [Portale di Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (classic)](vpn-gateway-multi-site.md) (PowerShell (classico))
>
> 

Questo articolo consente di aggiungere connessioni da sito a sito (S2S) a un gateway VPN con una connessione esistente usando il portale di Azure. Questo tipo di connessione è spesso definito configurazione "multisito". È possibile aggiungere una connessione da sito a sito a una rete virtuale che dispone già di una connessione da sito a sito, una connessione da punto a sito o una connessione da rete virtuale a rete virtuale. Quando si aggiungono delle connessioni, esistono alcune limitazioni di cui è necessario tenere conto. Prima di iniziare la configurazione, verificare quanto riportato nella sezione [Prima di iniziare](#before) di questo articolo. 

Questo articolo si applica alle reti virtuali di Resource Manager con un gateway VPN RouteBased. Questi passaggi non si applicano alle configurazioni con connessioni coesistenti ExpressRoute/da sito a sito. Per informazioni sulle connessioni coesistenti vedere [Creare connessioni coesistenti da sito a sito ed ExpressRoute](../expressroute/expressroute-howto-coexist-resource-manager.md).

### <a name="deployment-models-and-methods"></a>Metodi e modelli di distribuzione
[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Questa tabella viene aggiornata man mano che per questa configurazione diventano disponibili nuovi articoli e altri strumenti. Quando un articolo è disponibile, nella tabella sarà presente un collegamento diretto.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before"></a>Prima di iniziare
Verificare quanto segue:

* Non si sta creando una connessione coesistente ExpressRoute/da sito a sito.
* Si dispone di una rete virtuale creata usando il modello di distribuzione Resource Manager con una connessione esistente.
* Il gateway di rete virtuale per la rete virtuale è di tipo RouteBased. Se si dispone di un gateway VPN basato su criteri, è necessario eliminare il gateway di rete virtuale e creare un nuovo gateway VPN di tipo RouteBased.
* Nessuno degli intervalli di indirizzi si sovrappone a una qualsiasi delle reti virtuali a cui si connette questa rete virtuale.
* Si dispone di un dispositivo VPN compatibile ed è presente un utente in grado di configurarlo. Vedere [Informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md). Se non si ha familiarità con la configurazione del dispositivo VPN o con gli intervalli di indirizzi IP disponibili nella configurazione di rete locale, è necessario coordinarsi con qualcuno che possa fornire tali dettagli.
* Si dispone di un indirizzo IP pubblico esterno per il dispositivo VPN. L'indirizzo IP non può trovarsi dietro un NAT.

## <a name="part1"></a>Parte 1: Configurare una connessione
1. In un browser passare al [portale di Azure](https://portal.azure.com) e, se necessario, accedere con l'account Azure.
2. Fare clic su **All resources** (Tutte le risorse), individuare il **gateway di rete virtuale** nell'elenco di risorse e selezionarlo.
3. Nella pagina **Gateway di rete virtuale**, fare clic su **Connessioni**.
   
    ![Pagina Connessioni](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Pagina Connessioni")<br>
4. Nella pagina **Connessioni** fare clic su **+Aggiungi**.
   
    ![Pulsante Aggiungi connessione](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Pulsante Aggiungi connessione")<br>
5. Nella pagina **Aggiungi connessione** compilare i campi seguenti:
   
   * **Nome:** nome del sito a cui si sta creando la connessione.
   * **Tipo di connessione**: selezionare **Da sito a sito (IPsec)** .
     
     ![Pagina Aggiungi connessione](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Pagina Aggiungi connessione")<br>

## <a name="part2"></a>Parte 2: Aggiungere un gateway di rete locale
1. Fare clic su **Gateway di rete locale** ***Scegli un gateway di rete locale***. Viene aperta la pagina **Scegli gateway di rete locale**.
   
    ![Scegli gateway di rete virtuale](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Scegli gateway di rete virtuale")<br>
2. Fare clic su **Crea nuovo** per aprire la pagina **Crea un gateway di rete locale**.
   
    ![Pagina Crea un gateway di rete locale](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Crea un gateway di rete locale")<br>
3. Nella pagina **Crea un gateway di rete locale** compilare i campi seguenti:
   
   * **Nome:** nome da assegnare alla risorsa gateway di rete locale.
   * **Indirizzo IP**: indirizzo IP pubblico del dispositivo VPN nel sito a cui si vuole connettersi.
   * **Spazio indirizzi**: spazio indirizzi che si vuole venga indirizzato al nuovo sito di rete locale.
4. Fare clic su **OK** nella pagina **Crea un gateway di rete locale** per salvare le modifiche.

## <a name="part3"></a>Parte 3: Aggiungere la chiave condivisa e creare la connessione
1. Nella pagina **Aggiungi connessione** aggiungere la chiave condivisa che si intende usare per creare la connessione. È possibile ottenere la chiave condivisa dal dispositivo VPN oppure crearne una in questa sede e quindi configurare il dispositivo VPN per l'uso della stessa chiave condivisa. È fondamentale che le chiavi siano assolutamente identiche.
   
    ![Chiave condivisa](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Chiave condivisa")<br>
2. Nella parte inferiore della pagina fare clic su **OK** per creare la connessione.

## <a name="part4"></a>Parte 4: Verificare la connessione VPN


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per altre informazioni, vedere il [percorso di apprendimento relativo alle macchine virtuali](/learn/paths/deploy-a-website-with-azure-virtual-machines/).
