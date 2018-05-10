---
title: Firewall di Azure Stack pianificazione per Azure Stack integrati sistemi | Documenti Microsoft
description: Descrive le considerazioni firewall di Azure Stack per le distribuzioni di Azure Stack Azure connesse a più nodi.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 3d8a02cc540251d77b61cf0e5b7e2aa8292f1f5f
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="azure-stack-firewall-integration"></a>Integrazione con firewall di Azure Stack
È consigliabile che per utilizzare un dispositivo firewall sicura dello Stack di Azure. Sebbene i firewall consente ad esempio attacchi distribuiti di tipo denial of service (DDOS), il rilevamento delle intrusioni e l'ispezione del contenuto, possono anche diventare un collo di bottiglia della velocità effettiva per servizi di archiviazione di Azure come BLOB, tabelle e code.

In base al modello di identità Azure Active Directory (Azure AD) o Windows Server Active Directory Federation Services (ADFS), potrebbe essere necessario pubblicare l'endpoint ADFS. Se viene utilizzata una modalità di distribuzione disconnesso, è necessario pubblicare l'endpoint ADFS. Per ulteriori informazioni, vedere il [articolo identità di integrazione con Data Center](azure-stack-integrate-identity.md).

Gli endpoint di chiave dell'insieme di credenziali (amministratore), Gestione risorse di Azure (amministratore) e portale dell'amministratore non richiedono necessariamente pubblicazione esterna. Ad esempio, come un provider di servizi è limitare la superficie di attacco e amministrare solo Stack di Azure all'interno della rete e non da internet.

Per le organizzazioni aziendali, la rete esterna può essere la rete azienda esistente. In questo caso, è necessario pubblicare gli endpoint per il funzionamento di Azure Stack dalla rete aziendale.

### <a name="network-address-translation"></a>Network Address Translation
Network Address Translation (NAT) è il metodo consigliato per consentire la macchina virtuale di distribuzione DVM () per accedere alle risorse esterne e internet durante la distribuzione, nonché le macchine virtuali alla Console di ripristino di emergenza (ERCS) o con privilegi punto finale (PEP) durante registrazione e risoluzione dei problemi.

NAT può anche essere un'alternativa a indirizzi IP pubblici nella rete esterna o VIP pubblici. Tuttavia, è consigliabile non eseguire questa operazione perché limita l'esperienza utente tenant e aumenta la complessità. Le due opzioni sarebbe un NAT 1:1 richiede ancora un indirizzo IP pubblico per ogni indirizzo IP utente nel pool o NAT che richiede una regola NAT per ogni utente VIP che contiene le associazioni per tutte le porte di che un utente può utilizzare molti: 1.

Alcuni degli svantaggi dell'utilizzo di NAT per VIP pubblico sono:
- NAT comporta un overhead aggiuntivo per la gestione di regole del firewall perché gli utenti di controllare i propri endpoint e le proprie regole di pubblicazione definita dal software (SDN) dello stack di rete. Gli utenti devono contattare l'operatore di Stack di Azure per ottenere i relativi indirizzi IP virtuali pubblicate e per aggiornare l'elenco delle porte.
- Durante l'utilizzo NAT limita l'esperienza utente, offre controllo completo all'operatore tramite le richieste di pubblicazione.
- Per scenari di cloud ibrido con Azure, è consigliabile che Azure non supporta la configurazione di un tunnel VPN a un endpoint tramite NAT.

### <a name="ssl-decryption"></a>Decrittografia SSL
Attualmente si consiglia di disabilitare la decrittografia SSL per tutto il traffico di Azure Stack. Se è supportata negli aggiornamenti futuri, è verranno fornite istruzioni su come abilitare la decrittografia SSL per lo Stack di Azure.

## <a name="edge-firewall-scenario"></a>Scenario di firewall perimetrale
In una distribuzione di bordo, Stack di Azure viene distribuito direttamente dietro il router perimetrale o il firewall. In questi scenari, è supportata per il firewall essere sopra il bordo (Scenario 1) in cui supporta sia le configurazioni dei firewall attivo-attivo e attivo-passivo o che funge da dispositivo bordo (Scenario 2) in cui supporta solo firewall attivo-attivo configurazione basarsi su uguale Cost Multi Path ECMP () con BGP o routing statico per il failover.

In genere, gli indirizzi IP instradabili pubblici sono specificati per il pool di indirizzi VIP pubblico dalla rete esterna in fase di distribuzione. In uno scenario di edge, è consigliabile non utilizzare indirizzi IP instradabile pubblici su rete per motivi di sicurezza. Questo scenario consente a un utente provare l'esperienza del cloud self-controllata completo come in un cloud pubblico come Azure.  

![Esempio di firewall perimetrale Stack Azure](.\media\azure-stack-firewall\edge-firewall-scenario.png)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Intranet o perimetrale rete firewall scenario aziendale
In una distribuzione aziendale intranet o perimetrale dello Stack di Azure viene distribuito su un firewall multi-suddiviso in zone o tra il firewall perimetrale e il firewall della rete aziendale interna. Il traffico viene quindi distribuito tra la rete perimetrale, protetta (o DMZ) e le zone non protette come descritto di seguito:

- **Un'area protetta**: si tratta di rete interna che usa indirizzi IP instradabili interni o aziendali. Rete sicura può essere suddiviso, hanno accesso in uscita a internet tramite NAT, firewall e viene in genere accessibile da un punto qualsiasi all'interno del datacenter attraverso la rete interna. Tutte le reti di Azure Stack devono trovarsi in un'area protetta, ad eccezione di pool di indirizzi VIP pubblico della rete esterna.
- **Area perimetrale**. La rete perimetrale è dove esterno o applicazioni come server Web sono in genere distribuiti per internet. In genere viene monitorato da un firewall per evitare attacchi DDoS e intrusione (pirateria informatica) consentendo il traffico in ingresso specificato da internet. Solo il rete esterna pubblico pool VIP di Azure Stack deve trovarsi nell'area di rete Perimetrale.
- **Zona non sicuro**. Questa è la rete esterna, internet. Si **non** consigliabile distribuire Azure Stack della zona non sicuro.

![Esempio di rete perimetrale Stack Azure](.\media\azure-stack-firewall\perimeter-network-scenario.png)

## <a name="learn-more"></a>Altre informazioni
Altre informazioni, vedere [porte e protocolli utilizzati dagli endpoint di Azure Stack](azure-stack-integrate-endpoints.md).

## <a name="next-steps"></a>Passaggi successivi
[Requisiti di infrastruttura a chiave pubblica dello Stack di Azure](azure-stack-pki-certs.md)

