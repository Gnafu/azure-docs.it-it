---
title: Approcci alla migrazione degli utenti in Azure Active Directory B2C
description: Vengono illustrati i concetti di base e avanzati sulla migrazione degli utenti usando il Azure AD API Graph e, facoltativamente, usando Azure AD B2C criteri personalizzati.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c922799b650de7f921cc0493eb3feb2ad90b9d92
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183156"
---
# <a name="azure-active-directory-b2c-user-migration"></a>Azure Active Directory B2C: Migrazione degli utenti

Quando si esegue la migrazione del provider di identità a Azure Active Directory B2C (Azure AD B2C), potrebbe essere necessario eseguire la migrazione degli account utente. Questo articolo spiega come eseguire la migrazione degli account utente esistenti da qualsiasi provider di identità ad Azure AD B2C. L'articolo non è destinato a essere prescrittivo, ma descrive invece alcuni scenari. Lo sviluppatore è responsabile dell'idoneità di ogni approccio.

## <a name="user-migration-flows"></a>Flussi di migrazione degli utenti

Con Azure AD B2C, è possibile eseguire la migrazione degli utenti tramite la [API Graph Azure ad][B2C-GraphQuickStart]. Il processo di migrazione degli utenti rientra in due flussi:

- **Pre-migrazione**: questo flusso si applica quando si ha accesso alle credenziali di un utente (nome utente e password) o se le credenziali sono crittografate ma è possibile decrittografarle. Il processo di pre-migrazione include la lettura degli utenti dal vecchio provider di identità e la creazione di nuovi account nella directory di Azure AD B2C.

- **Pre-migrazione e reimpostazione della password**: questo flusso si applica quando la password di un utente non è accessibile. Ad esempio:
  - La password è archiviata in formato HASH.
  - La password è archiviata in un provider di identità a cui non è possibile accedere. Il vecchio provider di identità convalida le credenziali dell'utente chiamando un servizio Web.

In entrambi i flussi, eseguire prima il processo di pre-migrazione, leggere gli utenti dal vecchio provider di identità e creare nuovi account nella directory di Azure AD B2C. Se non si dispone della password, è necessario creare l'account usando una password generata in modo casuale. Chiedere quindi all'utente di cambiare la password oppure al primo accesso Azure AD B2C chiederà all'utente di reimpostarla.

## <a name="password-policy"></a>Criteri per le password

I criteri password di Azure AD B2C (per gli account locali) si basano su quelli di Azure AD. I criteri di Azure AD B2C iscrizione o accesso e reimpostazione della password usano il livello di attendibilità della password "forte" e non scadono alcuna password. Per altre informazioni, vedere [Criteri password in Azure AD][AD-PasswordPolicies].

Se gli account di cui si vuole eseguire la migrazione hanno un livello di complessità della password inferiore al [livello avanzato applicato da Azure AD B2C][AD-PasswordPolicies], è possibile disabilitare il requisito per la password complessa. Per modificare i criteri password predefiniti, impostare la proprietà `passwordPolicies` su `DisableStrongPassword`. È ad esempio possibile modificare la richiesta di creazione utente come segue:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>Passaggio 1: Usare l'API Graph di Azure AD per eseguire la migrazione degli utenti

Creare l'account utente di Azure AD B2C con l'API Graph (con la password personale o con una password casuale). Questa sezione descrive il processo di creazione di account utente nella directory di Azure AD B2C usando l'API Graph.

### <a name="step-11-register-your-application-in-your-tenant"></a>Passaggio 1.1: Registrare l'applicazione nel tenant

Per comunicare con l'API Graph è innanzitutto necessario avere un account di servizio con privilegi amministrativi. In Azure AD registrare un'applicazione e l'autenticazione ad Azure AD. Le credenziali dell'applicazione sono l'**ID applicazione** e il **Segreto dell'applicazione**. L'applicazione agisce autonomamente, non come utente, per chiamare l'API Graph.

Per prima cosa registrare l'applicazione di migrazione in Azure AD. Creare quindi la chiave dell'applicazione (segreto) e impostare l'applicazione con privilegi di scrittura.

1. Accedere al [portale di Azure][Portal].
1. Selezionare il filtro **directory + sottoscrizione** nella sezione in alto a destra del portale.
1. Selezionare la directory che contiene il tenant del Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure Active Directory** (*non* Azure ad B2C). Per individuarlo, potrebbe essere necessario selezionare **tutti i servizi**.
1. Selezionare **registrazioni app (legacy)** .
1. Selezionare **Registrazione nuova applicazione**.

   ![Azure Active Directory e Registrazioni app voci di menu evidenziate](media/active-directory-b2c-user-migration/pre-migration-app-registration.png)

