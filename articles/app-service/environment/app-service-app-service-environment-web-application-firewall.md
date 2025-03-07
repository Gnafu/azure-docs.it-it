---
title: Configurare un web application firewall (WAF) per l'ambiente del servizio app di Azure
description: Informazioni su come configurare un Web application firewall davanti all'ambiente del servizio app.
services: app-service\web
documentationcenter: ''
author: naziml
manager: erikre
editor: jimbe
ms.assetid: a2101291-83ba-4169-98a2-2c0ed9a65e8d
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/03/2018
ms.author: naziml
ms.custom: seodec18
ms.openlocfilehash: 01224e4270ba8a7c7df4a311823dd6156038438a
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070056"
---
# <a name="configuring-a-web-application-firewall-waf-for-app-service-environment"></a>Configurazione di un Web application firewall (WAF) per l'ambiente del servizio app
## <a name="overview"></a>Panoramica

I Web application firewall (WAF) consentono di proteggere le applicazioni Web controllando il traffico Web in ingresso per bloccare SQL injection, attacchi tramite script da altri siti, caricamenti di malware, DDoS di applicazioni e altri attacchi. Esaminano anche le risposte provenienti dai server Web back-end per la prevenzione della perdita dei dati. In associazione all'isolamento e alla scalabilità aggiuntiva fornita dagli ambienti del servizio app, costituisce un ambiente ideale per le applicazioni Web critiche per l'azienda, che devono sostenere richieste dannose e un volume elevato di traffico. Azure offre una funzionalità WAF con il [gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction).  Per informazioni su come integrare l'ambiente del servizio app con un gateway applicazione, vedere il documento [Integrare l'ambiente del servizio app con bilanciamento del carico interno con un gateway applicazione](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway).

In [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf-byol/) sono disponibili più opzioni oltre al gateway applicazione di Azure, ad esempio [Barracuda WAF per Azure](https://www.barracuda.com/programs/azure). La parte restante di questo documento illustra come integrare l'ambiente del servizio app con un dispositivo Barracuda WAF.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="setup"></a>Configurazione
Per questo documento si configurerà l'ambiente del servizio app dietro più istanze con carico bilanciato di Barracuda WAF, in modo che solo il traffico proveniente dal firewall WAF possa raggiungere l'ambiente del servizio app che non sarà accessibile dalla rete perimetrale. Gestione traffico di Azure si trova invece davanti alle istanze di Barracuda WAF per bilanciare il carico tra i data center e le aree di Azure. Il diagramma generale della configurazione sarà simile all'immagine seguente:

![Architettura][Architecture] 

> [!NOTE]
> Con l'introduzione del [supporto del bilanciamento del carico interno per l'ambiente del servizio app](app-service-environment-with-internal-load-balancer.md) è possibile configurare l'ambiente del servizio app in modo che risulti inaccessibile dalla rete perimetrale e sia disponibile solo per la rete privata. 
> 
> 

## <a name="configuring-your-app-service-environment"></a>Configurazione dell'ambiente del servizio app
Per configurare un ambiente del servizio app, fare riferimento alla [documentazione](app-service-web-how-to-create-an-app-service-environment.md) sull'argomento. Dopo aver creato un ambiente del servizio app, è possibile creare in questo ambiente le app Web, le app per le API e le [app per dispositivi mobili](../../app-service-mobile/app-service-mobile-value-prop.md) che saranno protette dietro il firewall WAF che verrà configurato nella sezione successiva.

