---
title: 'Connettere la rete locale a una rete virtuale di Azure: VPN da sito a sito: Portale | Microsoft Docs'
description: Passaggi per creare una connessione IPsec dalla rete locale a una rete virtuale di Azure tramite Internet pubblico. Questa procedura consentirà di creare una connessione gateway VPN da sito a sito cross-premise usando il portale.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: cherylmc
ms.openlocfilehash: 032b6a4f5147d06a4613a827a0372437dca47f47
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2018
ms.locfileid: "53651640"
---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>Creare una connessione da sito a sito nel portale di Azure

Questo articolo illustra come usare il portale di Azure per creare una connessione gateway VPN da sito a sito dalla rete locale alla rete virtuale. I passaggi di questo articolo sono applicabili al modello di distribuzione Resource Manager. È anche possibile creare questa configurazione usando strumenti o modelli di distribuzione diversi selezionando un'opzione differente nell'elenco seguente:

> [!div class="op_single_selector"]
> * [Portale di Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Portale di Azure (classico)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

Una connessione gateway VPN da sito a sito viene usata per connettere la rete locale a una rete virtuale di Azure tramite un tunnel VPN IPsec/IKE (IKEv1 o IKEv2). Questo tipo di connessione richiede un dispositivo VPN che si trova in locale con un indirizzo IP pubblico esterno assegnato. Per altre informazioni sui gateway VPN, vedere [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md).

![Diagramma della connessione cross-premise gateway VPN da sito a sito](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la configurazione, verificare di soddisfare i criteri seguenti:

* Verificare di avere un dispositivo VPN compatibile e che sia presente un utente in grado di configurarlo. Per altre informazioni sui dispositivi VPN compatibili e sulla configurazione dei dispositivi, vedere [Informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md).
* Verificare di avere un indirizzo IPv4 pubblico esterno per il dispositivo VPN.
* Se non si ha familiarità con gli intervalli degli indirizzi IP disponibili nella configurazione della rete locale, è necessario coordinarsi con qualcuno che possa fornire tali dettagli. Quando si crea questa configurazione, è necessario specificare i prefissi degli intervalli di indirizzi IP che Azure instraderà alla posizione locale. Nessuna delle subnet della rete locale può sovrapporsi alle subnet della rete virtuale a cui ci si vuole connettere. 

### <a name="values"></a>Valori di esempio

Gli esempi di questo articolo usano i valori seguenti. È possibile usare questi valori per creare un ambiente di test o farvi riferimento per comprendere meglio gli esempi di questo articolo. Per altre informazioni sulle impostazioni generali del gateway VPN, vedere [Informazioni sulle impostazioni del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md).

* **Nome della rete virtuale:** TestVNet1
* **Spazio di indirizzi:** 10.1.0.0/16
* **Sottoscrizione:** sottoscrizione che si vuole usare
* **Gruppo di risorse:** TestRG1
* **Località:** Stati Uniti orientali
* **Subnet:** FrontEnd: 10.1.0.0/24, BackEnd: 10.1.1.0/24 (facoltativa per questo esercizio)
* **Nome subnet del gateway:** GatewaySubnet (compilato automaticamente nel portale)
* **Intervallo di indirizzi subnet del gateway:** 10.1.255.0/27
* **Server DNS:** 8.8.8.8 - facoltativo. L'indirizzo IP del server DNS.
* **Nome del gateway di rete virtuale:** VNet1GW
* **IP pubblico:** VNet1GWIP
* **Tipo VPN:** basato su route
* **Tipo di connessione:** da sito a sito (IPsec)
* **Tipo di gateway:** VPN
* **Nome del gateway di rete locale:** Site1
* **Nome connessione:** VNet1toSite1
* **Chiave condivisa:** in questo esempio, viene usato abc123. È possibile usare qualsiasi valore compatibile con l'hardware VPN, ma è importante che i valori corrispondano su entrambi i lati della connessione.

## <a name="CreatVNet"></a>1. Crea rete virtuale

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-create-virtual-network-portal-include.md)]

## <a name="dns"></a>2. Specificare un server DNS

Il server DNS non è necessario per creare una connessione da sito a sito. Se tuttavia si vuole ottenere la risoluzione dei nomi per le risorse distribuite nella rete virtuale, è necessario specificare un server DNS. Questa impostazione consente di specificare il server DNS da usare per la risoluzione dei nomi nella rete virtuale. Non comporta la creazione di un server DNS. Per altre informazioni sulla risoluzione dei nomi, vedere [Risoluzione dei nomi per le macchine virtuali e le istanze del ruolo](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

[!INCLUDE [Specify a dns server - optional](../../includes/vpn-gateway-specify-dns-portal-include.md)]

## <a name="gatewaysubnet"></a>3. Creare la subnet del gateway

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-include.md)]

[!INCLUDE [Add a gateway subnet](../../includes/vpn-gateway-add-gateway-subnet-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="VNetGateway"></a>4. Creare il gateway VPN

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gateway-portal-include.md)]