1. Creare una nuova applicazione seguendo questa procedura:

   - Per **Nome** usare *B2CUserMigration* o qualsiasi altro nome.
   - Per **tipo di applicazione**selezionare **app Web/API**.
   - Per l' **URL di accesso**, usare `https://localhost` (non è pertinente per questa applicazione).
   - Selezionare **Create**.

    Dopo aver creato l'applicazione, viene visualizzata la pagina dell' **app registrata** che mostra le relative proprietà.
1. Copiare l' **ID applicazione**dell'applicazione e salvarlo per un momento successivo.

### <a name="step-12-create-the-application-secret"></a>Passaggio 1.2: Creare il segreto dell'applicazione

1. Nella pagina **app registrata** selezionare **Impostazioni**.
1. Selezionare **Chiavi**.
1. In **password**aggiungere una nuova chiave (nota anche come segreto client) denominata *MyClientSecret* o un altro nome a scelta, selezionare una finestra di scadenza, fare clic su **Salva**e quindi copiare il valore della chiave per usarlo in seguito.

    ![Voce di menu valore e chiavi dell'ID applicazione evidenziata in portale di Azure](media/active-directory-b2c-user-migration/pre-migration-app-id-and-key.png)

### <a name="step-13-grant-administrative-permission-to-your-application"></a>Passaggio 1.3: Concedere l'autorizzazione amministrativa all'applicazione

1. Nel menu **Impostazioni** selezionare **autorizzazioni necessarie**.
1. Selezionare **Windows Azure Active Directory**.
1. Nel riquadro **Abilita accesso** in **Autorizzazioni applicazione** selezionare **Leggi e scrivi i dati della directory** e quindi **Salva**.
1. Nel riquadro **autorizzazioni necessarie** selezionare **Concedi autorizzazioni**, quindi selezionare **Sì**.

   ![Casella di controllo lettura/scrittura directory, Salva e concedi le autorizzazioni evidenziate](media/active-directory-b2c-user-migration/pre-migration-app-registration-permissions.png)

A questo punto è disponibile un'applicazione con le autorizzazioni per creare, leggere e aggiornare gli utenti dal tenant Azure AD B2C.

### <a name="step-14-optional-environment-cleanup"></a>Passaggio 1.4: (Facoltativo) Pulizia dell'ambiente

Le autorizzazioni di lettura e scrittura dei dati della directory *non* includono il diritto di eliminare utenti. Per consentire all'applicazione di eliminare utenti, ovvero di pulire l'ambiente, è necessario un passaggio aggiuntivo che prevede l'esecuzione di PowerShell per impostare le autorizzazioni di amministratore account utente. In caso contrario, è possibile passare alla sezione successiva.

> [!IMPORTANT]
> È necessario usare un account di amministratore tenant B2C che sia *locale* in relazione al tenant B2C. La sintassi del nome dell'account è *admin\@contosob2c.onmicrosoft.com*.

In questo script di PowerShell, che richiede il [modulo Azure ad PowerShell V2][AD-Powershell], seguire questa procedura:

1. Connettersi al servizio online. A tale scopo, eseguire il cmdlet `Connect-AzureAD` al prompt dei comandi di Windows PowerShell e immettere le credenziali.

1. Usare l'**ID applicazione** per assegnare all'applicazione il ruolo di amministratore di account utente. Questi ruoli hanno identificatori noti, pertanto è sufficiente immettere l'**ID applicazione** nello script.

```powershell
# NOTE: This script REQUIRES the Azure AD PowerShell V2 module
#       https://docs.microsoft.com/powershell/azure/active-directory/install-adv2

Connect-AzureAD

$AppId = "<Your application ID>"

# Fetch Azure AD application to assign to role
$roleMember = Get-AzureADServicePrincipal -Filter "AppId eq '$AppId'"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add application to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
```

Modificare il valore `$AppId` con l'**ID applicazione** di Azure AD.

## <a name="step-2-pre-migration-application-sample"></a>Passaggio 2: Esempio di applicazione di pre-migrazione

L'esempio di codice di pre-migrazione è reperibile nel repository GitHub `azure-ad-b2c/user-migration` gestito dalla community:

[Azure-ad-B2C/utente-migrazione/pre-migrazione][UserMigrationSample-code] GitHub

### <a name="step-21-edit-the-migration-data-file"></a>Passaggio 2.1: Modificare il file dei dati di migrazione

L'app di esempio usa un file JSON che contiene dati utente fittizi. Dopo aver eseguito correttamente l'esempio è possibile modificare il codice per usare i dati del proprio database. In alternativa, è possibile esportare il profilo utente in un file JSON e impostare l'app in modo che usi questo file.

Per modificare il file JSON, aprire la soluzione Visual Studio `AADB2C.UserMigration.sln`. Nel progetto `AADB2C.UserMigration` aprire il file `UsersData.json`.

![Parte del file UsersData. JSON che mostra i blocchi JSON di due utenti](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Come si può notare, il file contiene un elenco di entità utente. Ogni entità utente ha le proprietà seguenti:

- email
- displayName
- firstName
- lastName
- password (il campo può essere vuoto)

> [!NOTE]
> In fase di compilazione, Visual Studio copia il file nella directory `bin`.

### <a name="step-22-configure-the-application-settings"></a>Passaggio 2.2: Configurare le impostazioni dell'applicazione

Nel progetto `AADB2C.UserMigration` aprire il file *App.config*. Sostituire le impostazioni dell'app seguenti con valori personalizzati:

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Client Secret Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users' data; for example, UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure table string}" />
</appSettings>
```

> [!NOTE]
> - L'uso della stringa di connessione di una tabella di Azure è descritto nelle sezioni successive.
> - Il nome del tenant B2C è il dominio immesso durante la creazione del tenant ed è visualizzato nel portale di Azure. Il nome del tenant termina in genere con il suffisso *.onmicrosoft.com* (ad esempio, *contosob2c.onmicrosoft.com*).

### <a name="step-23-run-the-pre-migration-process"></a>Passaggio 2.3: Eseguire il processo di pre-migrazione

Fare clic con il pulsante destro del mouse sulla soluzione `AADB2C.UserMigration` e ricompilare l'esempio. Se l'operazione riesce, sarà disponibile un file eseguibile `UserMigration.exe` in `AADB2C.UserMigration\bin\Debug\net461`. Per eseguire il processo di migrazione, usare un parametro della riga di comando tra i seguenti:

- Per **eseguire la migrazione degli utenti con password**, usare il comando `UserMigration.exe 1`.

- Per **eseguire la migrazione degli utenti con una password casuale**, usare il comando `UserMigration.exe 2`. Questa operazione crea anche un'entità tabella di Azure. Più avanti verranno configurati i criteri per chiamare il servizio API REST. Il servizio usa una tabella di Azure per monitorare e gestire il processo di migrazione.

![Finestra del prompt dei comandi che mostra l'output del comando UserMigration. exe](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Passaggio 2.4: Controllare il processo di pre-migrazione

Per convalidare la migrazione, usare uno dei due metodi seguenti:

- Per cercare un utente in base al nome visualizzato, usare il portale di Azure:

   1. Aprire **Azure ad B2C**, quindi selezionare **utenti**.
   1. Nella casella di ricerca digitare il nome visualizzato dell'utente e quindi visualizzarne il profilo.

- Per recuperare un utente in base all'indirizzo e-mail di accesso, usare questa applicazione di esempio:

   1. Eseguire il comando seguente:

      ```Console
          UserMigration.exe 3 {email address}
      ```

      > [!TIP]
      > È anche possibile recuperare un utente in base al nome visualizzato usando il comando seguente: `UserMigration.exe 4 "<Display name>"`.

   1. Aprire il file UserProfile.json in un editor JSON per visualizzare le informazioni dell'utente.

      ![File UserProfile. JSON aperto nell'editor di Visual Studio Code](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

### <a name="step-25-optional-environment-cleanup"></a>Passaggio 2.5: (Facoltativo) Pulizia dell'ambiente

Per eseguire la pulizia del tenant Azure AD e rimuovere utenti dalla directory di Azure AD, eseguire il comando `UserMigration.exe 5`.

> [!NOTE]
> * Per eseguire la pulizia del tenant, configurare le autorizzazioni di amministratore account utente per l'applicazione.
> * L'app di migrazione di esempio esegue la pulizia di tutti gli utenti elencati nel file JSON.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>Passaggio 2.6: Accedere con gli utenti di cui è stata eseguita la migrazione (con password)

Dopo aver eseguito il processo di pre-migrazione con le password degli utenti, gli account saranno pronti e gli utenti potranno accedere all'applicazione usando Azure AD B2C. Se l'accesso alle password degli utenti non è disponibile, passare alla sezione successiva.

## <a name="step-3-help-users-reset-their-password"></a>Passaggio 3: Aiutare gli utenti a reimpostare la password

Nel caso in cui si esegua la migrazione degli utenti con una password casuale, questi dovranno reimpostare la propria password. Per facilitare la reimpostazione della password, inviare un messaggio di benvenuto contenente un collegamento per reimpostare la password.

Per ottenere il collegamento ai criteri di reimpostazione della password, seguire questa procedura. Questa procedura presuppone che sia stato creato in precedenza un [criterio personalizzato](active-directory-b2c-get-started-custom.md)di reimpostazione della password.

1. Selezionare la directory contenente il tenant di Azure AD B2C usando il filtro **directory + sottoscrizione** nella sezione in alto a destra del [portale di Azure](https://portal.azure.com).
1. Selezionare **Azure ad B2C** nel menu a sinistra (o in **tutti i servizi**).
1. In **criteri**selezionare **Framework esperienza di identità**.
1. Selezionare i criteri di reimpostazione della password. Ad esempio, *B2C_1A_PasswordReset*.
1. Selezionare l'applicazione nell'elenco a discesa **Seleziona applicazione** .

    > [!NOTE]
    > **Esegui ora** richiede la registrazione di almeno un'applicazione nel tenant. Per informazioni su come registrare le applicazioni, [vedere Esercitazione: Registrare un'applicazione in Azure Active Directory B2C][B2C-AppRegister].

1. Copiare l'URL visualizzato nella casella di testo **Run Now endpoint** e quindi inviarlo agli utenti.

    ![Pagina Criteri di reimpostazione password con endpoint Run Now evidenziato](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>Passaggio 4: (Facoltativo) Modificare i criteri per controllare e impostare lo stato di migrazione degli utenti

> [!NOTE]
> Per controllare e modificare lo stato di migrazione degli utenti è necessario usare criteri personalizzati. È necessario completare le istruzioni di installazione di [Introduzione ai criteri personalizzati][B2C-GetStartedCustom] .

Quando gli utenti provano a eseguire l'accesso senza prima reimpostare la password, i criteri dovrebbero restituire un messaggio di errore descrittivo, Ad esempio:

> *La password è scaduta. Per reimpostarla, selezionare il collegamento Reimposta password.*

Questo passaggio facoltativo richiede l'uso di criteri personalizzati in Azure AD B2C, come descritto nell'articolo [Introduzione ai criteri personalizzati][B2C-GetStartedCustom].

In questa sezione vengono modificati i criteri per controllare lo stato di migrazione dell'utente all'accesso. Se l'utente non ha cambiato la password, verrà restituito un messaggio di errore HTTP 409 in cui si chiede all'utente di selezionare il collegamento **Password dimenticata?** .

Per monitorare il cambio della password, usare una tabella di Azure. L'esecuzione del processo di pre-migrazione con il parametro `2` della riga di comando crea un'entità utente in una tabella di Azure. Il servizio esegue le operazioni seguenti:

- In fase di accesso, i criteri di Azure AD B2C richiamano il servizio RESTful di migrazione inviando un messaggio e-mail come attestazione di input. Il servizio cerca l'indirizzo e-mail nella tabella di Azure. Se l'indirizzo esiste, il servizio genera un messaggio di errore: *La password deve essere modificata*.

- Dopo che l'utente ha cambiato la password, rimuovere l'entità dalla tabella di Azure.

> [!NOTE]
> L'uso di una tabella di Azure consente di semplificare l'esempio. È possibile archiviare lo stato di migrazione in qualsiasi database o come proprietà personalizzata nell'account Azure AD B2C.

### <a name="41-update-your-application-setting"></a>4.1: Aggiornare l'impostazione dell'applicazione

1. Per testare la demo dell'API RESTful, aprire `AADB2C.UserMigration.sln` in Visual Studio.
1. Nel progetto aprire il file *Web. config.* `AADB2C.UserMigration.API` Sostituire l'impostazione con quella configurata nel [passaggio 2.2](#step-22-configure-the-application-settings):

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>Passaggio 4.2: Distribuire l'applicazione Web nel Servizio app di Azure

In Esplora soluzioni fare clic con il pulsante destro del mouse su `AADB2C.UserMigration.API` e scegliere "Pubblica". Seguire le istruzioni per la pubblicazione in Servizio app di Azure. Per altre informazioni, vedere [Distribuire l'app nel Servizio app di Azure][AppService-Deploy].

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>Passaggio 4.3: Aggiungere un profilo tecnico e la relativa convalida ai criteri

1. In Esplora soluzioni espandere "Elementi di soluzione" e aprire il file dei criteri *TrustFrameworkExtensions.xml*.
1. Modificare i campi `TenantId`, `PublicPolicyUri` e `<TenantId>` da `yourtenant.onmicrosoft.com` al nome del tenant.
1. Nell'elemento sostituire tutte le istanze di `ProxyIdentityExperienceFrameworkAppId` e `IdentityExperienceFrameworkAppId` con gli ID applicazione configurati in Introduzione a [criteri personalizzati][B2C-GetStartedCustom]. `<TechnicalProfile Id="login-NonInteractive">`
1. Nel nodo `<ClaimsProviders>` trovare il frammento di codice XML seguente. Modificare il valore di `ServiceUrl` in modo che punti all'URL di Servizio app di Azure.

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>

        <TechnicalProfile Id="LocalAccountSignIn">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/LoalAccountSignIn</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>

        <TechnicalProfile Id="LocalAccountPasswordReset">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/PasswordUpdated</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

Il profilo tecnico precedente definisce un'attestazione di input: `signInName` (invia come e-mail). All'accesso l'attestazione viene inviata all'endpoint RESTful.

Dopo aver definito il profilo tecnico per l'API RESTful, indicare ai criteri di Azure AD B2C di chiamarlo. Il frammento XML esegue l'override di `SelfAsserted-LocalAccountSignin-Email`, definito nei criteri di base. Il frammento XML aggiunge anche `ValidationTechnicalProfile` con ReferenceId che punta al profilo tecnico `LocalAccountUserMigration`.

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Passaggio 4.4: Caricare i criteri nel tenant

1. Nel [portale di Azure][Portal] passare al [contesto del tenant Azure AD B2C][B2C-NavContext] e quindi selezionare **Azure AD B2C**.
1. Fare clic su **Framework dell'esperienza di gestione delle identità**.
1. Selezionare **Tutti i criteri**.
1. Selezionare **Carica criteri**.
1. Selezionare la casella di controllo **Sovrascrivi il criterio se esistente**.
1. Caricare il file *TrustFrameworkExtensions.xml* e assicurarsi che superi la convalida.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Passaggio 4.5: Testare i criteri personalizzati tramite Esegui adesso

1. Selezionare **Azure ad B2C**e quindi selezionare **Framework esperienza di identità**.
1. Aprire *B2C_1A_signup_signin*, i criteri personalizzati della relying party caricati in precedenza e quindi selezionare **Esegui adesso**.
1. Immettere le credenziali di uno degli utenti migrati e quindi selezionare **Accedi**. L'API REST genera il messaggio di errore seguente:

    ![Pagina di iscrizione dell'accesso che mostra il messaggio di errore di modifica della password](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>Passaggio 4.6: (Facoltativo) Risoluzione dei problemi dell'API REST

È possibile visualizzare e monitorare le informazioni di registrazione quasi in tempo reale.

1. Nel menu delle impostazioni dell'applicazione RESTful, in **Monitoraggio** selezionare **Log di diagnostica**.
1. Impostare **Registrazione applicazioni (file system)** su **Attiva**.
1. Impostare il **Livello** su **Dettagliato**.
1. Selezionare **Salva**

    ![Pagina di configurazione dei log di diagnostica in portale di Azure](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

1. Nel menu **Impostazioni** selezionare **Flusso di registrazione**.
1. Controllare l'output dell'API RESTful.

> [!IMPORTANT]
> Usare i log di diagnostica solo in fase di sviluppo e test. L'output dell'API RESTful può contenere informazioni riservate che non devono essere esposte in produzione.

## <a name="optional-download-the-complete-policy-files"></a>(Facoltativo) Scaricare i file dei criteri completi

Dopo aver completato la procedura dettagliata [Introduzione ai criteri personalizzati][B2C-GetStartedCustom] , è consigliabile compilare lo scenario usando i file dei criteri personalizzati. Per informazioni di riferimento, sono disponibili [file di criteri di esempio][UserMigrationSample-policy].

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/powershell/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample-code]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/source-code
[UserMigrationSample-policy]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/policy
