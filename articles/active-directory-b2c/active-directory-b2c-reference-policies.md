---
title: Criteri predefiniti in Azure Active Directory B2C | Microsoft Docs
description: Argomento che descrive il framework di criteri estendibile di Azure Active Directory B2C e che illustra come creare vari tipi di criteri.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5c89f39b2f94309ea3d99230f5265d834c7093d9
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38477450"
---
# <a name="azure-active-directory-b2c-built-in-policies"></a>Azure Active Directory B2C: criteri predefiniti


Il framework di criteri estendibile di Azure Active Directory (Azure AD) B2C è il punto di forza del servizio. I criteri descrivono le esperienze di identità, ad esempio iscrizione, accesso o modifica del profilo. Ad esempio, i criteri di iscrizione consentono di controllare i comportamenti configurando le impostazioni seguenti:

* Tipi di account (account di social network come Facebook o account locali come un indirizzo di posta elettronica) che gli utenti possono usare per effettuare l'iscrizione all'applicazione
* Attributi (ad esempio nome, codice postale, numero di scarpe) da raccogliere dall'utente durante la procedura di iscrizione
* Uso di Azure Multi-Factor Authentication
* Aspetto di tutte le pagine di iscrizione
* Informazioni (visualizzate come attestazioni in un token) che l'applicazione riceve al termine dell'esecuzione dei criteri

È possibile creare più criteri di tipi diversi nel proprio tenant e usarli nelle applicazioni in base alle esigenze. I criteri possono essere usati per più applicazioni. Gli sviluppatori possono in questo modo definire e modificare le esperienze delle identità degli utenti con modifiche minime al codice o anche senza alcuna modifica al codice.

Per usare i criteri, è disponibile una semplice interfaccia per sviluppatori. L'applicazione attiva i criteri tramite una richiesta di autenticazione HTTP standard (passando un parametro criteri nella richiesta) e riceve un token personalizzato come risposta. L'unica differenza tra le richieste che richiamano i criteri di iscrizione e le richieste che richiamano i criteri di accesso risiede nel nome del criterio usato nel parametro della stringa di query "p":

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

## <a name="create-a-sign-up-or-sign-in-policy"></a>Creare un criterio di iscrizione o accesso

Questo criterio consente di gestire le esperienze di iscrizione e accesso dei consumatori tramite una singola configurazione. I consumatori vengono indirizzati sul percorso corretto (iscrizione o accesso) a seconda del contesto. Vengono inoltre descritti i contenuti dei token che l'applicazione riceverà al completamento dell'iscrizione o dell'accesso.  Il codice di esempio per i criteri di **iscrizione o accesso** è [disponibile qui](active-directory-b2c-devquickstarts-web-dotnet-susi.md).  È consigliabile usare questi criteri invece dei criteri di **iscrizione** o di **accesso**.  

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

## <a name="create-a-sign-up-policy"></a>Creare un criterio di iscrizione

[!INCLUDE [active-directory-b2c-create-sign-up-policy](../../includes/active-directory-b2c-create-sign-up-policy.md)]

## <a name="create-a-sign-in-policy"></a>Creare un criterio di accesso

[!INCLUDE [active-directory-b2c-create-sign-in-policy](../../includes/active-directory-b2c-create-sign-in-policy.md)]

## <a name="create-a-profile-editing-policy"></a>Creare i criteri di modifica del profilo

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

## <a name="create-a-password-reset-policy"></a>Creare i criteri di reimpostazione delle password

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

## <a name="preview-policies"></a>Criteri di anteprima

Al momento del rilascio di nuove funzionalità, alcune di queste potrebbero non essere disponibili nei criteri esistenti.  Contiamo di sostituire le versioni precedenti con la versione più recente dello stesso tipo, dopo l'inserimento di tali criteri nel GA.  I criteri esistenti non cambieranno e per sfruttare queste nuove funzionalità sarà necessario creare nuovi criteri.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="how-do-i-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>Come si collegano i criteri di iscrizione o accesso ai criteri di reimpostazione delle password?
Quando si creano i criteri di **iscrizione o accesso** (con gli account locali), l'utente visualizza un collegamento **Password dimenticata?** nella prima pagina. Se si fa clic su questo collegamento non si attivano automaticamente i criteri di reimpostazione delle password. 

Viene invece restituito il codice di errore **`AADB2C90118`** all'app. L'applicazione deve gestire questo codice di errore e richiamare criteri di reimpostazione delle password specifici. Per altre informazioni, vedere un [esempio che illustra come collegare i criteri](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI).

### <a name="should-i-use-a-sign-up-or-sign-in-policy-or-a-sign-up-policy-and-a-sign-in-policy"></a>È consigliabile usare criteri di iscrizione o accesso o criteri di iscrizione e criteri di accesso?
È consigliabile usare i criteri di **iscrizione o accesso** invece dei criteri di **iscrizione** e dei criteri di **accesso**.  

I criteri di **iscrizione o accesso** offrono più funzionalità rispetto ai criteri di **accesso**. Consentono anche di usare la personalizzazione dell'interfaccia utente di pagina e sono meglio supportati nella localizzazione. 

I criteri di **accesso** sono consigliati se non è necessario localizzare i criteri, servono solo funzionalità di personalizzazione secondarie per il marchio e si vuole che la funzionalità di reimpostazione della password sia integrata.

## <a name="next-steps"></a>Passaggi successivi
* [Configurazione di token, sessione e accesso Single Sign-On](active-directory-b2c-token-session-sso.md)
* [Disabilitare la verifica della posta elettronica durante l'iscrizione dell'utente](active-directory-b2c-reference-disable-ev.md)

