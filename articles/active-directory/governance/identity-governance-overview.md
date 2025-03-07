---
title: Governance delle identità-Azure Active Directory | Microsoft Docs
description: Azure Active Directory Identity Governance consente di bilanciare la necessità dell'organizzazione di sicurezza e produttività dei dipendenti con i processi e la visibilità corretti.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 08/28/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: a17b64cbfffbe9cabd909bccb16d55c07bba8bb0
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241441"
---
# <a name="what-is-azure-ad-identity-governance"></a>Cos'è Azure AD Identity Governance?

La governance delle identità Azure Active Directory (Azure AD) consente di bilanciare la necessità della sicurezza e della produttività dei dipendenti dell'organizzazione con i processi e la visibilità corretti. Fornisce le funzionalità necessarie a garantire che gli utenti giusti abbiano i diritti di accesso appropriati per le risorse corrette e consente di proteggere, monitorare e controllare l'accesso agli asset critici, assicurando allo stesso tempo la produttività dei dipendenti.  

La governance delle identità consente alle organizzazioni di eseguire le attività seguenti tra dipendenti, partner aziendali e fornitori e servizi e applicazioni:

- Governance del ciclo di vita delle identità
- Governance del ciclo di vita degli accessi
- Amministrazione sicura

In particolare, consente alle organizzazioni di rispondere a queste quattro domande principali:

- Quali utenti hanno accesso a quali risorse?
- In che modo questi utenti usano l'accesso?
- Esistono controlli efficaci a livello aziendale per la gestione degli accessi?
- I revisori possono verificare l'operatività dei controlli?

## <a name="identity-lifecycle"></a>Ciclo di vita delle identità

La governance delle identità consente alle organizzazioni di raggiungere un equilibrio tra la *produttività* e la rapidità con cui un utente può accedere alle risorse necessarie, ad esempio quando partecipa alla propria organizzazione? e *sicurezza* (in che modo dovrebbe cambiare nel tempo l'accesso degli utenti, ad esempio a causa di cambiamenti nello status lavorativo).  Identity Lifecycle Management è la base per la governance delle identità e la governance efficace su larga scala richiede la modernizzazione dell'infrastruttura di gestione del ciclo di vita delle identità delle applicazioni.

![Ciclo di vita delle identità](./media/identity-governance-overview/identity-lifecycle.png)

Per molte organizzazioni, il ciclo di vita delle identità dei dipendenti è associato alla rappresentazione dell'utente in un sistema di gestione delle risorse umane (HCM).  Azure AD Premium gestisce automaticamente le identità degli utenti rappresentati in Workday sia in Active Directory che in Azure Active Directory, come descritto nell'esercitazione [Configurare Workday per il provisioning utenti automatico (anteprima)](../saas-apps/workday-inbound-tutorial.md).  Azure AD Premium include inoltre [Microsoft Identity Manager](/microsoft-identity-manager/), che può importare record dai sistemi HCM locali, come SAP, Oracle eBusiness e Oracle PeopleSoft.

Sempre più spesso, gli scenari aziendali richiedono la collaborazione con utenti esterni. Le funzionalità di [Collaborazione B2B di Azure AD](/azure/active-directory/b2b/) consentono di condividere in modo sicuro applicazioni e servizi con utenti guest e partner esterni provenienti da qualsiasi organizzazione, mantenendo al tempo stesso il controllo sui dati aziendali.

## <a name="access-lifecycle"></a>Ciclo di vita degli accessi

Le organizzazioni hanno bisogno di un processo per gestire l'accesso oltre il provisioning iniziale eseguito al momento della creazione dell'identità di un utente.  Inoltre, le organizzazioni aziendali devono disporre di scalabilità efficiente per poter sviluppare e applicare criteri e controlli di accesso in modo continuativo.

![Ciclo di vita degli accessi](./media/identity-governance-overview/access-lifecycle.png)

In genere, l'IT delega le decisioni in merito all'approvazione dell'accesso ai decision maker aziendali.  Inoltre, l'IT può comprendere gli utenti stessi.  Ad esempio, gli utenti che accedono a dati riservati dei clienti nell'applicazione di marketing di un'azienda in Europa devono conoscere i criteri dell'azienda. Gli utenti guest potrebbero non essere a conoscenza dei requisiti di gestione dei dati in un'organizzazione in cui sono stati invitati.

Le organizzazioni possono automatizzare il processo di gestione del ciclo di vita degli accessi grazie a tecnologie come i [gruppi dinamici](../users-groups-roles/groups-dynamic-membership.md), associati al provisioning degli utenti in [app SaaS](../saas-apps/tutorial-list.md) o [app integrate con SCIM](../manage-apps/use-scim-to-provision-users-and-groups.md).  Possono anche controllare quali [utenti guest hanno accesso alle applicazioni locali](../b2b/hybrid-cloud-to-on-premises.md).  I diritti di accesso possono quindi essere riesaminati regolarmente usando le [verifiche di accesso ricorrenti di Azure AD](access-reviews-overview.md).

Quando un utente tenta di accedere alle applicazioni, Azure AD applica i criteri di [accesso condizionale](/azure/active-directory/conditional-access/) . Ad esempio, i criteri di accesso condizionale possono includere la visualizzazione di condizioni per l' [utilizzo](../conditional-access/terms-of-use.md) e [la garanzia che l'utente abbia accettato tali termini](../conditional-access/require-tou.md) prima di poter accedere a un'applicazione.

