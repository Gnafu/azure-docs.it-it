---
title: Abilitare Enterprise State Roaming in Azure Active Directory | Microsoft Docs
description: Domande frequenti sulle impostazioni del servizio Enterprise State Roaming nei dispositivi Windows.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0f4a260bb52fb10147f6d6b9e74aa5cd4fd0e1a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562147"
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Abilitare Enterprise State Roaming in Azure Active Directory
Enterprise State Roaming è disponibile per qualsiasi organizzazione con una licenza Azure AD Premium o Enterprise Mobility + Security (EMS). Per altre informazioni su come ottenere una sottoscrizione di Azure AD, vedere la [pagina del prodotto Azure AD](https://azure.microsoft.com/services/active-directory) .

Quando si abilita il servizio Enterprise State Roaming, all'organizzazione viene concessa automaticamente una licenza gratuita e a uso limitato per la protezione Azure Rights Management da Azure Information Protection. Questa sottoscrizione gratuita è limitata a crittografia e decrittografia delle impostazioni aziendali e dei dati delle applicazioni sincronizzati da Enterprise State Roaming. È necessario avere una [sottoscrizione a pagamento](https://azure.microsoft.com/pricing/details/information-protection/) per usare le funzionalità complete del servizio Azure Rights Management.

## <a name="to-enable-enterprise-state-roaming"></a>Per abilitare Enterprise State Roaming

1. Accedere all'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com/).
1. Selezionare **Azure Active Directory** &gt; **Dispositivi** &gt; **Enterprise State Roaming**.
1. Selezionare **Gli utenti possono sincronizzare le impostazioni e i dati delle app su tutti i dispositivi**. Per altre informazioni, vedere [Configurare le impostazioni dei dispositivi](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).
  
   ![Immagine dell'impostazione del dispositivo Gli utenti possono sincronizzare le impostazioni e i dati delle app su tutti i dispositivi](./media/enterprise-state-roaming-enable/device-settings.png)
  
Per consentire a un dispositivo Windows 10 di usare il servizio Enterprise State Roaming, è necessario che il dispositivo effettui l'autenticazione con un'identità di Azure AD. Per i dispositivi aggiunti ad Azure AD, l'identità di accesso principale dell'utente è l'identità di Azure AD, quindi non sono necessarie operazioni aggiuntive di configurazione. Per i dispositivi che usano Active Directory in locale, l'amministratore IT deve [configurare i dispositivi ibridi aggiunti ad Azure Active Directory](hybrid-azuread-join-manual-steps.md). 

## <a name="data-storage"></a>Archiviazione dei dati
I dati del servizio Enterprise State Roaming sono ospitati in una o più [aree di Azure](https://azure.microsoft.com/regions/) ottimali per il valore relativo al paese/all'area geografica impostati nell'istanza di Azure Active Directory. I dati del servizio Enterprise State Roaming vengono partizionati in base alle tre principali aree geografiche: America del Nord, EMEA e Asia Pacifico. I dati del servizio Enterprise State Roaming per il tenant si trovano in locale con l'area geografica e non vengono replicati tra le aree.  Ad esempio:

| Valore di paese/area geografica | dati ospitati in |
| -------------------- | ------------------------ |
| Paese/area EMEA, ad esempio Francia o Zambia | Una o più aree di Azure in Europa |
| Un paese nordamericano, ad esempio Stati Uniti o Canada | Una o più aree di Azure negli Stati Uniti |
| Un paese/regione APAC, ad esempio Australia o Nuova Zelanda | Una o più aree di Azure in Asia |
| Aree America del Sud e Antartide | Una o più aree di Azure negli Stati Uniti |

Il valore relativo al paese/all'area geografica viene impostato come parte del processo di creazione della directory Azure AD e non può essere modificato successivamente. Se sono necessarie altre informazioni sulla posizione di archiviazione dei dati, inviare un ticket al [supporto tecnico Azure](https://azure.microsoft.com/support/options/).

## <a name="view-per-user-device-sync-status"></a>Visualizzare lo stato di sincronizzazione dei dispositivi per ogni utente
Seguire questi passaggi per visualizzare un report di stato della sincronizzazione dei dispositivi per ogni utente.

1. Accedere all'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com/).
1. Selezionare **Azure Active Directory** &gt; **Utenti** &gt; **Tutti gli utenti**.
1. Selezionare l'utente e quindi selezionare **Dispositivi**.
1. In **Mostra** selezionare **Impostazioni di sincronizzazione dispositivi e dati dell'app aziendali** per mostrare lo stato della sincronizzazione.
  
   ![Immagine dell'impostazione per la sincronizzazione di dispositivi e dati](./media/enterprise-state-roaming-enable/sync-status.png)
  
1. Se sono presenti dispositivi inclusi nella sincronizzazione per questo utente, tali dispositivi vengono visualizzati, come illustrato di seguito.
  
   ![Immagine dei dati a colonne relativi alla sincronizzazione dei dispositivi](./media/enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Conservazione dei dati
I dati sincronizzati con il cloud Microsoft tramite Enterprise State Roaming vengono mantenuti fino alla loro eliminazione manuale o fino a quando non viene determinato che non sono aggiornati. 

### <a name="explicit-deletion"></a>Eliminazione esplicita
I dati vengono eliminati in modo esplicito quando un amministratore di Azure elimina un utente o una directory oppure quando richiede esplicitamente l'eliminazione dei dati in altro modo.

* **Eliminazione dell'utente**: quando un utente viene eliminato in Azure AD, i dati mobili dell'account utente vengono eliminati dopo 90-180 giorni. 
* **Eliminazione della directory**: l'eliminazione di un'intera directory in Azure AD è un'operazione immediata. Tutti i dati delle impostazioni associati con quella directory vengono eliminati dopo 90-180 giorni. 
* **Eliminazione su richiesta**: se l'amministratore di Azure AD vuole eliminare manualmente i dati delle impostazioni o di un utente specifico, può inviare un ticket al [supporto tecnico di Azure](https://azure.microsoft.com/support/). 

### <a name="stale-data-deletion"></a>Eliminazione dei dati non aggiornati
I dati a cui non è stato eseguito l'accesso per un anno ("periodo di conservazione") verranno considerati non aggiornati e potrebbero essere eliminati dal cloud Microsoft. Il periodo di conservazione è soggetto a modifiche ma non sarà inferiore a 90 giorni. I dati obsoleti possono essere un set specifico di impostazioni di Windows/dell'applicazione o tutte le impostazioni per un utente. Ad esempio:

* Se nessun dispositivo accede a una raccolta specifica di impostazioni, ad esempio se un'applicazione viene rimossa dal dispositivo o un gruppo di impostazioni come "Tema" viene disabilitato per tutti i dispositivi di un utente, la raccolta diventa obsoleta dopo il periodo di conservazione e potrebbe essere eliminata. 
* Se un utente ha disattivato la sincronizzazione delle impostazioni in tutti i dispositivi, non verrà eseguito l'accesso a nessuno dei dati delle impostazioni e tutti i dati delle impostazioni per quell'utente diventeranno obsoleti e potranno essere eliminati dopo il periodo di conservazione. 
* Se l'amministratore della directory di Azure AD disattiva il servizio Enterprise State Roaming per l'intera directory, tutti gli utenti in tale directory non sincronizzeranno più le impostazioni e tutti i dati delle impostazioni per tutti gli utenti diventeranno obsoleti e potranno essere eliminati allo scadere del periodo di conservazione. 

### <a name="deleted-data-recovery"></a>Recupero di dati eliminati
Il criterio relativo alla conservazione dei dati non può essere configurato. I dati eliminati in modo definitivo non sono recuperabili. Tuttavia, i dati delle impostazioni vengono eliminati solo dal cloud Microsoft e non dal dispositivo dell'utente finale. Se un dispositivo si riconnette successivamente al servizio Enterprise State Roaming, le impostazioni vengono sincronizzate e archiviate di nuovo nel cloud Microsoft.

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di Enterprise State Roaming](enterprise-state-roaming-overview.md)
* [Domande frequenti su impostazioni e dati in roaming](enterprise-state-roaming-faqs.md)
* [Criteri di gruppo e impostazioni del software MDM per la sincronizzazione delle impostazioni](enterprise-state-roaming-group-policy-settings.md)
* [Riferimento alle impostazioni di roaming di Windows 10](enterprise-state-roaming-windows-settings-reference.md)
* [Risoluzione dei problemi](enterprise-state-roaming-troubleshooting.md)
