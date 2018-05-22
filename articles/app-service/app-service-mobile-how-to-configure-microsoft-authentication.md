---
title: Come configurare l'autenticazione dell'account Microsoft per un'applicazione dei servizi app
description: Informazioni su come configurare l'autenticazione dell'account Microsoft per un'applicazione dei servizi app.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.openlocfilehash: 4fb5bdf30502dbca3eba961165a1ab643427abd6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Come configurare l’applicazione del servizio app per usare l'account di accesso Microsoft
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo argomento descrive come configurare il servizio app di Azure per usare l'account Microsoft come provider di autenticazione. 

## <a name="register-microsoft-account"></a>Registrare l'app con l'account Microsoft
1. Accedere al [portale di Azure], e passare all'applicazione. Copiare l' **URL**, che verrà usato in un secondo momento per configurare l'app con Account Microsoft.
2. Passare alla pagina [My Applications] di Microsoft Account Developer Center e, se necessario, accedere con il proprio account Microsoft.
3. Fare clic su **Aggiungi un'app**, digitare il nome di un'applicazione e quindi fare clic su **Crea**.
4. Prendere nota dell' **ID applicazione**perché sarà necessario in un secondo momento. 
5. In "Piattaforme" fare clic su **Aggiungi piattaforma** e quindi selezionare "Web".
6. In "URI di reindirizzamento" specificare l'endpoint dell'applicazione e quindi fare clic su **Salva**. 
   
   > [!NOTE]
   > L'URI di reindirizzamento corrisponde all'URL dell'applicazione con l'aggiunta del percorso */.auth/login/microsoftaccount/callback*. Ad esempio: `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`.   
   > Assicurarsi che sia in uso lo schema HTTPS.
   
7. In "Segreti applicazione" fare clic su **Genera nuova password**. Prendere nota del valore visualizzato. Una volta chiusa la pagina, il valore non verrà più mostrato.

    > [!IMPORTANT]
    > La password è una credenziale di sicurezza importante. Non condividerla con altri e non distribuirla all'interno di un'applicazione client.

## <a name="secrets"></a>Aggiungere le informazioni dell'account Microsoft all'applicazione del servizio app
1. Nel [portale di Azure] passare all'applicazione e fare clic su **Impostazioni** > **Autenticazione/Autorizzazione**.
2. Se la funzionalità Autenticazione/Autorizzazione non è abilitata, impostare l'opzione su **Sì**.
3. Fare clic su **Account Microsoft**. Incollare i valori di ID applicazione e Password ottenuti in precedenza ed eventualmente abilitare gli ambiti richiesti dall'applicazione. Fare quindi clic su **OK**.
   
    ![][1]
   
    Per impostazione predefinita, il servizio app fornisce l'autenticazione ma non limita l'accesso alle API e al contenuto del sito solo agli utenti autorizzati. È necessario autorizzare gli utenti nel codice dell'app.
4. (Facoltativo) Per consentire l'accesso al sito solo agli utenti autenticati dall'account Microsoft, impostare il parametro **Azione da eseguire quando la richiesta non è autenticata** su **Account Microsoft**. Per poter utilizzare questa funzione, tuttavia, è necessario che tutte le richieste vengano autenticate e che le richieste non autenticate vengano reindirizzate all’account Microsoft per l'autenticazione.
5. Fare clic su **Save**.

È ora possibile usare l'account Microsoft per l'autenticazione nell'app.

## <a name="related-content"></a>Contenuti correlati
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[portale di Azure]: https://portal.azure.com/
