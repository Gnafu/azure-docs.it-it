---
title: Credenziali del certificato in Azure AD | Microsoft Docs
description: Questo articolo illustra la registrazione e l'uso delle credenziali del certificato per l'autenticazione dell'applicazione
services: active-directory
documentationcenter: .net
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f7c58b4ebd840aca555b52a03cf44ace311b64e3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="certificate-credentials-for-application-authentication"></a>Credenziali del certificato per l'autenticazione dell'applicazione

Azure Active Directory consente a un'applicazione di usare le proprie credenziali per l'autenticazione, ad esempio nel flusso di concessione di credenziali client OAuth 2.0 ([v1](active-directory-protocols-oauth-service-to-service.md), [v2](active-directory-v2-protocols-oauth-client-creds.md)) e nel flusso on-behalf-of ([v1](active-directory-protocols-oauth-on-behalf-of.md), [v2](active-directory-v2-protocols-oauth-on-behalf-of.md)).
Un tipo di credenziale che può essere usato è un'asserzione di token JSON Web (JWT) firmata con un certificato di proprietà dell'applicazione.

## <a name="format-of-the-assertion"></a>Formato dell'asserzione
Per calcolare l'asserzione, è preferibile usare una delle numerose librerie di [token JSON Web](https://jwt.ms/) nel linguaggio scelto. Le informazioni incluse nel token sono:

#### <a name="header"></a>Intestazione

| Parametro |  Commento |
| --- | --- |
| `alg` | Deve essere **RS256** |
| `typ` | Deve essere **JWT** |
| `x5t` | Deve essere l'identificazione personale SHA-1 del certificato X.509 |

#### <a name="claims-payload"></a>Attestazioni (payload)

| Parametro |  Commento |
| --- | --- |
| `aud` | Destinatari: deve essere **https://login.microsoftonline.com/*id_tenant*/oauth2/token** |
| `exp` | Data di scadenza: la data di scadenza del token. L'ora è rappresentata come numero di secondi dal 1° gennaio 1970 (1970-01-01T0:0:0Z) UTC fino all'ora in cui scade la validità del token.|
| `iss` | Autorità di certificazione: deve essere il parametro client_id (ID applicazione del servizio client) |
| `jti` | GUID: l'ID token JWT |
| `nbf` | Non prima: la data prima della quale il token non può essere usato. La data e l'ora sono rappresentate come numero di secondi dal 1 gennaio 1970 (1970-01-01T0:0:0Z) UTC fino alla data e all'ora in cui il token è stato rilasciato. |
| `sub` | Oggetto: per quanto riguarda `iss`, deve essere il parametro client_id (ID applicazione del servizio client) |

#### <a name="signature"></a>Firma

La firma viene calcolata applicando il certificato come descritto nella [specifica RFC7519 sul token JSON Web](https://tools.ietf.org/html/rfc7519)

### <a name="example-of-a-decoded-jwt-assertion"></a>Esempio di asserzione del token JWT decodificata

```
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,  
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"

```

### <a name="example-of-an-encoded-jwt-assertion"></a>Esempio di asserzione del token JWT codificata

La stringa seguente è un esempio di asserzione codificata. Se si osserva con attenzione, è possibile notare tre sezioni separate da punti (.).
La prima sezione codifica l'intestazione, la seconda il payload e l'ultima è la firma calcolata con i certificati a partire dal contenuto delle prime due sezioni.
```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

### <a name="register-your-certificate-with-azure-ad"></a>Registrare il certificato con Azure AD

È possibile associare la credenziale del certificato all'applicazione client in Azure AD tramite il portale di Azure usando uno dei metodi seguenti:

**Caricamento del file del certificato**

Nella registrazione dell'app di Azure per l'applicazione client, fare clic su **Impostazioni**, fare clic su **Chiavi** e quindi fare clic su **Carica la chiave pubblica**. Selezionare il file del certificato che si vuole caricare e fare clic su **Salva**. Dopo il salvataggio, il certificato viene caricato e vengono visualizzati i valori di identificazione personale, data di inizio e scadenza. 

**Aggiornamento del manifesto dell'applicazione**

Con un certificato disponibile, è necessario calcolare:

- `$base64Thumbprint`, che è la codifica Base 64 dell'hash del certificato
- `$base64Value`, che è la codifica Base 64 dei dati non elaborati dell'hash del certificato

È anche necessario specificare un GUID per identificare la chiave nel manifesto dell'applicazione (`$keyId`).

Nella registrazione dell'app di Azure per l'applicazione client aprire il manifesto dell'applicazione e sostituire la proprietà *keyCredentials* con le informazioni del nuovo certificato usando lo schema seguente:

```
"keyCredentials": [
    {
        "customKeyIdentifier": "$base64Thumbprint",
        "keyId": "$keyid",
        "type": "AsymmetricX509Cert",
        "usage": "Verify",
        "value":  "$base64Value"
    }
]
```

Salvare le modifiche apportate al manifesto dell'applicazione e caricare in Azure AD. La proprietà keyCredentials è multivalore, quindi è possibile caricare più certificati per una gestione delle chiavi più completa.
