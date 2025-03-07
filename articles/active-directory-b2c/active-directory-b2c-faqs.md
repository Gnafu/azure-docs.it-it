---
title: Domande frequenti (FAQ) per Azure Active Directory B2C
description: Risposte alle domande più frequenti su Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d852b786c1cc1c1eb9d39b931f9b8a142f969815
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065864"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: Domande frequenti

In questa pagina vengono fornite le risposte alle domande frequenti sulla Azure Active Directory B2C (Azure AD B2C). Controllarla costantemente per eventuali aggiornamenti.

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>Perché non riesco ad accedere all'estensione Azure AD B2C nel portale di Azure?

Esistono due motivi comuni per cui l'estensione di Azure AD non funziona per l'utente. Azure AD B2C richiede che il ruolo utente nella directory sia quello di amministratore globale. Se si ritiene di dover disporre dell'accesso, contattare l'amministratore. Se si dispone di privilegi di amministratore globale, assicurarsi di trovarsi in una directory di Azure AD B2C e non in una directory di Azure Active Directory. È possibile visualizzare le istruzioni per la [creazione di un tenant di Azure AD B2C](tutorial-create-tenant.md).

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>È possibile usare le funzionalità di Azure AD B2C nel tenant di Azure AD esistente per dipendenti aziendali?

Azure AD e Azure AD B2C vengono offerti come prodotti separati e non possono coesistere nello stesso tenant. Un tenant di Azure AD rappresenta un'organizzazione. Un tenant di Azure AD B2C rappresenta una raccolta di identità da usare con le applicazioni relying party. Con i criteri personalizzati (in anteprima pubblica), Azure AD B2C può eseguire la federazione di Azure AD consentendo l'autenticazione dei dipendenti in un'organizzazione.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>È possibile usare Azure AD B2C per fornire l'accesso a piattaforme di social networking (Facebook e Google+) in Office 365?

Azure AD B2C non può essere usato per autenticare gli utenti per Microsoft Office 365. Azure AD è la soluzione Microsoft per la gestione dell'accesso dei dipendenti alle app SaaS e include funzionalità progettate per questo scopo, ad esempio le licenze e l'accesso condizionale. Azure AD B2C offre una piattaforma di gestione di identità e accessi per la compilazione di applicazioni web e per dispositivi mobili. Quando Azure AD B2C è configurato per eseguire la federazione di un tenant di Azure AD, il tenant di Azure AD gestisce l'accesso dei dipendenti alle applicazioni che si basano su Azure AD B2C.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Che cosa sono gli account locali in Azure AD B2C? In che cosa differiscono dagli account aziendali o dell'istituto di istruzione in Azure AD?

In un tenant di Azure AD, gli utenti che vi appartengono effettuano l'accesso con un indirizzo di posta elettronica del modulo `<xyz>@<tenant domain>`. `<tenant domain>` è uno dei domini verificati nel tenant o nel dominio `<...>.onmicrosoft.com` iniziale. Questo tipo di account è un account aziendale o dell'istituto di istruzione.

In un tenant di Azure AD B2C, la maggior parte delle app richiede l'accesso dell'utente con un indirizzo di posta elettronica arbitrario, ad esempio joe@comcast.net, bob@gmail.com, sarah@contoso.com o jim@live.com. Questo tipo di account è un account locale. Sono supportati anche nomi utente arbitrari come account locali (ad esempio, joe, bob, sarah o jim). È possibile scegliere uno di questi due tipi di account locale durante la configurazione dei provider di identità per Azure AD B2C nel portale di Azure. Nel tenant di Azure AD B2C selezionare **provider di identità**, selezionare **account locale**e quindi fare clic su **nome utente**.

Gli account utente per le applicazioni devono essere sempre creati tramite un flusso di iscrizione utente, un flusso di iscrizione o di accesso utente oppure tramite l'API Graph di Azure AD. Gli account utente creati nel portale di Azure vengono usati solo per l'amministrazione del tenant.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Quali provider di identità di social networking sono attualmente supportati? Per quali provider è previsto il supporto in futuro?

Attualmente sono supportati diversi provider di identità basati su social network, tra cui Amazon, Facebook, GitHub (Preview), Google, LinkedIn, account Microsoft (MSA), QQ (anteprima), Twitter, WeChat (anteprima) e Weibo (anteprima). Viene valutato l'aggiunta del supporto per altri provider di identità di social networking comuni in base alla domanda dei clienti.

