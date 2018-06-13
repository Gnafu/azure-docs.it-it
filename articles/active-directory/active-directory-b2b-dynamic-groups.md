---
title: Gruppi dinamici e Collaborazione B2B di Azure Active Directory | Microsoft Docs
description: Mostra come usare i gruppi dinamici di Azure AD con la funzionalità di collaborazione B2B di Azure Active Directory
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 12/14/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 751502c2be84e9454c507f09a47b609d003ce2c5
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "33927852"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Gruppi dinamici e Collaborazione B2B di Azure Active Directory

## <a name="what-are-dynamic-groups"></a>Che cosa sono i gruppi dinamici
La configurazione dinamica dell'appartenenza a gruppi di sicurezza per Azure Active Directory (Azure AD) è disponibile nel [portale di Azure](https://portal.azure.com). Gli amministratori possono impostare regole per popolare i gruppi creati in Azure AD in base agli attributi utente, ad esempio tipo di utente, reparto o paese. I membri possono essere automaticamente aggiunti o rimossi in un gruppo di sicurezza in base agli attributi. Questi gruppi possono fornire accesso ad applicazioni o a risorse cloud, (documenti, siti di SharePoint) e per assegnare licenze ai membri. Per altre informazioni sui gruppi dinamici, vedere [Gruppi dedicati in Azure Active Directory](active-directory-accessmanagement-dedicated-groups.md).

La [licenza di Azure AD Premium P1 o P2](https://azure.microsoft.com/pricing/details/active-directory/) appropriata è necessaria per creare e usare gruppi dinamici. Maggiori informazioni sono riportate nell’articolo [Creare regole basate su attributi per l'appartenenza dinamica ai gruppi in Azure Active Directory](active-directory-groups-dynamic-membership-azure-portal.md).

## <a name="what-are-the-built-in-dynamic-groups"></a>Che cosa sono i gruppi dinamici predefiniti
Il gruppo dinamico **Tutti gli utenti** consente agli amministratori tenant di creare un gruppo contenente tutti gli utenti nel tenant con un solo clic. Per impostazione predefinita, il gruppo **Tutti gli utenti** include tutti gli utenti della directory, compresi guest e membri.
Nel nuovo portale di amministrazione di Azure Active Directory è possibile scegliere di abilitare il gruppo **Tutti gli utenti** nella visualizzazione Impostazioni gruppo.

![Visualizza l'opzione Abilita il gruppo "Tutti gli utenti" impostata su Sì](media/active-directory-b2b-dynamic-groups/enable-all-users-group.png)

## <a name="hardening-the-all-users-dynamic-group"></a>Applicando la protezione avanzata al gruppo dinamico Tutti gli utenti
Per impostazione predefinita, il gruppo **Tutti gli utenti** contiene anche gli utenti (guest) di Collaborazione B2B. È possibile proteggere ulteriormente il gruppo **Tutti gli utenti** tramite una regola di rimozione degli utenti guest. La figura seguente illustra il gruppo **Tutti gli utenti** modificato per escludere i guest.

![Mostra la regola in cui il tipo di utente non è uguale a guest](media/active-directory-b2b-dynamic-groups/exclude-guest-users.png)

Potrebbe inoltre essere utile creare un nuovo gruppo dinamico che contiene solo gli utenti guest, in modo che sia possibile applicare a essi criteri quali, ad esempio, criteri di accesso condizionale di Azure AD.
Un gruppo può avere questo aspetto:

![Mostra la regola in cui il tipo di utente è uguale a guest](media/active-directory-b2b-dynamic-groups/only-guest-users.png)

## <a name="next-steps"></a>Passaggi successivi

- [Proprietà dell'utente di Collaborazione B2B](active-directory-b2b-user-properties.md)
- [Aggiunta di un utente di Collaborazione B2B a un ruolo](active-directory-b2b-add-guest-to-role.md)
- [Accesso condizionale per gli utenti di Collaborazione B2B](active-directory-b2b-mfa-instructions.md)

