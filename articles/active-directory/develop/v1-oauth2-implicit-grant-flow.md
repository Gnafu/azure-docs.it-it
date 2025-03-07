---
title: Informazioni sul flusso di concessione implicita OAuth2 in Azure Active Directory | Documentazione Microsoft
description: Altre informazioni su come implementare in Azure Active Directory il flusso di concessione implicita OAuth2 e stabilire se è adatto all'applicazione.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 90e42ff9-43b0-4b4f-a222-51df847b2a8d
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb751d4cad036135865af9f97e159da104749388
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532401"
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Informazioni sul flusso di concessione implicita OAuth2 in Azure Active Directory (AD)

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

È noto che la concessione implicita OAuth2 è quella con il più lungo elenco di preoccupazioni relative alla sicurezza nella specifica OAuth2. Nonostante ciò, è l'approccio implementato da ADAL JS e quello consigliato da Microsoft quando si scrivono applicazioni a singola pagina. Quali sono i vantaggi? È una questione di compromessi: infatti risulta evidente che la concessione implicita è il miglior approccio che si possa adottare per le applicazioni che utilizzano un'API Web tramite JavaScript da un browser.

## <a name="what-is-the-oauth2-implicit-grant"></a>Che cos'è la concessione implicita OAuth2?

La tipica [concessione del codice di autorizzazione OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.1) è la concessione di autorizzazione che usa due endpoint separati. L'endpoint di autorizzazione viene usato per la fase di interazione dell'utente, che restituisce un codice di autorizzazione. L'endpoint di token viene quindi usato dal client per cambiare il codice in un token di accesso e spesso anche in un token di aggiornamento. Le applicazioni Web devono presentare le proprie credenziali all'endpoint di token, in modo che il server di autorizzazione possa autenticare il client.

