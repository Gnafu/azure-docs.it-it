---
title: Impostare la scadenza dei gruppi di Office 365 - Azure Active Directory | Microsoft Docs
description: Come configurare la scadenza dei gruppi di Office 365 in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/06/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56bfe92de24b9386252ee8719af66cc658948565
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844306"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Configurare i criteri di scadenza per i gruppi di Office 365

Questo articolo descrive come gestire il ciclo di vita dei gruppi di Office 365 impostando i criteri di scadenza. È possibile impostare i criteri di scadenza dei soli gruppi di Office 365 in Azure Active Directory (Azure AD).

Dopo l'impostazione della scadenza di un gruppo:

- Quando la scadenza si avvicina, i proprietari del gruppo ricevono la notifica della necessità di rinnovare il gruppo
- I gruppi che non vengono rinnovati vengono eliminati
- I gruppi di Office 365 eliminati possono essere ripristinati entro 30 giorni dai proprietari o dall'amministratore.

Attualmente può essere configurato un solo criterio di scadenza per i gruppi di Office 365 di un tenant.

> [!NOTE]
> Per la configurazione e l'uso dei criteri di scadenza per i gruppi di Office 365 è necessario disporre delle licenze Azure AD Premium per i membri di tutti i gruppi a cui vengono applicati i criteri di scadenza.

Per informazioni su come scaricare e installare i cmdlet di Azure AD PowerShell, vedere [Azure Active Directory PowerShell for Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) (Azure Active Directory PowerShell per Graph 2.0.0.137).

## <a name="roles-and-permissions"></a>Ruoli e autorizzazioni

Di seguito sono elencati i ruoli che possono configurare e usare la scadenza per i gruppi di Office 365 in Azure AD.

Role | Autorizzazioni
-------- | --------
Amministratore globale o Amministratore utenti | Questo ruolo consente di creare, leggere, aggiornare o eliminare le impostazioni dei criteri di scadenza per i gruppi di Office 365<br>Questo ruolo consente di rinnovare qualsiasi gruppo di Office 365
Utente | Questo ruolo consente di rinnovare un gruppo di Office 365 di cui è proprietario<br>Questo ruolo consente di ripristinare un gruppo di Office 365 di cui è proprietario<br>Questo ruolo consente di leggere le impostazioni dei criteri di scadenza

