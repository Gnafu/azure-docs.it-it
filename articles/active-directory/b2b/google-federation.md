---
title: Aggiunge Google come provider di identità per B2B - Azure Active Directory | Microsoft Docs
description: Configurare una federazione con Google per consentire agli utenti guest di accedere alle app di Azure AD con il proprio account Gmail
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 735c3db14963c1f3cfe700a97dee9fedb70e29f5
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441117"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users-preview"></a>Aggiunge Google come provider di identità per gli utenti guest B2B (anteprima)

|     |
| --- |
| Federazione di Google è una funzionalità in anteprima pubblica di Azure Active Directory. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Configurando una federazione con Google, è possibile consentire agli utenti invitati di accedere alle app e alle risorse condivise con i propri account Google, senza dover creare account Microsoft (MSA) o account Azure AD.  
> [!NOTE]
> Gli utenti guest di Google devono accedere tramite un collegamento che include il contesto tenant, ad esempio `https://myapps.microsoft.com/?tenantid=<tenant id>` o `https://portal.azure.com/<tenant id>` oppure, nel caso di un dominio verificato, `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`. È possibile usare anche collegamenti diretti alle applicazioni e alle risorse, purché includano tale contesto. Gli utenti guest attualmente non possono accedere tramite endpoint privi di un contesto tenant. Se si usa ad esempio `https://myapps.microsoft.com`, `https://portal.azure.com` o l'endpoint comune di Teams, verrà generato un errore.
 
## <a name="what-is-the-experience-for-the-google-user"></a>Qual è l'esperienza per l'utente di Google?
Quando si invia un invito a un utente di Google Gmail, l'utente guest deve accedere alle app o alle risorse condivise tramite un collegamento che include il contesto tenant. L'esperienza varia a seconda che sia stato già eseguito o meno l'accesso a Google:
  - Se l'utente guest non ha ancora eseguito l'accesso a Google, gli verrà chiesto di farlo.
  - Se l'utente guest ha già eseguito l'accesso a Google, gli verrà chiesto di scegliere un account da usare. L'utente deve scegliere l'account usato nell'invito.

Se viene visualizzato un errore a causa dell'intestazione troppo lunga, l'utente guest può provare a cancellare i cookie oppure aprire una finestra privata o in incognito e tentare nuovamente l'accesso.

![Screenshot di Google nella pagina di accesso](media/google-federation/google-sign-in.png)

## <a name="step-1-configure-a-google-developer-project"></a>Passaggio 1: Configurare un progetto di Google Developers
Prima di tutto creare un nuovo progetto nella console di Google Developers per ottenere un ID client e un segreto client che sarà possibile aggiungere ad Azure AD in un secondo momento. 
1. Passare alle API di Google all'indirizzo https://console.developers.google.com e accedere con l'account Google. È consigliabile usare l'account condiviso di un team Google.
2. Creare un nuovo progetto: nel dashboard selezionare **Crea progetto** e successivamente **Crea**. Nella pagina New Project (Nuovo progetto) immettere un nome in **Project Name** (Nome progetto) e quindi selezionare **Create** (Crea).
   
   ![Screenshot che mostra la pagina di un nuovo progetto di Google](media/google-federation/google-new-project.png)

