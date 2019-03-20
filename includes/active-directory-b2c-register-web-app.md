---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: 4d5c8373f2a8f02ccfbe51cb970ab08744950e7e
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2018
ms.locfileid: "58114541"
---
[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Per registrare l'applicazione Web, usare le impostazioni specificate nella tabella.

![Impostazioni di registrazione di esempio per una nuova app Web](./media/active-directory-b2c-register-web-app/b2c-new-app-settings.png)

| Impostazione      | Valore di esempio  | DESCRIZIONE                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Nome** | App Contoso B2C | Immettere un **Nome** per l'applicazione che descriva l'applicazione agli utenti. | 
| **Includi app Web/API Web** | Sì | Selezionare **Sì** per un'applicazione Web. |
| **Consenti il flusso implicito** | Sì | Selezionare **Sì** se l'applicazione usa l'[accesso con OpenID Connect](../articles/active-directory-b2c/active-directory-b2c-reference-oidc.md). |
| **URL di risposta** | `https://localhost:44316` | Gli URL di risposta sono gli endpoint a cui Azure AD B2C restituisce eventuali token richiesti dall'applicazione. Immettere [un ](../articles/active-directory-b2c/active-directory-b2c-app-registration.md#choosing-a-web-app-or-api-reply-url) **URL di risposta** appropriato. In questo esempio l'app Web è locale ed è in ascolto sulla porta 44316. |

Fare clic su **Crea** per registrare l'applicazione.

L'applicazione appena registrata viene visualizzata nell'elenco di applicazioni per il tenant B2C. Selezionare l'app Web dall'elenco. Viene visualizzato il riquadro delle proprietà dell'applicazione Web.

![Proprietà dell'app Web](./media/active-directory-b2c-register-web-app/b2c-web-app-properties.png)

Annotare l'**ID client applicazione** univoco a livello globale. L'ID viene usato nel codice dell'applicazione.