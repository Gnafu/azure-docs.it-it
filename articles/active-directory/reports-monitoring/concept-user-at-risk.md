---
title: Report di sicurezza Utenti contrassegnati per il rischio nel portale di Azure Active Directory | Microsoft Docs
description: Informazioni sul report di sicurezza Utenti contrassegnati per il rischio nel portale di Azure Active Directory
services: active-directory
author: cawrites
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/17/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e6b79c7d5c2ed9744dc00eb1588c35f8ea94a76
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127644"
---
# <a name="users-flagged-for-risk-report-in-the-azure-portal"></a>Report Utenti contrassegnati per il rischio nel portale di Azure

Azure Active Directory (Azure AD) rileva le azioni sospette correlate agli account utente. Per ogni azione rilevata, viene creato un record denominato [rilevamento dei rischi](concept-risk-events.md) .

È possibile accedere ai report di sicurezza dal [portale di Azure](https://portal.azure.com) selezionando il pannello **Azure Active Directory** e quindi passando alla sezione **Sicurezza**. 

I rilevamenti dei rischi rilevati vengono usati per calcolare:

- **Accessi a rischio**. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente. 

- **Utenti contrassegnati per il rischio**. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso. 

Per informazioni su come configurare i criteri per l'attivazione di questi rilevamenti dei rischi, vedere [come configurare i criteri di rischio utente](../identity-protection/howto-user-risk-policy.md). 

![Accessi a rischio](./media/concept-user-at-risk/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-the-users-at-risk-report"></a>Quale licenza di Azure AD è necessaria per accedere al report sugli utenti a rischio?  

Tutte le edizioni di Azure Active Directory offrono report sugli utenti contrassegnati per il rischio. Tuttavia, il livello di granularità dei report varia a seconda delle edizioni: 

- Nelle edizioni **Azure Active Directory Free e Basic**  è incluso un elenco degli utenti contrassegnati per il rischio. 

- Inoltre, la **Azure Active Directory Premium 1** edizione consente di esaminare alcuni dei rilevamenti di rischio sottostanti rilevati per ogni report. 

- L'edizione **Azure Active Directory Premium 2** fornisce le informazioni più dettagliate su tutti i rilevamenti di rischi sottostanti e consente inoltre di configurare criteri di sicurezza che rispondono automaticamente ai livelli di rischio configurati.


## <a name="users-at-risk-report-for-azure-ad-free-and-basic-editions"></a>Report sugli utenti a rischio per le edizioni Azure AD Free e Basic

Il report Utenti contrassegnati per il rischio nelle edizioni Azure AD Free e Basic fornisce un elenco degli account utente che potrebbero essere stati compromessi. 

![Accessi a rischio](./media/concept-user-at-risk/03.png)

La selezione di un utente fornisce le informazioni di accesso. Analizzare la cronologia di accesso degli utenti a rischio e reimpostare la password, se necessario.

Questa finestra di dialogo offre la possibilità di:

- Scaricare il report
- Cerca utenti

    ![Accessi a rischio](./media/concept-user-at-risk/16.png)

Per informazioni più dettagliate, è necessaria una licenza premium.

## <a name="users-at-risk-report-for-azure-ad-premium-editions"></a>Report sugli utenti a rischio per le edizioni Azure AD Premium

Il report Utenti contrassegnati per il rischio nelle edizioni Azure AD Premium fornisce:

- Un elenco di account utente che potrebbero essere stati compromessi 

- Informazioni aggregate sui [tipi di rilevamento dei rischi](concept-risk-events.md) rilevati

- un'opzione per scaricare il report

- avere un'opzione per configurare i [criteri per la correzione del rischio per gli utenti](../identity-protection/howto-user-risk-policy.md)  

![Accessi a rischio](./media/concept-user-at-risk/71.png)

Quando si seleziona un utente, si ottiene la visualizzazione di un report dettagliato per questo utente che consente di:

- aprire le visualizzazioni di tutti gli accessi

- Reimpostare la password dell'utente

- Elimina tutti gli eventi

- Esaminare i rilevamenti dei rischi segnalati per l'utente. 

![Accessi a rischio](./media/concept-user-at-risk/324.png)

Per esaminare un rilevamento dei rischi, selezionarne uno nell'elenco per aprire il pannello dei **Dettagli** per questo rilevamento dei rischi. Nel pannello dei **Dettagli** è possibile scegliere di chiudere manualmente un rilevamento dei rischi oppure riattivare un rilevamento dei rischi chiuso manualmente. 

![Accessi a rischio](./media/concept-user-at-risk/325.png)


## <a name="next-steps"></a>Passaggi successivi

- [Procedura: Configurare i criteri di rischio utente](../identity-protection/howto-user-risk-policy.md)
- [Procedura: Configurare i criteri di correzione del rischio utente](../identity-protection/howto-user-risk-policy.md)
- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