La [concessione implicita OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.2) è una variante di altre concessioni di autorizzazione e consente a un client di ottenere un token di accesso (e un elemento id_token, quando si usa [OpenId Connect](https://openid.net/specs/openid-connect-core-1_0.html)) direttamente dall'endpoint di autorizzazione, senza contattare l'endpoint di token né autenticare il client. Questa variante è stata progettata per le applicazioni basate su JavaScript in esecuzione in un Web browser: nella specifica OAuth2 originale i token vengono restituiti in un frammento di URI. In questo modo i bit dei token diventano disponibili per il codice JavaScript nel client, ma non verranno inclusi nei reindirizzamenti al server. Nella concessione implicita di OAuth2, l'endpoint di autorizzazione rilascia i token di accesso direttamente al client usando un URI di reindirizzamento precedentemente specificato. ha anche il vantaggio di eliminare eventuali requisiti per le chiamate tra origini, necessarie se l'applicazione JavaScript deve contattare l'endpoint di token.

Un caratteristica importante della concessione implicita OAuth2 è che tali flussi non restituiscono mai token di aggiornamento al client. La sezione successiva spiega che questo non è davvero necessario e costituirebbe anzi un problema di sicurezza.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Scenari adatti alla concessione implicita OAuth2

La specifica OAuth2 dichiara che la concessione implicita è stata definita per abilitare le applicazioni agente utente, ovvero le applicazioni JavaScript in esecuzione in un browser. La caratteristica distintiva di tali applicazioni è che il codice JavaScript viene usato per accedere alle risorse del server, in genere un'API Web, e per aggiornare di conseguenza l'esperienza utente delle applicazioni. Ad esempio in applicazioni come Gmail o Outlook Web Access, quando si seleziona un messaggio dalla Posta in arrivo, solo il pannello per la visualizzazione dei messaggi viene modificato per poter visualizzare la nuova selezione, mentre il resto della pagina rimane invariato. Avviene invece il contrario nelle app Web basate sul reindirizzamento tradizionale, in cui ogni interazione dell'utente restituisce un postback di pagina completo e un rendering di pagina completo della nuova risposta del server.

Le applicazioni che portano l'approccio basato su JavaScript all'estremo sono chiamate applicazioni a pagina singola o SPA. L'idea è che queste applicazioni gestiscano solo una pagina HTML iniziale e il codice JavaScript associato, mentre tutte le interazioni successive vengono gestite dalle chiamate all'API Web eseguite tramite JavaScript. Gli approcci ibridi, tuttavia, in cui l'applicazione è basata principalmente sul postback, ma esegue chiamate JS occasionali, non sono comuni e il discorso sull'utilizzo del flusso implicito riguarda anche tali approcci.

Le applicazioni basate sul reindirizzamento in genere proteggono le richieste tramite cookie, tuttavia questo approccio non funziona anche per le applicazioni JavaScript. I cookie funzionano solo nel dominio per cui sono stati generati, mentre le chiamate JavaScript potrebbero essere dirette ad altri domini. Di fatto ciò accade molto spesso. Si pensi, ad esempio, alle applicazioni che richiamano l'API Microsoft Graph, l'API di Office e l'API di Azure, che si trovano tutte all'esterno del dominio da cui è gestita l'applicazione. Una tendenza sempre più diffusa per le applicazioni JavaScript è quella di non avere back-end e di basarsi al 100% su API Web di terze parti per implementare la funzione aziendale.

Attualmente, il metodo preferito per proteggere le chiamate a un'API Web consiste nell'usare l'approccio basato sul token di connessione OAuth2, in cui ogni chiamata è accompagnata da un token di accesso OAuth2. L'API Web esamina il token di accesso in ingresso e, se vi trova gli ambiti necessari, concede l'accesso all'operazione richiesta. Il flusso implicito fornisce alle applicazioni JavaScript un pratico meccanismo per ottenere i token di accesso per un'API Web, con numerosi vantaggi rispetto ai cookie:

* I token possono essere ottenuti in modo affidabile senza bisogno di chiamate multiorigine. La registrazione obbligatoria dell'URI di reindirizzamento a cui i token vengono restituiti garantisce che non vengano spostati.
* Le applicazioni JavaScript possono ottenere tutti i token di accesso necessari per tutte le API Web di destinazione, senza alcuna restrizione sui domini.
* Le funzionalità HTML5, come l'archiviazione locale o di sessione, garantiscono il controllo completo sulla memorizzazione nella cache e sulla gestione del ciclo di vita dei token, mentre la gestione dei cookie è opaca per l'app.
* I token di accesso non sono soggetti agli attacchi di tipo richiesta intersito falsa.

Il flusso di concessione implicita non rilascia token di aggiornamento, soprattutto per motivi di sicurezza. Poiché l'ambito di un token di aggiornamento non è così ristretto come quello dei token di accesso, offre potenzialità decisamente maggiori, ma crea anche molti più danni se passa all'esterno. Nel flusso implicito i token vengono forniti nell'URL, quindi il rischio di intercettazione è maggiore che nella concessione di codice di autorizzazione.

Tuttavia, un'applicazione JavaScript ha a disposizione un altro meccanismo per rinnovare i token di accesso senza chiedere ripetutamente all'utente le credenziali. L'applicazione può usare un iframe nascosto per eseguire le nuove richieste di token nell'endpoint di autorizzazione di Azure AD: finché il browser ha una sessione attiva (ovvero, finché ha un cookie di sessione) nel dominio di Azure AD, la richiesta di autenticazione può essere eseguita correttamente senza alcuna interazione dell'utente.

Questo modello concede all'applicazione JavaScript la possibilità di rinnovare in modo indipendente i token di accesso e anche di acquisirne di nuovi per una nuova API, purché l'utente abbia già dato il consenso. In questo modo si evita di acquisire, gestire e proteggere un elemento di valore elevato, ad esempio un token di aggiornamento. L'elemento che rende possibile il rinnovo non interattivo, ovvero il cookie della sessione di Azure AD, viene gestito all'esterno dell'applicazione. Un altro vantaggio di questo approccio è che un utente può disconnettersi da Azure AD usando una delle applicazioni connesse ad Azure AD in esecuzione in una scheda del browser. Ciò comporta l'eliminazione del cookie della sessione di Azure AD e l'applicazione JavaScript perderà automaticamente la possibilità di rinnovare i token per l'utente disconnesso.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>La concessione implicita è adatta all'app?

La concessione implicita presenta maggiori rischi rispetto ad altre concessioni e le aree a cui è necessario prestare attenzione sono ben documentate (ad esempio, [uso improprio del token di accesso per rappresentare il proprietario delle risorse nel flusso implicito][OAuth2-Spec-Implicit-Misuse] e il [modello di minaccia OAuth 2,0 e la sicurezza Considerazioni][OAuth2-Threat-Model-And-Security-Implications]). Tuttavia il profilo di rischio maggiore è per lo più generato dal fatto che lo scopo è abilitare le applicazioni che eseguono codice attivo, fornito da una risorsa remota a un browser. Se si pianifica un'architettura di applicazione a singola pagina, non si hanno componenti back-end o si intende richiamare un'API Web tramite JavaScript, è consigliabile usare il flusso implicito per l'acquisizione dei token.

Se l'applicazione è un client nativo, il flusso implicito non è la soluzione ideale. L'assenza del cookie della sessione di Azure AD nel contesto di un client nativo non consente all'applicazione di mantenere una sessione di lunga durata. Di conseguenza, 'applicazione chiederà ripetutamente l'interazione dell'utente per ottenere i token di accesso per le nuove risorse.

Se si sta sviluppando un'applicazione Web che include un back-end e utilizza un'API dal codice back-end, nemmeno in questo caso il flusso implicito è la soluzione ideale. Altre concessioni offrono molte più possibilità. Ad esempio, la concessione delle credenziali client OAuth2 consente di ottenere token che rispecchiano le autorizzazioni assegnate all'applicazione stessa invece che alle deleghe utente. Ciò significa che il client è in grado di mantenere l'accesso a livello di codice alle risorse anche quando un utente non è attivamente impegnato in una sessione e così via. Tali concessioni offrono poi anche maggiori garanzie di sicurezza. Ad esempio, i token di accesso non passano mai dal browser dell'utente e non rischiano di essere salvati nella cronologia del browser e così via. L'applicazione client può anche eseguire l'autenticazione avanzata quando si richiede un token.

## <a name="next-steps"></a>Passaggi successivi

* Per un elenco completo delle risorse per gli sviluppatori, incluse le informazioni di riferimento per i protocolli e il supporto dei flussi di concessione di autorizzazione OAuth2 da Azure AD, vedere la guida per gli [sviluppatori di Azure ad][AAD-Developers-Guide]
* Per ulteriori informazioni dettagliate sul processo di integrazione dell'applicazione, vedere [come integrare un'applicazione con Azure ad][ACOM-How-To-Integrate] .

<!--Image references-->

<!--Reference style links in use-->
[AAD-Developers-Guide]:azure-ad-developers-guide.md
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: active-directory-how-to-integrate.md
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
