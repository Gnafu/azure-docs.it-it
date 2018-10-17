---
title: Report di sicurezza Utenti contrassegnati per il rischio nel portale di Azure Active Directory | Microsoft Docs
description: Informazioni sul report di sicurezza Utenti contrassegnati per il rischio nel portale di Azure Active Directory
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/14/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: fde063cb593ca1f610dc35cd044fe41e34ab9202
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2018
ms.locfileid: "45578367"
---
# <a name="users-flagged-for-risk-security-report-in-the-azure-active-directory-portal"></a>Report di sicurezza Utenti contrassegnati per il rischio nel portale di Azure Active Directory

I report di sicurezza in Azure Active Directory (Azure AD) permettono di ottenere informazioni approfondite sulla probabile presenza di account utente compromessi nell'ambiente. 

Azure Active Directory rileva le azioni sospette correlate agli account utente. Per ogni azione rilevati viene creato un record denominato *evento di rischio*. Per altre informazioni, vedere [Eventi di rischio di Azure Active Directory](concept-risk-events.md). 

Gli eventi di rischio rilevati vengono usati per calcolare:

- **Accessi a rischio**. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente. Per altre informazioni, vedere [Come configurare i criteri di rischio di accesso](../identity-protection/howto-sign-in-risk-policy.md). 

- **Utenti contrassegnati per il rischio**. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso. Per altre informazioni, vedere [Come configurare i criteri di rischio utente](../identity-protection/howto-user-risk-policy.md).  

Nel Portale di Azure, è possibile trovare i report di sicurezza nel pannello di **Azure Active Directory** nella sezione **Sicurezza**.  

![Accessi a rischio](./media/concept-user-at-risk/10.png)



## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Licenza di Azure AD necessaria per accedere a un report sulla sicurezza  

Tutte le edizioni di Azure Active Directory offrono report sugli utenti contrassegnati per il rischio.  
Tuttavia, il livello di granularità dei report varia a seconda delle edizioni: 

- Nelle edizioni **Azure Active Directory Free e Basic**  è già incluso un elenco degli utenti contrassegnati per il rischio. 

- Nell'edizione **Azure Active Directory Premium 1** questo modello consente anche di esaminare alcuni degli eventi di rischio sottostanti che sono stati rilevati per ogni report. 

- L'edizione **Azure Active Directory Premium 2** offre informazioni più dettagliate su tutti gli eventi di rischio sottostanti e permette di configurare criteri di sicurezza che rispondono automaticamente a livelli di rischio configurati.



## <a name="azure-active-directory-free-and-basic-edition"></a>Versione gratuita e di base di Azure Active Directory

Il report Utenti contrassegnati per il rischio nella versione gratuita e di base di Azure Active Directory offre un elenco degli account utente che potrebbero essere stati compromessi. 


![Accessi a rischio](./media/concept-user-at-risk/03.png)

Selezionando un utente verrà aperto il pannello dei dati utente correlati.
Analizzare la cronologia di accesso degli utenti a rischio e reimpostare la password, se necessario.

![Accessi a rischio](./media/concept-user-at-risk/46.png)


Questa finestra di dialogo offre la possibilità di:

- Scaricare il report

- Cercare gli utenti

![Accessi a rischio](./media/concept-user-at-risk/16.png)


## <a name="azure-active-directory-premium-editions"></a>Versione Premium di Azure Active Directory

Il report Utenti contrassegnati per il rischio della versione Premium di Azure Active Directory offre:

- Un elenco di account utente che potrebbero essere stati compromessi 

- informazioni aggregate sui [tipi di evento di rischio](concept-risk-events.md) che sono stati rilevati

- Un'opzione per scaricare il report

- avere un'opzione per configurare i [criteri per la correzione del rischio per gli utenti](../identity-protection/howto-user-risk-policy.md)  


![Accessi a rischio](./media/concept-user-at-risk/71.png)

Quando si seleziona un utente, si ottiene la visualizzazione di un report dettagliato per questo utente che consente di:

- aprire le visualizzazioni di tutti gli accessi

- Reimpostare la password dell'utente

- eliminare tutti gli eventi

- ricercare la causa degli eventi a rischio segnalati per l'utente. 


![Accessi a rischio](./media/concept-user-at-risk/324.png)


Per analizzare un evento di rischio, selezionarne uno nell'elenco per aprire il pannello **Dettagli** dell'evento di rischio. Nel pannello **Dettagli**, è possibile chiudere manualmente un evento di rischio o riattivare un evento di rischio chiuso manualmente. 


![Accessi a rischio](./media/concept-user-at-risk/325.png)



## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni in merito, vedere [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