## <a name="privileged-access-lifecycle"></a>Ciclo di vita dell'accesso con privilegi

In passato, l'accesso con privilegi è stato descritto da altri fornitori come funzionalità distinte dalla governance delle identità. Tuttavia, in Microsoft, l'accesso con privilegi è un aspetto fondamentale della governance delle identità, in particolare in base al rischio di utilizzo improprio associato a questi diritti di amministratore può causare un'organizzazione. I dipendenti, fornitori e terzisti che acquisiscono diritti amministrativi devono essere controllati.

![Ciclo di vita dell'accesso con privilegi](./media/identity-governance-overview/privileged-access-lifecycle.png)

Azure AD Privileged Identity Management (PIM) fornisce controlli aggiuntivi mirati alla protezione dei diritti di accesso alle risorse in Azure AD, Azure e altri Microsoft Online Services.  Le funzionalità di accesso just-in-time e di avviso di modifica dei ruoli fornite da Azure AD PIM, oltre all'autenticazione a più fattori e all'accesso condizionale, offrono un set completo di controlli di governance per proteggere le risorse aziendali (directory, Office 365 e ruoli delle risorse di Azure. Come con altre forme di accesso, le organizzazioni possono usare le verifiche di accesso per configurare la ricertificazione ricorrente dell'accesso per tutti gli utenti con ruoli di amministratore.

## <a name="least-privileged-roles"></a>Ruoli con privilegi minimi

È consigliabile usare il ruolo con privilegi minimi per eseguire attività amministrative nella governance delle identità. Si consiglia di usare Azure AD PIM per attivare un ruolo in base alle esigenze per eseguire queste attività. Di seguito sono riportati i ruoli della directory con privilegi minimi per configurare le funzionalità di governance delle identità: 

| Funzionalità | Ruolo con privilegi minimi |
| ------- | --------------------- |
| Gestione entitlement | Amministratore utente (ad eccezione dell'aggiunta di siti di SharePoint Online ai cataloghi, che richiede l'amministratore globale) |
| Condizioni per l'utilizzo | Amministratore della sicurezza o amministratore dell'accesso condizionale |
| Verifiche di accesso | Amministratore utente (ad eccezione delle verifiche di accesso dei ruoli di Azure o Azure AD, che richiedono l'amministratore del ruolo con privilegi) |
|Privileged Identity Management | Amministratore ruolo con privilegi |


## <a name="getting-started"></a>Introduzione

Sebbene non esistano soluzioni o raccomandazioni perfette per ogni cliente, le seguenti configurazioni forniscono una guida ai criteri di base che Microsoft consiglia di seguire per garantire una forza lavoro più sicura e produttiva.

- [Configurazioni di identità e accesso dei dispositivi](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Protezione dell'accesso con privilegi](../users-groups-roles/directory-admin-roles-secure.md)

Per iniziare a usare la gestione dei diritti, le verifiche di accesso, Privileged Identity Management e Condizioni per l'utilizzo, è anche possibile consultare la scheda Introduzione di **governance delle identità** nel portale di Azure.

![Guida introduttiva alla governance delle identità](./media/identity-governance-overview/getting-started.png)

Per commenti e suggerimenti sulle funzionalità di governance delle identità, fare clic su **commenti e suggerimenti?** nella portale di Azure per inviare i commenti. Il team esamina regolarmente il feedback.

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è Azure AD gestione dei diritti? (anteprima)](entitlement-management-overview.md)
- [Informazioni sulle verifiche di accesso di Azure AD](access-reviews-overview.md)
- [Che cos'è Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [Operazioni supportate da Condizioni per l'utilizzo](active-directory-tou.md)