Per altre informazioni sulle autorizzazioni per ripristinare un gruppo eliminato, vedere [Ripristinare un gruppo di Office 365 eliminato in Azure Active Directory](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Impostare la scadenza dei gruppi

1. Aprire l'interfaccia di [amministrazione di Azure ad](https://aad.portal.azure.com) con un account che sia un amministratore globale nell'organizzazione Azure ad.

2. Selezionare **gruppi**, quindi selezionare **scadenza** per aprire le impostazioni di scadenza.
  
   ![Impostazioni di scadenza per i gruppi](./media/groups-lifecycle/expiration-settings.png)

3. Nella pagina **scadenza** è possibile:

  - Impostare la durata del gruppo in giorni. È possibile selezionare uno dei valori predefiniti oppure un valore personalizzato (che deve essere di 31 giorni o più).
  - Specificare un indirizzo e-mail per l'invio delle notifiche di rinnovo e di scadenza quando un gruppo non ha un proprietario.
  - Selezionare i gruppi di Office 365 che scadono. È possibile impostare la scadenza per:
    - **Tutto** Gruppi di Office 365
    - Elenco di gruppi di Office 365 **selezionati**
    - **Nessuno** per limitare la scadenza per tutti i gruppi
  - Al termine salvare le impostazioni facendo clic su **Salva**.

> [!NOTE]
> Quando si configura per la prima volta la scadenza, tutti i gruppi antecedenti all'intervallo di scadenza vengono impostati su 30 giorni fino alla scadenza, a meno che il proprietario non lo rinnovi. La prima notifica di rinnovo viene inviata tramite posta elettronica entro un giorno.
>
> Quando un gruppo dinamico viene eliminato e ripristinato, viene considerato come un nuovo gruppo e popolato nuovamente in base alla regola. Questo processo può richiedere fino a 24 ore.
>
> Le notifiche di scadenza per i gruppi usati nei team vengono visualizzate nel feed dei proprietari dei team.

## <a name="email-notifications"></a>Notifiche tramite posta elettronica

Le notifiche tramite posta elettronica di questo tipo vengono inviate ai proprietari del gruppo di Office 365 30 giorni, 15 giorni e 1 giorno prima della scadenza del gruppo. La lingua del messaggio di posta elettronica è determinata dalla lingua preferita del proprietario dei gruppi o dall'impostazione della lingua Azure AD. Se il proprietario del gruppo ha definito una lingua preferita o più proprietari hanno la stessa lingua preferita, viene utilizzata tale lingua. Per tutti gli altri casi, viene utilizzata l'impostazione della lingua Azure AD.

![Notifiche di posta elettronica scadenza](./media/groups-lifecycle/expiration-notification.png)

Dalla notifica di posta elettronica **Rinnova il gruppo** i proprietari del gruppo possono accedere direttamente alla pagina dei dettagli del gruppo nel Riquadro di accesso. In questa area è possibile ottenere altre informazioni sul gruppo, ad esempio la descrizione, la data dell'ultimo rinnovo, la data di scadenza, e anche procedere con il rinnovo del gruppo. La pagina dei dettagli del gruppo include anche collegamenti alle risorse del gruppo di Office 365, che consentono al proprietario del gruppo di visualizzare facilmente il contenuto e l'attività del gruppo.

Alla scadenza il gruppo viene eliminato, un giorno dopo la data di scadenza. Viene inviata una notifica tramite posta elettronica simile alla seguente ai proprietari del gruppo di Office 365 per informarli della scadenza e della conseguente eliminazione del gruppo di Office 365.

![Notifiche di posta elettronica per l'eliminazione del gruppo](./media/groups-lifecycle/deletion-notification.png)

Il gruppo può essere ripristinato entro 30 giorni dalla relativa eliminazione selezionando **Ripristina gruppo** oppure usando i cmdlet di PowerShell, come descritto in [Ripristinare un gruppo di Office 365 eliminato in Azure Active Directory](groups-restore-deleted.md). Si noti che il periodo di ripristino del gruppo di 30 giorni non è personalizzabile.

Se il gruppo che si sta ripristinando contiene documenti, siti di SharePoint o altri oggetti persistenti, potrebbero essere necessarie fino a 24 ore per ripristinare completamente il gruppo e il relativo contenuto.

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>Come recuperare la data di scadenza del gruppo di Office 365
Oltre al pannello di accesso in cui gli utenti possono visualizzare i dettagli dei gruppi, tra cui la data di scadenza e l'ultima data di rinnovo, è possibile recuperare la data di scadenza di un gruppo di Office 365 dall'API REST beta di Microsoft Graph expirationDateTime come proprietà del gruppo è stato abilitato in Microsoft Graph beta. Può essere recuperato con una richiesta GET. Per altri dettagli, vedere [questo esempio](https://docs.microsoft.com/graph/api/group-get?view=graph-rest-beta#example).

> [!NOTE]
> Per gestire le appartenenze ai gruppi nel pannello di accesso, è necessario impostare "limita l'accesso ai gruppi nel pannello di accesso" su "No" nell'impostazione generale gruppi Azure Active Directory.

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Come funziona la scadenza dei gruppi di Office 365 con una cassetta postale in blocco a fini giudiziari
Quando un gruppo scade e viene eliminato, 30 giorni i dati del gruppo presenti in app come Planner, Siti o Teams vengono eliminati definitivamente, mentre la cassetta postale del gruppo che è in blocco a fini giudiziari viene mantenuta. L'amministratore può usare i cmdlet di Exchange per ripristinare la cassetta postale e recuperare i dati. 

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Come funziona la scadenza dei gruppi di Office 365 con i criteri di conservazione
I criteri di conservazione vengono configurati tramite il Centro sicurezza e conformità. Se per i gruppi di Office 365 sono stati configurati criteri di conservazione, quando un gruppo scade e viene eliminato le conversazioni presenti nella cassetta postale del gruppo e i file presenti nel sito del gruppo vengono mantenuti nell'apposito contenitore per uno specifico numero di giorni definito nei criteri di conservazione. Dopo la scadenza gli utenti non potranno vedere il gruppo o i relativi contenuti, ma potranno recuperare i dati del sito e della cassetta postale tramite e-discovery.

## <a name="powershell-examples"></a>Esempi di PowerShell
Di seguito sono riportati alcuni esempi di come è possibile usare i cmdlet di PowerShell per configurare le impostazioni di scadenza per i gruppi di Office 365 nell'organizzazione Azure AD:

1. Installare il modulo PowerShell v 2.0 e accedere al prompt di PowerShell:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. Configurare le impostazioni di scadenza usare il cmdlet New-AzureADMSGroupLifecyclePolicy per impostare la durata di tutti i gruppi di Office 365 nell'organizzazione Azure AD su 365 giorni. Le notifiche di rinnovo per i gruppi di Office 365 senza proprietari verranno inviate a emailaddress@contoso.com.
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. Recuperare il criterio Get-AzureADMSGroupLifecyclePolicy esistente: Questo cmdlet recupera le impostazioni di scadenza dei gruppi di Office 365 correnti configurate. Questo esempio contiene gli elementi seguenti:

   - ID dei criteri
   - La durata di tutti i gruppi di Office 365 nell'organizzazione Azure AD è impostata su 365 giorni
   - Notifiche di rinnovo per i gruppi di Office 365 senza proprietari che verranno inviate a emailaddress@contoso.com
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. Aggiornare il criterio Set-AzureADMSGroupLifecyclePolicy esistente: Questo cmdlet viene usato per aggiornare un criterio esistente. Nell'esempio seguente la durata del gruppo nei criteri esistenti viene modificata da 365 giorni a 180 giorni.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. Aggiungere gruppi specifici al criterio Add-AzureADMSLifecyclePolicyGroup: Questo cmdlet aggiunge un gruppo di criteri di ciclo di vita. Ad esempio:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. Rimuovere il criterio Remove-AzureADMSGroupLifecyclePolicy esistente: Questo cmdlet elimina le impostazioni di scadenza dei gruppi di Office 365, ma richiede l'ID del criterio. Verrà così disabilitata la scadenza per i gruppi di Office 365.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
I cmdlet seguenti possono essere usati per configurare i criteri in modo più dettagliato. Per ulteriori informazioni, vedere la [documentazione di PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups).

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Get-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Passaggi successivi

Questi articoli forniscono informazioni aggiuntive sui gruppi di Azure AD.

- [Vedere i gruppi esistenti](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Gestire le impostazioni di un gruppo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gestire i membri di un gruppo](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Gestire le appartenenze di un gruppo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gestire le regole dinamiche per gli utenti in un gruppo](groups-dynamic-membership.md)