Azure AD B2C supporta anche [criteri personalizzati](active-directory-b2c-overview-custom.md). I criteri personalizzati consentono di creare criteri personalizzati per qualsiasi provider di identità che supporta [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) o SAML. Per un'introduzione ai criteri personalizzati, vedere lo [starter pack sui criteri personalizzati](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack).

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>È possibile configurare ambiti per raccogliere altre informazioni sugli utenti da diversi provider di identità di social networking?

No. Gli ambiti predefiniti usati per il gruppo di provider di identità di social networking supportato sono:

* Facebook: email
* Google+: email
* Account Microsoft: profilo di posta elettronica openid
* Amazon: profile
* LinkedIn: r_emailaddress e r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Per essere compatibile con Azure AD B2C, un'applicazione deve essere eseguita in Azure?

No, l'applicazione può essere ospitata ovunque (nel cloud o in locale). Per interagire con Azure AD B2C deve avere la possibilità di inviare e ricevere richieste HTTP su endpoint accessibili pubblicamente.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Nel caso di più tenant Azure AD B2C, come è possibile gestirli nel portale di Azure?

Prima di aprire "Azure AD B2C" nel menu sul lato sinistro del portale di Azure, è necessario passare alla directory che si vuole gestire. Per cambiare directory, fare clic sulla propria identità in alto a destra nel portale di Azure e quindi scegliere una directory nell'elenco a discesa che viene visualizzato.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Come si personalizzano i messaggi di posta elettronica di verifica (il contenuto e il campo "Da:") inviati da Azure AD B2C?

È possibile usare la [funzionalità di personalizzazione della società](../active-directory/fundamentals/customize-branding.md) per personalizzare il contenuto dei messaggi di posta elettronica di verifica. In particolare, è possibile personalizzare i due elementi di posta elettronica seguenti:

