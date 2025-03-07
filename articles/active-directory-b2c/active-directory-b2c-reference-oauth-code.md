---
title: Flusso del codice di autorizzazione - Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come compilare app Web tramite Azure AD B2C e il protocollo di autenticazione OpenID Connect.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 50d697f6ddff42690d4d674bd43a01d8a32c313c
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69874081"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>Flusso del codice di autorizzazione di OAuth 2.0 in Azure Active Directory B2C

È possibile usare la concessione del codice di autorizzazione OAuth 2.0 nelle app che vengono installate su un dispositivo per ottenere l'accesso a risorse protette, ad esempio le API Web. Con l'implementazione di Azure Active Directory B2C (Azure AD B2C) di OAuth 2.0 è possibile aggiungere attività di gestione dell'iscrizione, dell'accesso e altre attività di gestione delle identità alle app per desktop e per dispositivi mobili. Questo articolo è indipendente dal linguaggio. Descrive come inviare e ricevere messaggi HTTP senza usare alcuna libreria open source.

Il flusso del codice di autorizzazione di OAuth 2.0 è descritto nella [sezione 4.1 della specifica di OAuth 2.0](https://tools.ietf.org/html/rfc6749). È possibile usarlo per eseguire l'autenticazione e l'autorizzazione nella maggior parte dei [tipi di applicazioni](active-directory-b2c-apps.md), tra cui le applicazioni Web e le applicazioni installate in modo nativo. È possibile usare il flusso del codice di autorizzazione di OAuth 2.0 per acquisire in modo sicuro token di accesso e di aggiornamento per le applicazioni, i quali possono essere usati per accedere a risorse protette da un [server di autorizzazione](active-directory-b2c-reference-protocols.md).  Il token di aggiornamento consente al client di acquisire nuovi token di accesso (e aggiornamento) quando scade il token di accesso, in genere dopo un'ora.

Questo articolo illustra il flusso del codice di autorizzazione di OAuth 2.0 dei **client pubblici**. Un client pubblico è qualsiasi applicazione client che non può essere considerata attendibile in modo sicuro per mantenere l'integrità di una password segreta. Questo include app per dispositivi mobili, applicazioni desktop e pressoché qualsiasi applicazione che viene eseguita su un dispositivo e deve ottenere token di accesso.

> [!NOTE]
> Per aggiungere la gestione delle identità a un'App Web usando Azure AD B2C, usare [OpenID Connect](active-directory-b2c-reference-oidc.md) al posto di OAuth 2.0.

Azure AD B2C estende i flussi standard OAuth 2.0 per non limitarsi esclusivamente a semplici operazioni di autorizzazione e autenticazione. Introduce il [flusso utente](active-directory-b2c-reference-policies.md). Con i flussi utente è possibile usare OAuth 2.0 per aggiungere esperienze utente all'applicazione, ad esempio la gestione dell'iscrizione, dell'accesso e del profilo. I provider di identità che usano il protocollo OAuth 2.0 includono [Amazon](active-directory-b2c-setup-amzn-app.md), [Azure Active Directory](active-directory-b2c-setup-oidc-azure-active-directory.md), [Facebook](active-directory-b2c-setup-fb-app.md), [GitHub](active-directory-b2c-setup-github-app.md), [ Google](active-directory-b2c-setup-goog-app.md) e [LinkedIn](active-directory-b2c-setup-li-app.md).

Per provare le richieste HTTP in questo articolo:

1. Sostituire `{tenant}` con il nome del tenant di Azure AD B2C.
1. Sostituire `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` con l'ID app di un'applicazione registrata in precedenza nel tenant del Azure ad B2C.
1. Sostituire `{policy}` con il nome di un criterio creato nel tenant, ad esempio `b2c_1_sign_in`.

## <a name="1-get-an-authorization-code"></a>1. Ottenere un codice di autorizzazione
Il flusso del codice di autorizzazione ha inizio con il client che indirizza l'utente all'endpoint `/authorize` . Questa è la parte interattiva del flusso, dove l'utente esegue operazioni. In questa richiesta il client indica nel parametro `scope` le autorizzazioni che deve acquisire dall'utente. Di seguito sono riportati tre esempi (con interruzioni di riga per migliorare la leggibilità), ognuno dei quali usa un flusso utente diverso.


```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
```


| Parametro | Obbligatorio? | Descrizione |
| --- | --- | --- |
|inquilino| Obbligatoria | Nome del tenant di Azure AD B2C|
| politica | Obbligatoria | Flusso utente da eseguire. Specificare il nome di un flusso utente creato nel tenant del Azure AD B2C. Ad esempio: `b2c_1_sign_in`, `b2c_1_sign_up`o `b2c_1_edit_profile`. |
| client_id |Obbligatoria |ID applicazione assegnato all'app nel [portale di Azure](https://portal.azure.com). |
| response_type |Obbligatoria |Tipo di risposta, che deve includere `code` per il flusso del codice di autorizzazione. |
| redirect_uri |Obbligatoria |URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| scope |Obbligatoria |Elenco di ambiti separati da spazi. Un singolo valore di ambito indica ad Azure Active Directory (Azure AD) entrambe le autorizzazioni richieste. L'uso dell'ID client come ambito indica che l'app necessita di un token di accesso, che può essere usato per il servizio o l'API Web, rappresentato dallo stesso ID client.  L'ambito `offline_access` indica che l'app necessita di un token di aggiornamento per avere un accesso di lunga durata alle risorse. È anche possibile usare l'ambito `openid` per richiedere un token ID ad Azure Active Directory B2C. |
| response_mode |Consigliato |Metodo da usare per inviare all'app il codice di autorizzazione risultante. Può essere `query`, `form_post` o `fragment`. |
| stato |Consigliato |Valore incluso nella richiesta che può essere una stringa di qualsiasi contenuto che si intende usare. Per evitare attacchi di richiesta intersito falsa, viene in genere usato un valore univoco generato casualmente. Anche lo stato viene usato per codificare le informazioni sullo stato dell'utente nell'app prima del verificarsi della richiesta di autenticazione, ad esempio la pagina in cui si trova l'utente o il flusso utente che era in esecuzione. |
| prompt |Facoltativo |Tipo di interazione utente obbligatoria. L'unico valore valido in questa fase è `login`, che impone all'utente di immettere le credenziali per la richiesta. L'accesso Single Sign-On non avrà effetto. |

Viene a questo punto richiesto all'utente di completare il flusso di lavoro del flusso utente. È possibile che venga richiesto all'utente di immettere nome utente e password, di accedere con un'identità di social networking, di iscriversi alla directory o di effettuare qualsiasi altro passaggio. Le azioni dell'utente dipendono dal modo in cui è definito il flusso utente.

Dopo che l'utente ha completato il flusso utente, Azure AD restituisce una risposta per l'app in corrispondenza del valore usato per `redirect_uri`. Viene usato il metodo specificato nel parametro `response_mode`. La risposta è esattamente la stessa per ogni scenario di azione dell'utente, indipendentemente dal flusso utente eseguito.

Una risposta con esito positivo che usa `response_mode=query` ha un aspetto simile al seguente:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parametro | Descrizione |
| --- | --- |
| code |Codice di autorizzazione richiesto dall'app. L'app può usare il codice di autorizzazione per richiedere un token di accesso per una risorsa di destinazione. I codici di autorizzazione hanno una durata molto breve. In genere scadono dopo circa 10 minuti. |
| stato |Vedere la descrizione completa nella tabella della sezione precedente. Se un parametro `state` è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'app deve verificare che i valori `state` nella richiesta e nella risposta siano identici. |

Anche le risposte di errore possono essere inviate all'URI di reindirizzamento in modo che l'app possa gestirle adeguatamente:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametro | DESCRIZIONE |
| --- | --- |
| error |Stringa di codice di errore che è possibile usare per classificare i tipi di errori che si verificano. È possibile usare la stringa anche per rispondere agli errori. |
| error_description |Messaggio di errore specifico che consente di identificare la causa principale di un errore di autenticazione. |
| stato |Vedere la descrizione completa nella tabella precedente. Se un parametro `state` è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'app deve verificare che i valori `state` nella richiesta e nella risposta siano identici. |

## <a name="2-get-a-token"></a>2. Acquisizione di un token
Ora che è stato acquisito il codice di autorizzazione, è possibile riscattare `code` per un token per la risorsa desiderata inviando una richiesta POST all'endpoint `/token`. In Azure AD B2C, è possibile [richiedere i token di accesso per altre API](active-directory-b2c-access-tokens.md#request-a-token) come di consueto specificando gli ambiti nella richiesta.

È anche possibile richiedere un token di accesso per l'API Web back-end dell'app per convenzione di usare l'ID client dell'app come ambito richiesto (che comporterà un token di accesso con tale ID client come "audience"):

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parametro | Obbligatorio? | Descrizione |
| --- | --- | --- |
|inquilino| Obbligatoria | Nome del tenant di Azure AD B2C|
|politica| Obbligatoria| Flusso utente usato per acquisire il codice di autorizzazione. Non è possibile usare un flusso utente diverso in questa richiesta. |
| client_id |Obbligatoria |ID applicazione assegnato all'app nel [portale di Azure](https://portal.azure.com).|
| grant_type |Obbligatoria |Tipo di concessione. Per il flusso del codice di autorizzazione il tipo di concessione deve essere `authorization_code`. |
| scope |Consigliato |Elenco di ambiti separati da spazi. Un valore per l'ambito indica ad Azure AD entrambe le autorizzazioni richieste. L'uso dell'ID client come ambito indica che l'app necessita di un token di accesso, che può essere usato per il servizio o l'API Web, rappresentato dallo stesso ID client.  L'ambito `offline_access` indica che l'app necessita di un token di aggiornamento per avere un accesso di lunga durata alle risorse.  È anche possibile usare l'ambito `openid` per richiedere un token ID ad Azure Active Directory B2C. |
| code |Obbligatoria |Codice di autorizzazione acquisito nella prima sezione del flusso. |
| redirect_uri |Obbligatoria |L'URI di reindirizzamento dell'applicazione dove è stato ricevuto il codice di autorizzazione. |

Una risposta di token con esito positivo ha un aspetto simile al seguente:

```JSON
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parametro | Descrizione |
| --- | --- |
| not_before |Il momento in cui il token viene considerato valido, nel periodo. |
| token_type |Valore del tipo di token. L'unico tipo supportato da Azure AD è Bearer. |
| access_token |Token JSON Web (JWT) firmato richiesto. |
| scope |Ambiti per i quali il token è valido. È possibile usare gli ambiti anche per memorizzare i token nella cache per un uso successivo. |
| expires_in |Periodo di validità del token (in secondi). |
| refresh_token |Token di aggiornamento di OAuth 2.0. L'app può usare questo token per acquisire token aggiuntivi dopo la scadenza del token corrente. I token di aggiornamento sono di lunga durata. È possibile usarli per mantenere l'accesso alle risorse per periodi prolungati di tempo. Per altre informazioni, vedere le [informazioni di riferimento sul token di Azure AD B2C](active-directory-b2c-reference-tokens.md). |

Le risposte di errore si presentano nel modo seguente:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametro | DESCRIZIONE |
| --- | --- |
| error |Stringa di codice di errore che è possibile usare per classificare i tipi di errori che si verificano. È possibile usare la stringa anche per rispondere agli errori. |
| error_description |Messaggio di errore specifico che consente di identificare la causa principale di un errore di autenticazione. |

## <a name="3-use-the-token"></a>3. Uso del token
Dopo avere acquisito un token di accesso, è possibile usarlo nelle richieste alle API Web back-end includendolo nell'intestazione `Authorization`:

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Aggiornamento del token
I token di accesso e i token ID hanno breve durata. È necessario aggiornarli dopo la scadenza per continuare ad accedere alle risorse. Inviare a tale scopo un'altra richiesta POST per l'endpoint `/token`. questa volta specificando `refresh_token` invece di `code`:

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&client_secret=JqQX2PNo9bpM0uEihUPzyrh&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametro | Obbligatorio? | Descrizione |
| --- | --- | --- |
|inquilino| Obbligatoria | Nome del tenant di Azure AD B2C|
|politica |Obbligatoria |Flusso utente usato per acquisire il token di aggiornamento originale. Non è possibile usare un flusso utente diverso in questa richiesta. |
| client_id |Obbligatoria |ID applicazione assegnato all'app nel [portale di Azure](https://portal.azure.com). |
| client_secret |Obbligatoria |Valore di client_secret associato al valore di client_id nel [portale di Azure](https://portal.azure.com). |
| grant_type |Obbligatoria |Tipo di concessione. Per questa parte del flusso del codice di autorizzazione il tipo di concessione deve essere `refresh_token`. |
| scope |Consigliato |Elenco di ambiti separati da spazi. Un valore per l'ambito indica ad Azure AD entrambe le autorizzazioni richieste. L'uso dell'ID client come ambito indica che l'app necessita di un token di accesso, che può essere usato per il servizio o l'API Web, rappresentato dallo stesso ID client.  L'ambito `offline_access` indica che l'app necessita di un token di aggiornamento per un accesso di lunga durata alle risorse.  È anche possibile usare l'ambito `openid` per richiedere un token ID ad Azure Active Directory B2C. |
| redirect_uri |Facoltativo |L'URI di reindirizzamento dell'applicazione dove è stato ricevuto il codice di autorizzazione. |
| refresh_token |Obbligatoria |Token di aggiornamento originale acquisito nella seconda sezione del flusso. |

Una risposta di token con esito positivo ha un aspetto simile al seguente:

```JSON
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parametro | Descrizione |
| --- | --- |
| not_before |Il momento in cui il token viene considerato valido, nel periodo. |
| token_type |Valore del tipo di token. L'unico tipo supportato da Azure AD è Bearer. |
| access_token |Token JWT firmato richiesto. |
| scope |Ambiti per i quali il token è valido. È possibile usare gli ambiti anche per memorizzare i token nella cache per un uso successivo. |
| expires_in |Periodo di validità del token (in secondi). |
| refresh_token |Token di aggiornamento di OAuth 2.0. L'app può usare questo token per acquisire token aggiuntivi dopo la scadenza del token corrente. I token di aggiornamento hanno durata elevata e possono essere usati per mantenere l'accesso alle risorse per lunghi periodi di tempo. Per altre informazioni, vedere le [informazioni di riferimento sul token di Azure AD B2C](active-directory-b2c-reference-tokens.md). |

Le risposte di errore si presentano nel modo seguente:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametro | Descrizione |
| --- | --- |
| error |Stringa di codice di errore che è possibile usare per classificare i tipi di errori che si verificano. È possibile usare la stringa anche per rispondere agli errori. |
| error_description |Messaggio di errore specifico che consente di identificare la causa principale di un errore di autenticazione. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Usare la directory di Azure AD B2C
Per provare queste richieste, completare i passaggi seguenti. Sostituire i valori dell'esempio usato in questo articolo con valori personalizzati.

1. [Creare una directory Azure AD B2C](active-directory-b2c-get-started.md). Usare il nome della directory nelle richieste.
2. [Creare un'applicazione](active-directory-b2c-app-registration.md) per ottenere un ID applicazione e un URI di reindirizzamento. Includere un client nativo nell'app.
3. [Creare i flussi utente](active-directory-b2c-reference-policies.md) per ottenere i propri nomi di flusso utente.