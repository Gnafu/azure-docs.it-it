---
title: Informazioni sulle connessioni VPN da punto a sito di Azure | Microsoft Docs
description: Questo articolo descrive le connessioni da punto a sito e consente di stabilire quale tipo di autenticazione gateway VPN da punto a sito usare.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: cherylmc
ms.openlocfilehash: f1e014bb14b2b5c1ae924f4371e08aa8bf8698f2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056489"
---
# <a name="about-point-to-site-vpn"></a>Informazioni sulla VPN da punto a sito

Una connessione gateway VPN da punto a sito (P2S) consente di creare una connessione sicura alla rete virtuale da un singolo computer client. Una connessione da punto a sito viene stabilita avviandola dal computer client. Questa soluzione è utile per i telelavoratori che intendono connettersi alle reti virtuali di Azure da una posizione remota, ad esempio da casa o durante una riunione. Una VPN da punto a sito è anche una soluzione utile da usare al posto di una VPN da sito a sito quando solo pochi client devono connettersi a una rete virtuale. Questo articolo si applica al modello di distribuzione di Azure Resource Manager.

## <a name="protocol"></a>Protocollo usato nelle connessioni da punto a sito

Per la VPN da punto a sito può essere usato uno dei protocolli seguenti:

* **Protocollo® OpenVPN**, protocollo VPN basato su un SSL/TLS. Una soluzione VPN SSL può penetrare i firewall, poiché la maggior parte dei firewall apre la porta TCP 443 in uscita, usata da SSL. OpenVPN può essere usato per connettersi da Android, iOS (versioni 11.0 e versioni successive), i dispositivi Windows, Linux e Mac (versioni OSX 10.13 e versioni successive).

* Secure Socket Tunneling Protocol (SSTP), un protocollo VPN di proprietà basato su SSL. Una soluzione VPN SSL può penetrare i firewall, poiché la maggior parte dei firewall apre la porta TCP 443 in uscita, usata da SSL. SSTP è supportato solo nei dispositivi Windows. Azure supporta tutte le versioni di Windows che hanno SSTP (Windows 7 e versioni successive).

* VPN IKEv2, una soluzione VPN IPsec basata su standard. VPN IKEv2 può essere usato per connettersi da dispositivi Mac (versioni OSX 10.11 e successive).


>[!NOTE]
>IKEv2 e OpenVPN per la connessione da punto a sito sono disponibili solo per il modello di distribuzione Resource Manager. Non sono disponibili per il modello di distribuzione classica.
>

## <a name="authentication"></a>Modalità di autenticazione del client VPN da punto a sito

L'utente deve essere autenticato prima che Azure possa accettare una connessione VPN da punto a sito. Azure offre due meccanismi per autenticare un utente che esegue la connessione.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Autenticazione del certificato nativa di Azure

Quando si usa l'autenticazione del certificato nativa di Azure, per autenticare l'utente che esegue la connessione viene usato un certificato client presente nel dispositivo. I certificati client vengono generati da un certificato radice attendibile e quindi installati in ogni computer client. È possibile usare un certificato radice generato tramite una soluzione aziendale oppure generare un certificato autofirmato.

La convalida del certificato client viene eseguita dal gateway VPN quando viene stabilita la connessione VPN da punto a sito. Il certificato radice è necessario per la convalida e deve essere caricato in Azure.

### <a name="authenticate-using-active-directory-ad-domain-server"></a>Autenticazione con server di dominio Active Directory (AD)

L'autenticazione con un dominio di AD consente agli utenti di connettersi ad Azure usando le credenziali di dominio dell'organizzazione. Richiede un server RADIUS che si integra con il server AD. Le organizzazioni possono anche sfruttare la distribuzione RADIUS esistente.   
  
Il server RADIUS può essere distribuito in locale o nella rete virtuale di Azure. Durante l'autenticazione, il gateway VPN di Azure funge da pass-through e inoltra i messaggi di autenticazione tra il server RADIUS e il dispositivo che si connette e viceversa. È quindi importante che il gateway possa raggiungere il server RADIUS. Se il server RADIUS si trova in locale, per la raggiungibilità è necessaria una connessione VPN da sito a sito da Azure al sito locale.  
  
È anche possibile integrare il server RADIUS con Servizi certificati AD. Ciò consente di usare il server RADIUS e la distribuzione di certificati dell'organizzazione per l'autenticazione del certificato da punto a sito in alternativa all'autenticazione del certificato di Azure. Il vantaggio è che non è necessario caricare i certificati radice e i certificati revocati in Azure.

Un server RADIUS può anche integrarsi con altri sistemi di identità esterni, offrendo così molte opzioni di autenticazione per le VPN da punto a sito, incluse le opzioni a più fattori.

>[!NOTE]
>**Protocollo® OpenVPN** non è supportata con l'autenticazione RADIUS.
>

