---
title: Informazioni su App per dispositivi mobili nel servizio app di Azure
description: Informazioni sui vantaggi che il servizio app può apportare alle app per dispositivi mobili aziendali.
services: app-service\mobile
documentationcenter: ''
author: elamalani
manager: yochayk
editor: ''
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: a3817b3e9f62a863f3fea9305ef169db2eb84491
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637954"
---
# <a name="getting-started"> </a>Informazioni su App per dispositivi mobili nel servizio app di Azure

> [!NOTE]
> Visual Studio App Center sta investendo nei servizi nuovi e integrati centrali per lo sviluppo di app per dispositivi mobili. Gli sviluppatori possono utilizzare i servizi di **compilazione**, **test** e **distribuzione** per configurare la pipeline di integrazione e recapito continua. Una volta distribuita l'app, gli sviluppatori possono monitorare lo stato e l'utilizzo dell'app usando i servizi di **analisi** e **diagnostica** e coinvolgere gli utenti che usano il servizio di **push** . Gli sviluppatori possono inoltre sfruttare l' **autenticazione** per autenticare gli utenti e il servizio **dati** per salvare in modo permanente e sincronizzare i dati delle app nel cloud. Estrai [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-value-prop) oggi stesso.
>

Il servizio app di Azure è un'offerta di [piattaforma distribuita come servizio ](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) interamente gestita per sviluppatori professionisti. Il servizio offre un set completo di funzionalità per scenari Web, per dispositivi mobili e di integrazione. 

La funzionalità App per dispositivi mobili del servizio app di Azure offre a integratori di sistemi e sviluppatori aziendali una piattaforma di sviluppo di applicazioni per dispositivi mobili con scalabilità elevata e disponibilità globale.