## <a name="configuring-your-barracuda-waf-cloud-service"></a>Configurazione del servizio Cloud Barracuda WAF
Sul sito Barracuda è disponibile un [articolo dettagliato](https://campus.barracuda.com/product/webapplicationfirewall/article/WAF/DeployWAFInAzure) sulla distribuzione del firewall WAF in una macchina virtuale in Azure. Poiché tuttavia quello che si vuole è la ridondanza senza introdurre un singolo punto di guasto, è opportuno distribuire almeno due VM con istanze WAF nello stesso servizio cloud quando si seguono queste istruzioni.

### <a name="adding-endpoints-to-cloud-service"></a>Aggiunta di endpoint al servizio cloud
Dopo aver creato 2 o più istanze di macchine virtuali WAF nel servizio cloud, è possibile usare il [portale di Azure](https://portal.azure.com/) per aggiungere endpoint HTTP e HTTPS usati dall'applicazione, come illustrato nell'immagine seguente:

![Configurare l'endpoint][ConfigureEndpoint]

Se le applicazioni usano altri endpoint, assicurarsi di aggiungerli tutti a questo elenco. 

### <a name="configuring-barracuda-waf-through-its-management-portal"></a>Configurazione di Barracuda WAF tramite il relativo portale di gestione
Barracuda WAF usa la porta TCP 8000 per la configurazione tramite il proprio portale di gestione. Se sono presenti più istanze di VM WAF, è necessario ripetere questi passaggi per ogni istanza di VM. 

> [!NOTE]
> Dopo aver completato la configurazione WAF, rimuovere l'endpoint TCP/8000 da tutte le VM WAF per mantenere protetto il firewall WAF.
> 
> 

Aggiungere l'endpoint di gestione, come illustrato nell'immagine seguente, per configurare Barracuda WAF.

![Aggiungere l'endpoint di gestione][AddManagementEndpoint]

Usare un browser per passare all'endpoint di gestione nel servizio cloud. Se il servizio cloud è denominato test.cloudapp.net, si accederà a questo endpoint passando a `http://test.cloudapp.net:8000`. Dovrebbe essere visualizzata una pagina di accesso come quella illustrata nell'immagine seguente, a cui è possibile accedere usando le credenziali specificate in fase di configurazione delle VM WAF.

![Pagina di accesso gestione][ManagementLoginPage]

Dopo avere eseguito l'accesso, dovrebbe essere visualizzato un dashboard come quello nell'immagine seguente che presenterà le statistiche di base sulla protezione WAF.

![Dashboard di gestione][ManagementDashboard]

Fare clic sulla scheda **Services** (Servizi) per configurare il firewall WAF per i servizi protetti. Per altri dettagli sulla configurazione di Barracuda WAF, vedere la [relativa documentazione](https://techlib.barracuda.com/waf/getstarted1). Nell'esempio seguente è stata configurata un'app del servizio app che gestisce il traffico HTTP e HTTPS.

![Aggiunta di servizi di gestione][ManagementAddServices]

> [!NOTE]
> A seconda di come sono configurate le applicazioni e di quali funzionalità sono in uso nell'ambiente del servizio app, è necessario inoltrare il traffico a porte TCP diverse dalla 80 e dalla 443, ad esempio in caso di configurazione di IP SSL per un'app del servizio app. Per un elenco di porte di rete usate negli ambienti del servizio app, vedere la sezione Porte di rete della [documentazione sul controllo del traffico in ingresso](app-service-app-service-environment-control-inbound-traffic.md).
> 
> 

## <a name="configuring-microsoft-azure-traffic-manager-optional"></a>Configurazione di Gestione traffico di Microsoft Azure (FACOLTATIVO)
Se l'applicazione è disponibile in più aree, è preferibile bilanciarne il carico dietro [Gestione traffico di Azure](../../traffic-manager/traffic-manager-overview.md). A questo scopo, è possibile aggiungere un endpoint nel [portale di Azure](https://portal.azure.com) usando il nome del servizio cloud per WAF nel profilo di Gestione traffico come illustrato nell'immagine seguente. 

![Endpoint di Gestione traffico][TrafficManagerEndpoint]

Se l'applicazione richiede l'autenticazione, assicurarsi di disporre di qualche risorsa che non richiede alcuna autenticazione per consentire a Gestione traffico di effettuare il ping per la disponibilità dell'applicazione. È possibile configurare l'URL nella pagina **Configurazione** del [portale di Azure](https://portal.azure.com) come illustrato nella figura seguente:

![Configurare Gestione traffico][ConfigureTrafficManager]

Per inoltrare i ping di Gestione traffico dal firewall WAF all'applicazione, è necessario configurare Website Translations in Barracuda WAF per inoltrare il traffico all'applicazione, come illustrato nell'esempio seguente:

![Website Translations][WebsiteTranslations]

## <a name="securing-traffic-to-app-service-environment-using-network-security-groups-nsg"></a>Protezione del traffico verso l'ambiente del servizio app con i gruppi di sicurezza di rete (NSG)
Attenersi alla [documentazione sul controllo del traffico in ingresso](app-service-app-service-environment-control-inbound-traffic.md) per informazioni dettagliate sulla limitazione del traffico all'ambiente del servizio app dal firewall WAF usando solo l'indirizzo VIP del servizio cloud. Ecco un comando di Powershell di esempio per eseguire questa attività per la porta TCP 80.

    Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP

Sostituire SourceAddressPrefix con l'indirizzo IP virtuale (VIP) del servizio cloud del firewall WAF.

> [!NOTE]
> L'indirizzo VIP del servizio cloud cambia quando si elimina e si ricrea il servizio cloud. In questo caso, assicurarsi di aggiornare l'indirizzo IP nel gruppo di risorse di rete. 
> 
> 

<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png