![Da punto a sito](./media/point-to-site-about/p2s.png "Point-to-Site")

## <a name="what-are-the-client-configuration-requirements"></a>Requisiti di configurazione per i client

>[!NOTE]
>Per i client Windows, è necessario avere i diritti di amministratore nel dispositivo client per avviare la connessione VPN dal dispositivo client ad Azure.
>

Gli utenti usano i client VPN nativi nei dispositivi Windows e Mac per la connessione da punto a sito. Azure fornisce un file ZIP per la configurazione del client VPN contenente le impostazioni necessarie per la connessione di questi client nativi ad Azure.

* Per i dispositivi Windows, la configurazione del client VPN è costituita da un pacchetto di installazione che gli utenti installano nei propri dispositivi.
* Per i dispositivi Mac è costituita dal file mobileconfig che gli utenti installano nei propri dispositivi.

Il file ZIP fornisce anche i valori di alcune impostazioni importanti sul lato Azure che è possibile usare per creare il proprio profilo per questi dispositivi. Alcuni dei valori includono l'indirizzo del gateway VPN, i tipi di tunnel configurati, le route e il certificato radice per la convalida del gateway.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="gwsku"></a>Quali SKU del gateway supportano la connessione VPN da punto a sito?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Per consigli sugli SKU del gateway, vedere [Informazioni sulle impostazioni del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>Lo SKU Basic non supporta l'autenticazione IKEv2 o RADIUS.
>

## <a name="IKE/IPsec policies"></a>Quali criteri IKE/IPsec sono configurati in un gateway VPN per P2S?


**IKEv2**

|**Pacchetti di crittografia** | **Integrità** | **PRF** | **Gruppo DH** |
|---        | ---           | ---       | ---   |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_24 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_14 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_ECP384 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_ECP256 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_24 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_14 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_ECP384 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_ECP256 |
|AES256     |   SHA384      | SHA384    | GROUP_24 |
|AES256     |   SHA384      | SHA384    | GROUP_14 |
|AES256     |   SHA384      | SHA384    | GROUP_ECP384 |
|AES256     |   SHA384      | SHA384    | GROUP_ECP256 |
|AES256     |   SHA256      | SHA256    | GROUP_24 |
|AES256     |   SHA256      | SHA256    | GROUP_14 |
|AES256     |   SHA256      | SHA256    | GROUP_ECP384 |
|AES256     |   SHA256      | SHA256    | GROUP_ECP256 |
|AES256     |   SHA256      | SHA256    | GROUP_2 |

**IPsec**

|**Pacchetti di crittografia** | **Integrità** | **Gruppo PFS** |
|---        | ---           | ---       |
|GCM_AES256 | GCM_AES256 | GROUP_NONE |
|GCM_AES256 | GCM_AES256 | GROUP_24 |
|GCM_AES256 | GCM_AES256 | GROUP_14 |
|GCM_AES256 | GCM_AES256 | GROUP_ECP384 |
|GCM_AES256 | GCM_AES256 | GROUP_ECP256 |
| AES256    | SHA256 | GROUP_NONE |
| AES256    | SHA256 | GROUP_24 |
| AES256    | SHA256 | GROUP_14 |
| AES256    | SHA256 | GROUP_ECP384 |
| AES256    | SHA256 | GROUP_ECP256 |
| AES256    | SHA1 | GROUP_NONE |

## <a name="TLS policies"></a>Quali criteri TLS sono configurati in un gateway VPN per P2S?
**TLS**

|**Criteri** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |




## <a name="configure"></a>Come si configura una connessione da punto a sito?

La configurazione di una connessione da punto a sito richiede alcuni passaggi specifici. Gli articoli seguenti illustrano le procedure di configurazione di una connessione da punto a sito e contengono collegamenti per la configurazione dei dispositivi client VPN:

* [Configurare una connessione da punto a sito usando l'autenticazione RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurare una connessione da punto a sito usando l'autenticazione del certificato nativa di Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Configurare OpenVPN](vpn-gateway-howto-openvpn.md)

## <a name="how-do-i-remove-the-configuration-of-a-p2s-connection"></a>Come si rimuove la configurazione di una connessione P2S?

È possibile rimuovere una configurazione P2S con az cli e il comando seguente: 

`az network vnet-gateway update --name <gateway-name> --resource-group <resource-group name> --remove "vpnClientConfiguration"`
 
## <a name="faqcert"></a>Domande frequenti per l'autenticazione del certificato di Azure nativo

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faqradius"></a>Domande frequenti per l'autenticazione RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Fasi successive

* [Configurare una connessione da punto a sito usando l'autenticazione RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurare una connessione da punto a sito usando l'autenticazione del certificato nativa di Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" è un marchio registrato di OpenVPN Inc.**
