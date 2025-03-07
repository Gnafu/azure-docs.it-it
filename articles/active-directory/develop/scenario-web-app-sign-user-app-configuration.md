---
title: App Web che esegue l'accesso degli utenti (configurazione del codice)-piattaforma di identità Microsoft
description: Informazioni su come creare un'app Web per l'accesso degli utenti (configurazione del codice)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a746b0f6d85e3f012cdd2e78fff8cd10a586950
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086745"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>App Web per l'accesso degli utenti-configurazione del codice

Informazioni su come configurare il codice per l'app Web per l'accesso degli utenti.

## <a name="libraries-used-to-protect-web-apps"></a>Librerie usate per proteggere le app Web

<!-- This section can be in an include for Web App and Web APIs -->
Le librerie usate per proteggere un'app Web (e un'API Web) sono:

| Piattaforma | Libreria | DESCRIZIONE |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Estensioni del modello di identità per .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Usato direttamente da ASP.NET e ASP.NET Core, Microsoft Identity Extensions per .NET propone un set di dll in esecuzione sia su .NET Framework che su .NET Core. Da un'app Web ASP.NET/ASP.NET Core è possibile controllare la convalida dei token usando la classe **TokenValidationParameters** (in particolare in alcuni scenari ISV) |
| ![Java](media/sample-v2-code/logo_java.png) | [msal4j](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | MSAL per Java-attualmente disponibile in anteprima pubblica |
| ![Python](media/sample-v2-code/logo_python.png) | [Python MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | MSAL per Python-attualmente disponibile in anteprima pubblica |

I frammenti di codice in questo articolo e gli elementi seguenti vengono estratti da:

- l' [esercitazione incrementale ASP.NET Core app Web, capitolo 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).
- [esempio di app Web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)
- esempio di [applicazione Web Java che chiama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp) msal4j app Web
- l' [applicazione Web Python che chiama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) MSAL. Esempio di app Web Python

È possibile fare riferimento a queste esercitazioni ed esempi per i dettagli di implementazione completi.

## <a name="configuration-files"></a>File di configurazione

Le applicazioni Web che eseguono l'accesso agli utenti con la piattaforma di identità Microsoft vengono in genere configurate tramite i file di configurazione. Le impostazioni che è necessario compilare sono:

- il cloud `Instance` se si vuole eseguire l'app (ad esempio nei cloud nazionali)
- destinatari in`tenantId`
- oggetto `clientId` per l'applicazione, copiato dal portale di Azure.

In alcuni casi, le applicazioni possono essere `authority`con parametri da, che è la concatenazione `instance` di e`tenantId`

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET Core queste impostazioni si trovano nel `appsettings.json` file, nella sezione "AzureAD".

```Json
{
  "AzureAd": {
    // Azure Cloud instance among:
    // "https://login.microsoftonline.com/" for Azure Public cloud.
    // "https://login.microsoftonline.us/" for Azure US government.
    // "https://login.microsoftonline.de/" for Azure AD Germany
    // "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD Audience among:
    // - the tenant Id as a GUID obtained from the azure portal to sign-in users in your organization
    // - "organizations" to sign-in users in any work or school accounts
    // - "common" to sign-in users with any work and school account or Microsoft personal account
    // - "consumers" to sign-in users with Microsoft personal account only
    "TenantId": "[Enter the tenantId here]]",

    // Client Id (Application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

In ASP.NET Core, esiste un altro file (`properties\launchSettings.json`) che contiene l'URL (`applicationUrl`) e la porta SSL (`sslPort`) per l'applicazione e vari profili.

```Json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

Nel portale di Azure, gli URI di risposta necessari per la registrazione nella pagina di **autenticazione** per l'applicazione devono corrispondere a questi URL. ovvero, per i due file di configurazione `https://localhost:44321/signin-oidc` precedenti, il ApplicationUrl è `http://localhost:3110` , `sslPort` ma `CallbackPath` è specificato (44321) e è `/signin-oidc` come definito in `appsettings.json`.
  
Allo stesso modo, l'URI di disconnessione viene impostato su `https://localhost:44321/signout-callback-oidc`.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET, l'applicazione viene configurata `Web.Config` tramite il file

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, please visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

Nel portale di Azure, gli URI di risposta necessari per la registrazione nella pagina di **autenticazione** per l'applicazione devono corrispondere a questi URL. `https://localhost:44326/`ovvero.

# <a name="javatabjava"></a>[Java](#tab/java)

In Java la configurazione si trova nel `application.properties` file che si trova in`src/main/resources`

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraphUsers=http://localhost:8080/msal4jsample/graph/users
```

Nel portale di Azure, gli URI di risposta necessari per la registrazione nella pagina di **autenticazione** per l'applicazione devono corrispondere ai redirectUris definiti dall'applicazione, ovvero `http://localhost:8080/msal4jsample/secure/aad` e`http://localhost:8080/msal4jsample/graph/users`

# <a name="pythontabpython"></a>[Python](#tab/python)

Ecco il file di configurazione di Python in [app_config. py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/web_app_sample/app_config.py)

```Python
AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
CLIENT_ID = "Enter_the_Application_Id_here"
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
SCOPE = ["https://graph.microsoft.com/User.Read"]
REDIRECT_URI = "http://localhost:5000/getAToken"
```

---

## <a name="initialization-code"></a>Codice di inizializzazione

Il codice di inizializzazione è diverso a seconda della piattaforma. Per ASP.NET Core e ASP.NET, l'accesso degli utenti viene delegato al middleware OpenIDConnect. Attualmente il modello ASP.NET/ASP.NET Core genera applicazioni Web per l'endpoint Azure AD v 1.0. Pertanto, è necessario un po' di configurazione per adattarle all'endpoint della piattaforma Microsoft Identity (v 2.0). Nel caso di Java, viene gestito da Spring con la collaborazione dell'applicazione.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET Core app Web (e le API Web), l'applicazione è protetta perché è presente `[Authorize]` un attributo sui controller o sulle azioni del controller. Questo attributo verifica che l'utente sia autenticato. Il codice che esegue l'inizializzazione dell'applicazione si `Startup.cs` trova nel file e, per aggiungere l'autenticazione con la piattaforma di identità Microsoft (in precedenza Azure ad v 2.0), è necessario aggiungere il codice seguente. I commenti nel codice devono essere autoesplicativi.

  > [!NOTE]
  > Se si avvia il progetto con il progetto Web ASP.NET Core predefinito in Visual Studio o `dotnet new mvc` se si `AddAzureAD` usa il metodo è disponibile per impostazione predefinita perché i pacchetti correlati vengono caricati automaticamente.
  > Tuttavia, se si compila un progetto da zero e si tenta di usare il codice riportato di seguito, si consiglia di aggiungere il pacchetto NuGet **"Microsoft. AspNetCore. Authentication. AzureAD. UI"** al progetto per `AddAzureAD` rendere disponibile il metodo.
  
Il codice seguente è disponibile da [Startup. cs # L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34)

```CSharp
public class Startup
{
 ...

  // This method gets called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign-in users with the Microsoft identity platform
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration);
  
      services.AddMvc(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
            options.Filters.Add(new AuthorizeFilter(policy));
            })
        .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
    }
```

È un metodo di estensione definito in [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L23.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23) `AddMicrosoftIdentityPlatformAuthentication` È

- aggiunge il servizio di autenticazione
- configurare le opzioni per la lettura del file di configurazione
- Configura le opzioni di OpenID Connect in modo che l'autorità utilizzata sia l'endpoint della piattaforma di identità Microsoft (in precedenza Azure AD v 2.0)
- l'emittente del token viene convalidato
- le attestazioni corrispondenti al nome sono mappate dall'attestazione "preferred_username" nel token ID 

Oltre alla configurazione, è possibile specificare quando si chiama `AddMicrosoftIdentityPlatformAuthentication`:

- nome della sezione di configurazione (per impostazione predefinita AzureAD)
- per tracciare gli eventi del middleware OpenIdConnect, che consentono di risolvere i problemi dell'applicazione Web se l'autenticazione non funziona: l' `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` impostazione `true` di su indicherà come le informazioni vengono elaborate dal set di ASP.NET Core il middleware procede dalla risposta HTTP all'identità dell'utente in `HttpContext.User`.

```CSharp
/// <summary>
/// Add authentication with Microsoft identity platform.
/// This method expects the configuration file will have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenIdConnect events.
/// </param>
/// <returns></returns>
public static IServiceCollection AddMicrosoftIdentityPlatformAuthentication(
  this IServiceCollection services,
  IConfiguration configuration,
  string configSectionName = "AzureAd",
  bool subscribeToOpenIdConnectMiddlewareDiagnosticsEvents = false)
{
  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
      .AddAzureAD(options => configuration.Bind(configSectionName, options));
  services.Configure<AzureADOptions>(options => configuration.Bind(configSectionName, options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
      // Per the code below, this application signs in users in any Work and School
      // accounts and any Microsoft Personal Accounts.
      // If you want to direct Azure AD to restrict the users that can sign-in, change
      // the tenant value of the appsettings.json file in the following way:
      // - only Work and School accounts => 'organizations'
      // - only Microsoft Personal accounts => 'consumers'
      // - Work and School and Personal accounts => 'common'
      // If you want to restrict the users that can sign-in to only one tenant
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users that can sign-in to several organizations
      // Set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set the nameClaimType to be preferred_username.
      // This change is needed because certain token claims from Azure AD V1 endpoint
      // (on which the original .NET core template is based) are different than Microsoft identity platform endpoint.
      // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
      options.TokenValidationParameters.NameClaimType = "preferred_username";

      // ...

      if (subscribeToOpenIdConnectMiddlewareDiagnosticsEvents)
      {
          OpenIdConnectMiddlewareDiagnostics.Subscribe(options.Events);
      }
  });
  return services;
}
  ...
```

La `AadIssuerValidator` classe consente di convalidare l'autorità emittente del token in molti casi (applicazione di token v 1.0 o v 2.0, single-tenant o multi-tenant o applicazione che esegue l'accesso agli utenti con account Microsoft personali, nel cloud pubblico di Azure o in nazionale Cloud). È disponibile da [Microsoft. Identity. Web/Resource/AadIssuerValidator. cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Il codice correlato all'autenticazione in ASP.NET Web App/API Web si trova nel `App_Start/Startup.Auth.cs` file.

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0
     // The `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="javatabjava"></a>[Java](#tab/java)

L'esempio Java USA Spring Framework. L'applicazione è protetta perché si implementa un `Filter`oggetto, che ottiene ogni risposta http. Nella Guida introduttiva all'app Web Java questa `AuthFilter` operazione `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`si trova in. Il filtro elabora il flusso del codice di autorizzazione OAuth 2,0 e pertanto:

- Verifica se l'utente è autenticato (`isAuthenticated()` metodo)
- Se l'utente non è autenticato, calcola l'URL del Azure AD autorizzare gli endpoint e reindirizza il browser a questo URI
- Quando arriva la risposta, contenente il flusso del codice di autenticazione, msal4j acquisisce il token.
- Quando riceve infine il token dall'endpoint del token (sull'URI di reindirizzamento), l'utente ha eseguito l'accesso.

Per informazioni dettagliate, `doFilter()` vedere il metodo in [AuthFilter. Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java)

> [!NOTE]
> Il codice di `doFilter()` viene scritto in un ordine leggermente diverso, ma il flusso è quello descritto.

Per informazioni dettagliate sul flusso del codice di autorizzazione attivato da questo metodo [, vedere Microsoft Identity Platform e il flusso del codice di autorizzazione OAuth 2,0](v2-oauth2-auth-code-flow.md) .

# <a name="pythontabpython"></a>[Python](#tab/python)

Nell'esempio Python viene usato Flask. Inizializzazione di Flask e MSAL. Python viene eseguito in [app. py # L1-L17](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e1199b4c3cdcb637cf0d8306832efbd85492e123/app.py#L1-L17)

```Python
import uuid
import flask
import requests
from flask import Flask, render_template, session, request
from flask_session import Session
import msal
import app_config

sess = Session()
app = Flask(__name__)
app.config.from_object('config.Config')
sess.init_app(app)
cache = msal.SerializableTokenCache()
application = msal.ConfidentialClientApplication(
    app_config.CLIENT_ID, authority=app_config.AUTHORITY,
    client_credential=app_config.CLIENT_SECRET,
    token_cache=cache)
```

Questo è MSAL. Python che si occuperà di consentire l'accesso dell'utente. Vedere [app. py # L74-84](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e1199b4c3cdcb637cf0d8306832efbd85492e123/app.py#L74-84)

```Python
@app.route('/authenticate')
def authenticate():
    # Call to the authorize endpoint
    auth_state = str(uuid.uuid4())
    session[(request.cookies.get("session")+'state')] = auth_state
    authorization_url = application.get_authorization_request_url(app_config.SCOPE, state=auth_state,
                                                                  redirect_uri=app_config.REDIRECT_URI)
    resp = flask.Response(status=307)
    resp.headers['location'] = authorization_url
    return resp
```

---

## <a name="next-steps"></a>Passaggi successivi

Nell'articolo successivo verrà illustrato come attivare l'accesso e la disconnessione.

> [!div class="nextstepaction"]
> [Accedere e disconnettersi](scenario-web-app-sign-user-sign-in.md)