3. Assicurarsi che il nuovo progetto sia selezionato nel menu dei progetti. Aprire quindi il menu in alto a sinistra e selezionare **APIs & Services** (API e servizi) > **Credentials** (Credenziali).

   ![Screenshot che illustra l'API di Google opzione delle credenziali](media/google-federation/google-api.png)
 
4. Scegliere la scheda **OAuth consent screen** (Schermata consenso OAuth) e immettere un **Application name** (Nome applicazione). Lasciare invariate le altre impostazioni.

   ![Screenshot che mostra di opzione schermata consenso Google OAuth](media/google-federation/google-oauth-consent-screen.png)

5. Scorrere fino alla sezione **Authorized domains** (Domini autorizzati) e immettere microsoftonline.com.

   ![Screenshot che mostra la sezione di domini Authorized](media/google-federation/google-oauth-authorized-domains.png)

6. Selezionare **Salva**.

7. Selezionare la scheda **Credentials** (Credenziali). Dal menu **Create credentials** (Crea credenziali) scegliere **OAuth client ID** (ID client OAuth).

   ![Screenshot di Google APIs creare opzione relativa alle credenziali](media/google-federation/google-api-credentials.png)

8. In **Application type** (Tipo di applicazione) scegliere **Web application** (Applicazione Web) e quindi in **Authorized redirect URIs** (URI di reindirizzamento autorizzati) immettere gli URI seguenti:
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(dove `<directory id>` è l'ID directory)
   
     > [!NOTE]
     > Per trovare l'ID directory, passare a https://portal.azure.com e quindi in **Azure Active Directory** scegliere **Proprietà** e copiare il valore di **ID directory**.

   ![Screenshot che mostra il Authorized sezione relativa agli URI di reindirizzamento](media/google-federation/google-create-oauth-client-id.png)

9. Selezionare **Create**. Copiare l'ID client e il segreto client, che verranno usati quando si aggiungerà il provider di identità nel portale di Azure AD.

   ![Screenshot che mostra il client di OAuth ID e il segreto client](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Passaggio 2: Configurare la federazione con Google in Azure AD 
Si imposteranno ora l'ID client e il segreto client di Google immettendoli nel portale di Azure AD o tramite PowerShell. Assicurarsi di testare la configurazione della federazione con Google inviando un invito al proprio indirizzo Gmail e quindi provando a riscattare l'invito con l'account Google invitato. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>Per configurare la federazione con Google nel portale di Azure AD 
1. Accedere al [portale di Azure](https://portal.azure.com). Nel riquadro sinistro selezionare **Azure Active Directory**. 
2. Selezionare **Relazioni aziendali**.
3. Selezionare **Provider di identità** e quindi fare clic sul pulsante **Google**.
4. Immettere un nome. Immettere quindi l'ID client e il segreto client ottenuti in precedenza. Selezionare **Salva**. 

   ![Screenshot che mostra la pagina provider di identità Google aggiungere](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>Per configurare la federazione con Google tramite PowerShell
1. Installare la versione più recente del modulo di Azure AD PowerShell per Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Eseguire il comando seguente: `Connect-AzureAD`.
3. Al prompt di accesso, accedere con l'account di amministratore globale gestito.  
4. Eseguire il comando seguente: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Usare l'ID client e il segreto client dall'app creata nel "Passaggio 1: Configurare un progetto di Google Developers". Per altre informazioni, vedere l'articolo relativo al cmdlet [New-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview). 
 
## <a name="how-do-i-remove-google-federation"></a>Come si rimuove la federazione con Google?
È possibile rimuovere la configurazione della federazione con Google. In questo caso, gli utenti guest di Google che hanno già riscattato l'invito non potranno accedere, ma è possibile concedere loro nuovamente l'accesso alle risorse eliminandoli dalla directory e invitandoli di nuovo. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Per eliminare la federazione con Google nel portale di Azure AD: 
1. Accedere al [portale di Azure](https://portal.azure.com). Nel riquadro sinistro selezionare **Azure Active Directory**. 
2. Selezionare **Relazioni aziendali**.
3. Selezionare **provider di identità**.
4. Nel **Google** , selezionare il menu di scelta rapida ( **...** ) e quindi selezionare **eliminare**. 
   
   ![Screenshot che mostra l'opzione di eliminazione per il provider di identità di social networking](media/google-federation/google-social-identity-providers.png)

1. Selezionare **Sì** per confermare l'eliminazione. 

### <a name="to-delete-google-federation-by-using-powershell"></a>Per eliminare la federazione con Google tramite PowerShell: 
1. Installare la versione più recente del modulo di Azure AD PowerShell per Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Eseguire `Connect-AzureAD`.  
4. Al prompt di accesso, accedere con l'account amministratore globale gestito.  
5. Immettere il comando seguente:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Per altre informazioni, vedere l'articolo relativo al cmdlet [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 
