---
title: "Esercitazione: Abilitare l'autenticazione in un'applicazione a pagina singola - Azure Active Directory B2C"
description: Informazioni su come usare Azure Active Directory B2C per consentire l'accesso degli utenti a un'applicazione a pagina singola (JavaScript).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc, seo-javascript-september2019
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 06ac81105ad8871c934715c18cd5f78fc3ea05f5
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858522"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c"></a>Esercitazione: Abilitare l'autenticazione in un'applicazione a pagina singola con Azure Active Directory B2C

Questa esercitazione illustra come usare Azure Active Directory (Azure AD) B2C per l'iscrizione e l'accesso degli utenti in un'applicazione a pagina singola. Azure AD B2C consente alle applicazioni di eseguire l'autenticazione ad account di social network, aziendali e di Azure Active Directory usando protocolli standard aperti.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiornare l'applicazione in Azure AD B2C
> * Configurare l'esempio per l'uso dell'applicazione
> * Iscriversi usando il flusso utente

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di continuare con i passaggi in questa esercitazione è necessario implementare le seguenti risorse di Azure AD B2C:

* [Tenant di Azure AD B2C](tutorial-create-tenant.md)
* [Applicazione registrata](tutorial-register-applications.md) nel tenant
* [Flussi utente creati](tutorial-create-user-flows.md) nel tenant

In più, nell'ambiente di sviluppo locale devono anche essere installati gli elementi seguenti:

* Editor di codice, ad esempio [Visual Studio Code](https://code.visualstudio.com/) o [Visual Studio 2019](https://www.visualstudio.com/downloads/)
* [.NET Core SDK 2.2](https://dotnet.microsoft.com/download) o versione successiva
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Aggiornare l'applicazione

Nella seconda esercitazione completata come parte dei prerequisiti è stata registrata un'applicazione Web in Azure AD B2C. Per consentire la comunicazione con l'esempio nell'esercitazione, è necessario aggiungere un URI di reindirizzamento all'applicazione in Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
1. Selezionare **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Applicazioni** e quindi l'applicazione *webapp1*.
1. In **URL di risposta** aggiungere `http://localhost:6420`.
1. Selezionare **Salva**.
1. Nella pagina delle proprietà, registrare l' **ID applicazione**. Si userà l'ID dell'app in un passaggio successivo quando si aggiorna il codice nell'applicazione Web a pagina singola.

## <a name="get-the-sample-code"></a>Scaricare il codice di esempio

In questa esercitazione si configura un codice di esempio che si scarica da GitHub. L'esempio dimostra come un'applicazione a pagina singola può usare Azure AD B2C per l'iscrizione e l'accesso degli utenti e per chiamare un'API Web protetta.

[Scaricare un file ZIP](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) o clonare l'esempio da GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Aggiornare l'esempio

Ora che è stato ottenuto l'esempio, aggiornare il codice con il nome del tenant di Azure AD B2C e l'ID dell'applicazione registrato in un passaggio precedente.

1. Aprire il file `index.html` nella radice della directory di esempio.
1. Nella definizione di `msalConfig`, modificare il valore **clientId** con l'ID applicazione registrato in un passaggio precedente. Quindi, aggiornare l'URI **authority** con il nome del tenant di Azure AD B2C. Aggiornare l'URI anche con il nome del flusso utente di accesso/iscrizione creato in uno dei prerequisiti (ad esempio, *B2C_1_signupsignin1*).

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "00000000-0000-0000-0000-000000000000", //This is your client ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Il nome del flusso utente usato in questa esercitazione è **B2C_1_signupsignin1**. Se si usa un nome di flusso utente diverso, specificarlo nel valore di `authority`.

## <a name="run-the-sample"></a>Eseguire l'esempio

1. Aprire una finestra della console e passare alla directory contenente il codice di esempio. Ad esempio:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Eseguire i comandi seguenti:

    ```
    npm install && npm update
    node server.js
    ```

    La finestra della console visualizza il numero di porta del server Node.js in esecuzione in locale:

    ```
    Listening on port 6420...
    ```

1. Passare a `http://localhost:6420` nel browser per visualizzare l'applicazione.

L'esempio supporta l'iscrizione, l'accesso, la modifica del profilo e la reimpostazione della password. Questa esercitazione illustra l'iscrizione di un utente con un indirizzo di posta elettronica.

### <a name="sign-up-using-an-email-address"></a>Iscriversi usando un indirizzo di posta elettronica

1. Selezionare **Accedi** per avviare il flusso utente *B2C_1_signupsignin1* specificato in un passaggio precedente.
1. Azure AD B2C presenta una pagina di accesso con un collegamento di iscrizione. Non avendo ancora un account, selezionare il collegamento **Iscriversi adesso**.
1. Il flusso di lavoro per l'iscrizione presenta una pagina per raccogliere e verificare l'identità dell'utente usando un indirizzo e-mail. Il flusso di lavoro per l'iscrizione raccoglie anche la password dell'utente e gli attributi richiesti definiti nel flusso utente.

    Usare un indirizzo e-mail valido ed eseguire la convalida usando un codice di verifica. Impostare una password. Immettere i valori per gli attributi richiesti.

    ![Pagina di iscrizione visualizzata dal flusso utente di accesso/iscrizione](./media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.PNG)

1. Selezionare **Crea** per creare un account locale nella directory di Azure AD B2C.

Quando si seleziona **Crea**, la pagina di iscrizione si chiude e viene nuovamente visualizzata la pagina di accesso.

È ora possibile usare l'indirizzo di posta elettronica e la password per accedere all'applicazione.

### <a name="error-insufficient-permissions"></a>Errore: autorizzazioni insufficienti

Dopo l'accesso, l'app visualizza l'errore "Autorizzazioni insufficienti": si tratta di un **comportamento previsto**:

```Output
ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.
Correlation ID: ce15bbcc-0000-0000-0000-494a52e95cd7
Timestamp: 2019-07-20 22:17:27Z
```

Questo errore viene visualizzato perché l'applicazione Web sta provando ad accedere a un'API Web protetta dalla directory demo, *fabrikamb2c*. Poiché il token di accesso è valido solo per la directory di Azure AD, la chiamata API non è autorizzata.

Per correggere l'errore, continuare con l'esercitazione successiva della serie (vedere [Passaggi successivi](#next-steps)) per la creazione di un'API Web protetta per la directory.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Aggiornare l'applicazione in Azure AD B2C
> * Configurare l'esempio per l'uso dell'applicazione
> * Iscriversi usando il flusso utente

Passare ora all'esercitazione successiva nella serie per concedere l'accesso a un'API Web protetta dall'applicazione a pagina singola:

> [!div class="nextstepaction"]
> [Esercitazione: Concedere l'accesso a un'API Web ASP.NET Core da un'applicazione a pagina singola usando Azure AD B2C >](active-directory-b2c-tutorials-spa-webapi.md)