![Panoramica visiva delle funzionalità di App per dispositivi mobili](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>Perché le app per dispositivi mobili?
Con la funzionalità App per dispositivi mobili, è possibile:

* **Compilare app native e multipiattaforma**: sia che si compilino app native iOS, Android e Windows o app multipiattaforma Xamarin o Cordova (PhoneGap), è possibile sfruttare il servizio app usando SDK nativi.
* **Connettersi ai sistemi aziendali**: con la funzionalità App per dispositivi mobili, è possibile aggiungere l'accesso aziendale in pochi minuti e connettersi alle risorse aziendali locali o nel cloud.
* **Compilare app offline con sincronizzazione dei dati**: è possibile aumentare la produttività della forza lavoro mobile compilando app eseguibili offline e usando App per dispositivi mobili per sincronizzare i dati in background quando è disponibile la connettività con qualsiasi origine dati o API SaaS (Software as a Service) aziendale.
* **Inviare notifiche push a milioni di utenti in pochi secondi**: è possibile coinvolgere i clienti con notifiche push immediate su qualsiasi dispositivo, personalizzate in base alle specifiche esigenze e inviate al momento giusto.

## <a name="mobile-apps-features"></a>Funzionalità di App per dispositivi mobili
Le funzionalità seguenti sono importanti per lo sviluppo per dispositivi mobili abilitati per il cloud:

* **Autenticazione e autorizzazione**: supporto provider di identità, tra cui Azure Active Directory per l'autenticazione aziendale, nonché provider di servizi di social networking come Facebook, Google, Twitter e account Microsoft. App per dispositivi mobili offre un servizio OAuth 2.0 per ogni provider. È anche possibile integrare l'SDK del provider di identità per funzionalità specifiche del provider.

    Altre informazioni sulle [funzionalità di autenticazione].

* **Accesso ai dati**: App per dispositivi mobili offre un'origine dati OData v3 ottimizzata per dispositivi mobili collegata al database SQL di Azure o a un'istanza locale di SQL Server. Dato che questo servizio può essere basato su Entity Framework, consente una facile integrazione con altri provider di dati NoSQL e SQL, tra cui [Archivio tabelle di Azure], MongoDB e [Azure Cosmos DB], e provider di API SaaS come Office 365 e Salesforce.com.

* **Sincronizzazione offline**: gli SDK client facilitano la compilazione di applicazioni per dispositivi mobili solide e reattive che funzionano con un set di dati offline. Il set di dati può essere sincronizzato con i dati back-end, con supporto della risoluzione dei conflitti.

  Altre informazioni sulle [funzionalità dati].

* **Notifiche push**: gli SDK client si integrano facilmente con le funzionalità di registrazione di Hub di notifica di Azure, consentendo di inviare notifiche push a milioni di utenti contemporaneamente.

  Altre informazioni sulle [funzionalità di notifica push].

* **Client SDK**: è disponibile un set completo di SDK client che coprono lo sviluppo nativo ([iOS], [Android] e [Windows]), multipiattaforma ([Xamarin.iOS, Xamarin.Android] e [Xamarin.Forms]) e di applicazioni ibride ([Apache Cordova]). Ogni SDK client è disponibile con una licenza MIT ed è open source.

## <a name="azure-app-service-features"></a>Funzionalità del servizio app di Azure
Le funzionalità della piattaforma seguenti sono utili per i siti di produzione per dispositivi mobili:

* **Ridimensionamento automatico**: il servizio app consente di aumentare rapidamente le prestazioni o il numero di istanze per gestire qualsiasi carico di lavoro in ingresso dei clienti. È possibile selezionare manualmente il numero e le dimensioni delle VM o configurare la scalabilità automatica per ridimensionare il back-end delle app per dispositivi mobili in base al carico o alla pianificazione.

  Altre informazioni sulla [scalabilità automatica].

* **Ambienti di staging**: il servizio app può eseguire più versioni del sito e consente così di eseguire test A/B, test in ambiente di produzione nell'ambito di un piano DevOps più ampio e staging sul posto di un nuovo back-end.

  Altre informazioni sugli [ambienti di staging].

* **Distribuzione continua**: il servizio app può integrarsi con i sistemi comuni per la gestione del _controllo del codice sorgente_ e consente così di distribuire facilmente una nuova versione del back-end.

  Altre informazioni sulle [opzioni di distribuzione](../app-service/deploy-local-git.md).

* **Reti virtuali**: il servizio app può connettersi alle risorse locali usando una rete virtuale, Azure ExpressRoute o connessioni ibride.

  Altre informazioni su [connessioni ibride], [reti virtuali] ed [ExpressRoute].

* **Ambienti isolati e dedicati**: per eseguire in modo sicuro le app del Servizio app di Azure, è possibile eseguire il servizio app in un ambiente completamente isolato e dedicato. Un ambiente di questo tipo è ideale per i carichi di lavoro delle applicazioni che richiedono scalabilità elevata, isolamento o accesso alla rete sicuro.

  Altre informazioni sugli [ambienti del servizio app].

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare App per dispositivi mobili nel servizio app di Azure, completare l'[esercitazione introduttiva]. L'esercitazione illustra le nozioni di base della creazione di un back-end e un client per dispositivi mobili di propria scelta, nonché l'integrazione dell'autenticazione, della sincronizzazione offline e delle notifiche push. È possibile completare l'esercitazione più volte, una per ogni applicazione client.

Per altre informazioni su App per dispositivi mobili, vedere la [mappa di apprendimento].
Per altre informazioni sulla piattaforma del servizio app di Azure, vedere [Servizio app di Azure].

<!-- URLs. -->
[Migrate your mobile service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[esercitazione introduttiva]: app-service-mobile-ios-get-started.md
[Archivio tabelle di Azure]:../cosmos-db/table-storage-how-to-use-dotnet.md
[Azure Cosmos DB]: ../cosmos-db/sql-api-get-started.md
[funzionalità di autenticazione]: ./app-service-mobile-auth.md
[funzionalità dati]: ./app-service-mobile-offline-data-sync.md
[funzionalità di notifica push]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.iOS, Xamarin.Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.Forms]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[scalabilità automatica]: ../app-service/manage-scale-up.md
[ambienti di staging]: ../app-service/deploy-staging-slots.md
[connessioni ibride]: ../biztalk-services/integration-hybrid-connection-overview.md
[reti virtuali]: ../app-service/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service/environment/app-service-app-service-environment-network-configuration-expressroute.md
[ambienti del servizio app]: ../app-service/environment/intro.md
[mappa di apprendimento]: https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/
[Servizio app di Azure]: ../app-service/overview.md