* **Logo banner**: In basso a destra.
* **Colore di sfondo**: Visualizzato nella parte superiore.

    ![Screenshot di un messaggio di posta elettronica di verifica personalizzato](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

La firma della posta elettronica contiene il nome del tenant di Azure AD B2C fornito al momento della creazione del tenant di Azure AD B2C. È possibile modificare il nome seguendo queste istruzioni:

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale.
1. Aprire il pannello **Azure Active Directory**.
1. Fare clic sulla scheda **Proprietà**.
1. Modificare il valore del campo **Nome**.
1. Fare clic su **Salva** nella parte superiore della pagina.

Attualmente non è possibile modificare il campo "Da:" del messaggio di posta elettronica.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Come si esegue la migrazione di nomi utente, password e profili esistenti dal database personale in Azure AD B2C?

È possibile usare l'API Graph di Azure AD per creare lo strumento di migrazione. Vedere il [Guida alla migrazione degli utenti](active-directory-b2c-user-migration.md) per informazioni dettagliate.

### <a name="what-password-user-flow-is-used-for-local-accounts-in-azure-ad-b2c"></a>Qual è il flusso di password utente usato per gli account locali in Azure AD B2C?

Il flusso di password utente di AAD B2C per gli account locali si basa sui criteri di Azure AD. L'iscrizione ad Azure AD B2C, i flussi di iscrizione o di accesso e di reset delle password utenti usano un livello di complessità sicuro e le password non scadono. Per altre informazioni dettagliate, vedere [Criteri password in Azure AD](/previous-versions/azure/jj943764(v=azure.100)) . Per informazioni su blocchi automatici e password degli account, vedere [Gestire le minacce alle risorse e ai dati in Azure Active Directory B2C](active-directory-b2c-reference-threat-management.md).

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>È possibile usare Azure AD Connect per eseguire la migrazione in Azure AD B2C delle identità utente archiviate in Active Directory locale?

No, Azure AD Connect non è progettato per funzionare con Azure AD B2C. È consigliabile usare l'[API Graph di Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md) per la migrazione dell'utente. Vedere il [Guida alla migrazione degli utenti](active-directory-b2c-user-migration.md) per informazioni dettagliate.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>L'applicazione può aprire le pagine di Azure AD B2C all'interno di un iFrame?

No. Per motivi di sicurezza, le pagine di Azure AD B2C non possono essere aperte in un iFrame. Il servizio comunica con il browser per impedire iFrames. L'uso di iFrames per le esperienze di gestione delle identità è sconsigliato dalla community della sicurezza in generale e dalla specifica OAUTH2 a causa del rischio di clickjacking.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Azure AD B2C è compatibile con i sistemi CRM come Microsoft Dynamics?

È disponibile l'integrazione con il portale di Microsoft Dynamics 365. Vedere l'articolo relativo alla [configurazione del portale di Dynamics 365 per usare Azure AD B2C per l'autenticazione](https://docs.microsoft.com/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Azure AD B2C è compatibile con SharePoint 2016 locale o versione precedente?

Azure AD B2C non è concepito per lo scenario di condivisione esterna con i partner di SharePoint. A questo scopo, vedere invece il blog su [Azure AD B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/).

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>È necessario usare Azure AD B2C o B2B per gestire le identità esterne?

Per altre informazioni su come applicare le funzionalità appropriate agli scenari relativi alle identità esterne, vedere l'articolo sulle [identità esterne](../active-directory/active-directory-b2b-compare-external-identities.md) .

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Quali funzionalità di reporting e controllo offre Azure AD B2C? Offre le stesse funzionalità di Azure AD Premium?

No, Azure AD B2C non supporta lo stesso set di report di Azure AD Premium. Esistono tuttavia molte caratteristiche comuni:

* I **report degli accessi** contengono un record per ogni accesso con dettagli ridotti.
* I **report di controllo** includono sia le attività amministrative che quelle delle applicazioni.
* I **report sull'uso** includono il numero di utenti, il numero di accessi e il volume di autenticazioni a più fattori.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>È possibile localizzare l'interfaccia utente delle pagine servite da Azure AD B2C? Quali lingue sono supportate?

Sì.  Altre informazioni sulla [personalizzazione della lingua](active-directory-b2c-reference-language-customization.md) che è in anteprima pubblica. Offriamo traduzioni per 36 lingue ed è possibile eseguire l'override di qualsiasi stringa in base alle esigenze.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-contosob2clogincom-to-logincontosocom"></a>È possibile usare l'URL personale nelle pagine di iscrizione e accesso servite da Azure AD B2C? Ad esempio, è possibile modificare l'URL da contoso.b2clogin.com a login.contoso.com?

No, per il momento. Questa funzionalità verrà implementata in futuro. Questo non si ottiene verificando il dominio nella scheda **Domini** nel portale di Azure. Tuttavia, con b2clogin.com, viene offerto un [dominio di livello superiore neutro](b2clogin.md)e, di conseguenza, l'aspetto esterno può essere implementato senza il riferimento a Microsoft.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Come si elimina il tenant di Azure AD B2C?

Attenersi a questa procedura per eliminare il tenant di Azure AD B2C:

1. Eliminare tutti i **flussi utente (criteri)** nel tenant del Azure ad B2C.
1. Eliminare tutte le **applicazioni** registrate nel tenant del Azure ad B2C.
1. Accedere quindi al [portale di Azure](https://portal.azure.com/) come amministratore della sottoscrizione. Usare lo stesso account aziendale o dell'Istituto di istruzione o lo stesso account Microsoft usato per iscriversi ad Azure.
1. Passare al tenant di Azure AD B2C che si vuole eliminare.
1. Selezionare **Azure Active Directory** dal menu a sinistra.
1. In **Gestisci** selezionare **Utenti**.
1. Selezionare un utente alla volta, escludendo l'amministratore della sottoscrizione con il quale si è connessi attualmente. Selezionare **Elimina** nella parte inferiore della pagina e selezionare **Sì** quando richiesto.
1. In **Gestisci**selezionare **Registrazioni app** (o **registrazioni app (legacy)** ).
1. Selezionare **Visualizza tutte le applicazioni**
1. Selezionare l'applicazione denominata **B2C-Extensions-app**, selezionare **Elimina**e quindi fare clic su **Sì** quando richiesto.
1. In **Gestisci**selezionare **impostazioni utente**.
1. Se presente, in **connessioni account LinkedIn**selezionare **No**, quindi selezionare **Salva**.
1. In **Gestisci**selezionare **Proprietà**
1. In **gestione accessi per le risorse di Azure**selezionare **Sì**e quindi fare clic su **Salva**.
1. Disconnettersi dal portale di Azure e quindi accedere di nuovo per aggiornare l'accesso.
1. Selezionare **Azure Active Directory** dal menu a sinistra.
1. Nella pagina **Overview (panoramica** ) selezionare **Delete directory (Elimina directory**). Seguire le istruzioni visualizzate per completare il processo.

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>È possibile ottenere Azure AD B2C come parte di Enterprise Mobility Suite?

No, Azure AD B2C è un servizio di Azure con pagamento in base al consumo e non fa parte di Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Come è possibile segnalare problemi relativi ad Azure AD B2C?

Vedere [Azure Active Directory B2C: Inviare richieste di supporto](active-directory-b2c-support.md).
