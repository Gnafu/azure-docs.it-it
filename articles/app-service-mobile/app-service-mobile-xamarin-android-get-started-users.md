---
title: Introduzione all'autenticazione per app per dispositivi mobili in Xamarin Android
description: Informazioni su come usare le app per dispositivi mobili per autenticare gli utenti dell'app per Xamarin Android tramite vari provider di identità, tra cui AAD, Google, Facebook, Twitter e Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: panarasi
editor: ''
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 7a0b2c54c2d2a9daba56ea1d05c18e72a2d7a7a0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447060"
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Aggiungere l'autenticazione all'app per Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center investe in nuovi e integrati servizi fondamentali per lo sviluppo di app per dispositivi mobili. Gli sviluppatori possono utilizzare **compilare**, **Test** e **Distribuisci** servizi per impostare le pipeline di integrazione continua e recapito. Dopo aver distribuito l'app, gli sviluppatori possono monitorare lo stato e sull'utilizzo di app using il **Analitica** e **diagnostica** servizi e Coinvolgi gli utenti utilizzando il **Push** servizio. Gli sviluppatori possono inoltre sfruttare **Auth** di autenticare gli utenti e **dati** service per rendere persistente e sincronizzare i dati dell'app nel cloud. Consulta [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-android-get-started-users) oggi stesso.
>

## <a name="overview"></a>Panoramica
Questo argomento descrive come autenticare gli utenti di un'app per dispositivi mobili dall'applicazione client. In questa esercitazione si aggiungerà l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato dal servizio App per dispositivi mobili di Azure. Una volta completate l'autenticazione e l'autorizzazione nell'app per dispositivi mobili, verrà visualizzato il valore dell'ID utente.

Questa esercitazione è basata sulla guida introduttiva dell'app mobile. È anche necessario completare prima l'esercitazione [Creare un'app per Xamarin.Android]. Se non si usa il progetto server di avvio rapido scaricato, è necessario aggiungere il pacchetto di estensione di autenticazione al progetto. Per altre informazioni sui pacchetti di estensione server, vedere l'articolo relativo all' [utilizzo dell'SDK del server back-end .NET per app per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register"></a>Registrare l'app per l'autenticazione e configurare i servizi app
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Aggiungere l'app agli URL di reindirizzamento esterni consentiti

L'autenticazione sicura richiede la definizione di un nuovo schema URL per l'app. In questo modo il sistema di autenticazione reindirizza all'app al termine del processo di autenticazione. In questa esercitazione si usa lo schema URL _appname_. È tuttavia possibile usare QUALSIASI schema URL. Lo schema deve essere univoco per l'applicazione per dispositivi mobili. Per abilitare il reindirizzamento sul lato server:

1. Nel [portale di Azure] selezionare il servizio app.

2. Fare clic sull'opzione di menu **Autenticazione/Autorizzazione**.

3. In **URL di reindirizzamento esterni consentiti** specificare `url_scheme_of_your_app://easyauth.callback`.  Il valore **url_scheme_of_your_app** in questa stringa è lo schema URL per l'applicazione per dispositivi mobili.  Deve seguire le normale specifica URL per un protocollo, ovvero usare solo lettere e numeri e iniziare con una lettera.  È opportuno prendere nota della stringa scelta perché sarà necessario modificare il codice dell'applicazione per dispositivi mobili con lo schema URL in diverse posizioni.

4. Fare clic su **OK**.

5. Fare clic su **Save**.

## <a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

In Visual Studio o Xamarin Studio, eseguire il progetto client su un dispositivo o un emulatore. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato). L'eccezione non gestita viene generata perché l'app prova ad accedere al back-end dell'app per dispositivi mobili come utente non autenticato. La tabella *TodoItem* richiede ora l'autenticazione.

Si aggiornerà quindi l'app client per richiedere le risorse dal back-end dell'app per dispositivi mobili con un utente autenticato.

## <a name="add-authentication"></a>Aggiungere l'autenticazione all'app
L'applicazione viene aggiornata per richiedere agli utenti di toccare il pulsante **Accedi** ed eseguire l'autenticazione prima della visualizzazione dei dati.

1. Aggiungere il codice seguente alla classe **TodoActivity** :
   
        // Define an authenticated user.
        private MobileServiceUser user;
        private async Task<bool> Authenticate()
        {
                var success = false;
                try
                {
                    // Sign in with Facebook login using a server-managed flow.
                    user = await client.LoginAsync(this,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    CreateAndShowDialog(string.Format("you are now logged in - {0}",
                        user.UserId), "Logged in!");
   
                    success = true;
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
                return success;
        }
   
        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds.
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;
   
                // Load the data.
                OnRefreshItemsSelected();
            }
        }
   
    Verrà creato un nuovo metodo per autenticare un utente e un gestore del metodo per un nuovo pulsante **Accedi** . Nell'esempio di codice precedente, l'utente viene autenticato tramite un account di accesso di Facebook. Viene usata una finestra di dialogo per visualizzare l'ID utente una volta completata l'autenticazione.
   
   > [!NOTE]
   > Se si usa un provider di identità diverso da Facebook, sostituire il valore passato a **LoginAsync** in precedenza a uno dei seguenti: *MicrosoftAccount*, *Twitter*, *Google* o *WindowsAzureActiveDirectory*.
   > 
   > 
2. Nel metodo **OnCreate** , eliminare o impostare come commento la riga di codice seguente:
   
        OnRefreshItemsSelected ();
3. Nel file Activity_To_Do.axml aggiungere la definizione del pulsante *LoginUser* prima del pulsante *AddItem* esistente:
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Aggiungere l'elemento seguente al file di risorse Strings.xml:
   
        <string name="login_button_text">Sign in</string>
5. Aprire il file AndroidManifest.xml, aggiungere il codice seguente all'interno dell'elemento XML `<application>`:

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. In Visual Studio o Xamarin Studio eseguire il progetto client in un dispositivo o un emulatore ed eseguire l'accesso tramite il provider di identità scelto. Dopo aver eseguito l'accesso, verranno visualizzati l'ID di accesso l'elenco di elementi ToDo e sarà possibile aggiornare i dati nell'app.

## <a name="troubleshooting"></a>risoluzione dei problemi

**Si è verificato l'arresto anomalo dell'applicazione `Java.Lang.NoSuchMethodError: No static method startActivity`**

In alcuni casi, i conflitti nei pacchetti di supporto vengono visualizzati semplicemente con un avviso in Visual Studio, ma si verifica un arresto anomalo dell'applicazione con questa eccezione in fase di esecuzione. In questo caso è necessario verificare che tutti i pacchetti di supporto referenziati nel progetto abbiano la stessa versione. Il [pacchetto NuGet di App per dispositivi mobili di Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) presenta la dipendenza `Xamarin.Android.Support.CustomTabs` per la piattaforma Android, perciò se il progetto usa pacchetti di supporto più recenti è necessario installare questo pacchetto direttamente con la versione richiesta per evitare conflitti.

<!-- URLs. -->
[Creare un'app per Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
