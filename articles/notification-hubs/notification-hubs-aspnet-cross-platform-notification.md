---
title: Inviare notifiche multipiattaforma agli utenti con Hub di notifica di Azure (ASP.NET)
description: Informazioni su come usare i modelli di Hub di notifica per inviare, in un'unica richiesta, una notifica indipendente dalla piattaforma destinata a tutte le piattaforme.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
origin.date: 04/14/2018
ms.date: 02/25/2019
ms.author: v-biyu
ms.openlocfilehash: 0f92b49c9d77029a9624782b49eb23f7083c49aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60872256"
---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Uso di Hub di notifica per inviare notifiche agli utenti tra piattaforme diverse

In un'esercitazione precedente relativa all'[invio di notifiche agli utenti con Hub di notifica] si è appreso come eseguire il push di notifiche a tutti i dispositivi registrati per uno specifico utente autenticato. In tale esercitazione vengono utilizzate più richieste per inviare una notifica a ogni piattaforma client supportata. Hub di notifica di Azure supporta modelli che consentono di specificare il modo in cui un determinato dispositivo vuole ricevere notifiche. Questo metodo semplifica l'invio di notifiche multipiattaforma.

Questo articolo illustra come sfruttare i modelli per inviare, in un'unica richiesta, una notifica indipendente dalla piattaforma destinata a tutte le piattaforme. Per informazioni dettagliate sui modelli, vedere [Panoramica dell'Hub di notifica][Templates].

> [!IMPORTANT]
> I progetti Windows Phone 8.1 e versioni precedenti non sono supportati in Visual Studio 2017. Per altre informazioni, vedere [Selezione della piattaforma e compatibilità di Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

> [!NOTE]
> Con Hub di notifica, un dispositivo può registrare più modelli con lo stesso tag. In questo caso, un messaggio in arrivo destinato a tale tag determina il recapito al dispositivo di più notifiche, una per ogni modello. Questo processo consente di visualizzare lo stesso messaggio in più notifiche visive, ad esempio sia come notifica che come avviso popup in un'app di Windows Store.

## <a name="send-cross-platform-notifications-using-templates"></a>Inviare notifiche multipiattaforma usando i modelli

Per inviare notifiche multipiattaforma usando i modelli, seguire questa procedura:

1. In Esplora soluzioni in Visual Studio espandere la cartella **Controller** e quindi aprire il file RegisterController.cs.

2. Trovare il blocco di codice nel metodo `Put` che crea una nuova registrazione e quindi sostituire il contenuto di `switch` con il codice seguente:

    ```csharp
    switch (deviceUpdate.Platform)
    {
        case "mpns":
            var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>$(message)</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
            registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "wns":
            toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
            registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "apns":
            var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
            registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }
    ```

    In questo codice viene chiamato il metodo specifico della piattaforma per creare una registrazione modello anziché una registrazione nativa. Dato che le registrazioni dei modelli derivano da registrazioni native, non è necessario modificare le registrazioni esistenti.

3. Nel controller `Notifications` sostituire il metodo `sendNotification` con il codice seguente:

    ```csharp
    public async Task<HttpResponseMessage> Post()
    {
        var user = HttpContext.Current.User.Identity.Name;
        var userTag = "username:" + user;

        var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
        await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

    Questo codice invia una notifica a tutte le piattaforme contemporaneamente, senza che sia necessario specificare un payload nativo. Gli hub di notifica creano il payload corretto e lo distribuiscono a tutti i dispositivi con il valore *tag* specificato, come indicato nei modelli registrati.

4. Pubblicare di nuovo il progetto back-end WebApi.

5. Eseguire nuovamente l'app client e quindi verificare che la registrazione abbia avuto esito positivo.

6. (Facoltativo) Distribuire l'app client in un secondo dispositivo e quindi eseguirla.
    Verrà visualizzata una notifica su ogni dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Dopo avere completato questa esercitazione, è possibile reperire altre informazioni su Hub di notifica e sui modelli nei seguenti argomenti:

* [Use Notification Hubs to send breaking news]: Demonstrates another scenario for using templates.
* [Panoramica di Hub di notifica di Azure][Templates]: contiene informazioni più dettagliate sui modelli.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[invio di notifiche agli utenti con Hub di notifica]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx
