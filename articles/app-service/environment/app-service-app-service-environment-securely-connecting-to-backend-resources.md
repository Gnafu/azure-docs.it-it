---
title: Connettersi in modo sicuro alle risorse back-end da un ambiente del servizio app - Azure
description: Informazioni su come connettersi in modo sicuro alle risorse back-end da un ambiente del servizio app.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: adb7c246a9f8c8d202d45b58f4d22eeb8d51a773
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70069970"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>Connettersi in modo sicuro alle risorse back-end da un ambiente del servizio app
## <a name="overview"></a>Panoramica
Poiché un ambiente del servizio app viene sempre creato in una rete virtuale Azure Resource Manager **o** in una [rete virtuale][virtualnetwork]del modello di distribuzione classica, le connessioni in uscita da un ambiente del servizio app ad altre risorse back-end può fluire esclusivamente sulla rete virtuale.  Con una modifica recente apportata a giugno 2016, gli ambienti del servizio app possono essere distribuiti nelle reti virtuali che usano intervalli di indirizzi pubblici o spazi di indirizzi RFC1918, ovvero indirizzi privati.  

Ad esempio, potrebbe essere in esecuzione un'istanza di SQL Server in un cluster di macchine virtuali con la porta 1433 bloccata.  In base all'elenco di controllo di accesso definito per l'endpoint, potrebbe essere consentito solo l'accesso da altre risorse nella stessa rete virtuale.  

Come altro esempio, gli endpoint sensibili possono essere eseguiti in locale ed essere connessi ad Azure tramite connessioni da [sito a sito][SiteToSite] o [Azure ExpressRoute][ExpressRoute] .  In questo caso, solo le risorse nelle reti virtuali connesse ai tunnel da sito a sito o ExpressRoute potrebbero accedere agli endpoint locali.

Per tutti questi scenari, le app in esecuzione in un ambiente del servizio app potranno connettersi in modo sicuro ai server e alle risorse.  Il traffico in uscita dalle app in esecuzione in un ambiente del servizio app agli endpoint privati nella stessa rete virtuale (o connessi alla stessa rete virtuale) transiterà solo attraverso la rete virtuale.  Il traffico in uscita agli endpoint privati non transiterà attraverso la rete Internet pubblica.

Si noti che un'eccezione è rappresentata dal traffico in uscita da un ambiente del servizio app agli endpoint all'interno di una rete virtuale.  Gli ambienti del servizio app non riescono a raggiungere gli endpoint delle macchine virtuali all'interno della **stessa** subnet dell'ambiente del servizio app.  Questo normalmente non costituisce un problema, purché gli ambienti del servizio app vengano distribuiti in una subnet riservata a uso esclusivo dell'ambiente del servizio app.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Requisiti per DNS e connettività in uscita
Per un corretto funzionamento dell'ambiente del servizio app, è necessario l'accesso in uscita ai vari endpoint. Un elenco completo degli endpoint esterni usati da un ambiente del servizio app è disponibile nella sezione "Requisiti della connettività di rete" dell'articolo [Configurazione di rete per ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

Gli ambienti del servizio app richiedono un'infrastruttura DNS valida configurata per la rete virtuale.  Se per qualsiasi motivo viene modificata la configurazione DNS dopo aver creato un ambiente di servizio app, gli sviluppatori possono forzare un ambiente di servizio app per selezionare la nuova configurazione del DNS.  L'attivazione di un riavvio di ambiente in sequenza mediante l'icona "Riavvia" posizionata nella parte superiore del pannello di gestione dell'ambiente del servizio app nel portale farà sì che l'ambiente selezioni la nuova configurazione del DNS.

È anche consigliabile che i server DNS personalizzati nella rete virtuale vengano configurati prima di creare un ambiente del servizio app.  Se la configurazione DNS della rete virtuale viene modificata durante la creazione di un ambiente del servizio app, il processo di creazione dell'ambiente del servizio app avrà esito negativo.  In modo analogo, se esiste un server DNS personalizzato nell’altra estremità di un gateway VPN e il server DNS è irraggiungibile o non disponibile, anche il processo di creazione dell’ambiente del servizio App avrà esito negativo.

## <a name="connecting-to-a-sql-server"></a>Connessione a un'istanza di SQL Server
Una configurazione di SQL Server comune prevede un endpoint in ascolto sulla porta 1433:

![Endpoint di SQL Server][SqlServerEndpoint]

È possibile usare due approcci per limitare il traffico a questo endpoint:

* [Elenchi di controllo di accesso di rete][NetworkAccessControlLists] (ACL di rete)
* [Gruppi di sicurezza di rete][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Limitazione dell'accesso con un elenco di controllo di accesso di rete
È possibile proteggere la porta 1433 usando un elenco di controllo di accesso di rete.  L'esempio seguente illustra come consentire gli indirizzi client originati dall'interno di una rete virtuale e bloccare l'accesso a tutti gli altri client.

![Esempio di elenco di controllo di accesso di rete (ACL)][NetworkAccessControlListExample]

Tutte le applicazioni in esecuzione nell'ambiente del servizio app all'interno della stessa rete virtuale di SQL Server potranno connettersi all'istanza di SQL Server usando l'indirizzo IP **interno della rete virtuale** per la macchina virtuale SQL Server.  

La stringa di connessione di esempio seguente fa riferimento all'instanza di SQL Server usando l'indirizzo IP privato.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Sebbene la macchina virtuale disponga anche di un endpoint pubblico, i tentativi di connessione usando l'indirizzo IP pubblico verranno rifiutati a causa dell'elenco di controllo di accesso di rete. 

## <a name="restricting-access-with-a-network-security-group"></a>Limitazione dell'accesso con un gruppo di sicurezza di rete
In alternativa, per proteggere l'accesso è possibile usare un gruppo di sicurezza di rete.  I gruppi di sicurezza di rete possono essere applicati alle singole macchine virtuali o a una subnet contenente macchine virtuali.

È prima necessario creare un gruppo di sicurezza di rete:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

La limitazione dell'accesso al solo traffico interno della rete virtuale con un gruppo di sicurezza di rete è un'operazione estremamente semplice.  Le regole predefinite in un gruppo di sicurezza di rete consentono l'accesso solo da altri client di rete nella stessa rete virtuale.

Di conseguenza, è possibile bloccare l'accesso a SQL Server semplicemente applicando un gruppo di sicurezza di rete con le relative regole predefinite alle macchine virtuali che eseguono SQL Server o alla subnet contenente le macchine virtuali.

L'esempio seguente mostra come applicare un gruppo di sicurezza di rete alla subnet contenente le macchine virtuali:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'

Il risultato finale è costituito da un set di regole di sicurezza che blocca l'accesso esterno e consente l'accesso interno al traffico proveniente dalla rete virtuale:

![Regole di sicurezza di rete predefinite][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Introduzione
Per iniziare a usare gli ambienti del servizio app, vedere [Introduzione all'ambiente del servizio app][IntroToAppServiceEnvironment]

Per informazioni dettagliate su come controllare il traffico in ingresso verso la ambiente del servizio app, vedere [controllo del traffico in ingresso verso un ambiente del servizio app][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png 