## <a name="LocalNetworkGateway"></a>5. Creare il gateway di rete locale

Il gateway di rete locale in genere fa riferimento al percorso locale. Assegnare al sito un nome che Azure possa usare come riferimento, quindi specificare l'indirizzo IP del dispositivo VPN locale con cui si creerà una connessione. Specificare anche i prefissi degli indirizzi IP che verranno instradati tramite il gateway VPN al dispositivo VPN. I prefissi degli indirizzi specificati sono quelli disponibili nella rete locale. Se la rete locale viene modificata o è necessario modificare l'indirizzo IP pubblico del dispositivo VPN, è possibile aggiornare facilmente i valori in un secondo momento.

[!INCLUDE [Add a local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-include.md)]

## <a name="VPNDevice"></a>6. Configurare il dispositivo VPN

Le connessioni da sito a sito verso una rete locale richiedono un dispositivo VPN. In questo passaggio viene configurato il dispositivo VPN. Durante la configurazione del dispositivo VPN, è necessario quanto segue:

- Chiave condivisa. Si tratta della stessa chiave condivisa che viene specificata durante la creazione della connessione VPN da sito a sito. In questi esempi viene usata una chiave condivisa semplice. È consigliabile generare una chiave più complessa per l'uso effettivo.
- Indirizzo IP pubblico del gateway di rete virtuale. È possibile visualizzare l'indirizzo IP pubblico usando il portale di Azure, PowerShell o l'interfaccia della riga di comando. Per trovare l'indirizzo IP pubblico del gateway VPN usando il portale di Azure, passare a **Gateway di rete virtuali**, quindi fare clic sul nome del gateway.

[!INCLUDE [Configure a VPN device](../../includes/vpn-gateway-configure-vpn-device-include.md)]

## <a name="CreateConnection"></a>7. Creare la connessione VPN

Creare la connessione VPN da sito a sito tra il gateway di rete virtuale e il dispositivo VPN locale.

[!INCLUDE [Add a site-to-site connection](../../includes/vpn-gateway-add-site-to-site-connection-portal-include.md)]

## <a name="VerifyConnection"></a>8. Verificare la connessione VPN

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="connectVM"></a>Per connettersi a una macchina virtuale

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]

## <a name="reset"></a>Come reimpostare un gateway VPN

La reimpostazione del gateway VPN di Azure è utile se si perde la connettività VPN cross-premise in uno o più tunnel VPN da sito a sito. In questa situazione tutti i dispositivi VPN funzionano correttamente, ma non sono in grado di stabilire tunnel IPsec con i gateway VPN di Azure. Per la procedura da seguire, vedere [Reimpostare un gateway VPN](vpn-gateway-resetgw-classic.md).

## <a name="resize"></a>Come modificare uno SKU del gateway e ridimensionare un gateway

Per la procedura da seguire per modificare uno SKU del gateway, vedere [SKU del gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

## <a name="addconnect"></a>Come aggiungere un'altra connessione a un gateway VPN

È possibile aggiungere altre connessioni a condizione che non si crei alcuna sovrapposizione degli spazi indirizzi tra le connessioni.

1. Per aggiungere un'altra connessione, passare al gateway VPN e quindi fare clic su **Connessioni** per aprire la pagina Connessioni.
2. Fare clic su **+Aggiungi** per aggiungere la connessione. Modificare il tipo di connessione per riflettere una connessione da rete virtuale a rete virtuale (in caso di connessione a un altro gateway di rete virtuale) o da sito a sito.
3. Se si usa una connessione da sito a sito e non si è ancora creato un gateway di rete locale per il sito a cui ci si vuole connettere, è possibile crearne uno nuovo.
4. Specificare la chiave condivisa da usare e quindi fare clic su **OK** per creare la connessione.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su BGP, vedere [Panoramica di BGP](vpn-gateway-bgp-overview.md) e [Come configurare BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Per informazioni sul tunneling forzato, vedere [Informazioni sul tunneling forzato](vpn-gateway-forced-tunneling-rm.md).
* Per informazioni sulle connessioni da rete attiva a rete attiva a disponibilità elevata, vedere [Connettività cross-premise e da rete virtuale a rete virtuale a disponibilità elevata](vpn-gateway-highlyavailable.md).
* Per informazioni su come limitare il traffico di rete verso le risorse in una rete virtuale, vedere [Sicurezza di rete](../virtual-network/security-overview.md).
* Per informazioni sul modo in cui Azure instrada il traffico tra Azure, l'ambiente locale e le risorse Internet, vedere [Routing del traffico di rete virtuale](../virtual-network/virtual-networks-udr-overview.md).
* Per informazioni sulla creazione di una connessione VPN da sito a sito con un modello di Azure Resource Manager, vedere [Create a Site-to-Site VPN Connection](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/) (Creare una connessione VPN da sito a sito).
* Per informazioni sulla creazione di una connessione VPN da rete virtuale a rete virtuale con un modello di Azure Resource Manager, vedere [Deploy HBase geo replication](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/) (Distribuire la replica geografica di HBase).
