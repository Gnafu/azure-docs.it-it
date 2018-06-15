---
title: Come configurare gli avvisi di sicurezza | Documentazione Microsoft
description: Informazioni su come configurare gli avvisi di sicurezza per l'estensione Azure Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: protection
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 9b1779ed409a01356615561b1eb6fea0235529f4
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233921"
---
# <a name="how-to-configure-security-alerts-in-azure-ad-privileged-identity-management"></a>Come configurare gli avvisi di sicurezza in Azure AD Privileged Identity Management
## <a name="security-alerts"></a>Avvisi di sicurezza
Azure Privileged Identity Management (PIM) genera avvisi nel caso di attività sospette o non sicure nel proprio ambiente. Una avviso attivato viene visualizzato nel dashboard di PIM. Selezionare l'avviso per visualizzare un report che elenca gli utenti o i ruoli che hanno attivato l'avviso.

![Schermata Avvisi di sicurezza del dashboard di PIM][1]

| Avviso | Gravità | Trigger | Raccomandazione |
| --- | --- | --- | --- |
| **I ruoli vengono assegnati all'esterno di PIM** |Alto |Un utente è stato assegnato a un ruolo privilegiato in modo permanente, all'esterno dell'interfaccia PIM. |Rivedere gli utenti nell'elenco e annullare l'assegnazione a ruoli privilegiati all'esterno dell'interfaccia PIM. |
| **I ruoli vengono attivati con una frequenza eccessiva** |Media |È stato eseguito un numero eccessivo di riattivazioni dello stesso ruolo nel tempo consentito nelle impostazioni. |Contattare l'utente per verificare il motivo per cui ha attivato il ruolo con una frequenza elevata. Il limite temporale potrebbe essere troppo breve per completare le attività oppure è possibile che l'utente usi script per attivare automaticamente un ruolo. Verificare che la durata di attivazione del ruolo sia sufficiente per eseguire le attività. |
| **I ruoli non richiedono l'autenticazione MFA per l'attivazione** |Media |Sono presenti ruoli con impostazioni in cui l'autenticazione MFA non è abilitata. |È richiesta l'autenticazione MFA per i ruoli con i privilegi più elevati, ma è consigliabile abilitare l'autenticazione MFA per l'attivazione di tutti i ruoli. |
| **Gli utenti non usano i ruoli con privilegi** |Basso |Sono presenti amministratori idonei che non hanno attivato i ruoli di recente. |Avviare una verifica di accesso per determinare gli utenti che non necessitano più dell'accesso. |
| **Il numero di amministratori globali presenti è eccessivo** |Basso |Sono presenti più amministratori globali di quanti consigliati. |Se il numero di amministratori globali è elevato, è probabile che ottengano più autorizzazioni di quelle necessarie. Trasferire gli utenti a ruoli con privilegi meno elevati o rendere alcuni utenti idonei per il ruolo anziché assegnare i privilegi in modo permanente. |

### <a name="severity"></a>Gravità
* **Elevata**: richiede un'azione immediata a causa di una violazione dei criteri. 
* **Media**: non richiede un'azione immediata ma segnala una potenziale violazione dei criteri.
* **Bassa**: non richiede un'azione immediata ma suggerisce una modifica dei criteri.

## <a name="configure-security-alert-settings"></a>Configurare le impostazioni degli avvisi di sicurezza
È possibile personalizzare alcuni avvisi di sicurezza in PIM in linea con gli obiettivi di protezione e l'ambiente. Seguire questa procedura per accedere al pannello impostazioni:

1. Accedere al [portale di Azure](https://portal.azure.com/) e selezionare **Azure AD Privileged Identity Management** nel dashboard.
2. Selezionare **Managed privileged roles (Ruoli con privilegi gestiti)** > **Impostazioni** > **Impostazioni degli avvisi**.
   
    ![Passare a impostazioni degli avvisi di sicurezza][2]

### <a name="roles-are-being-activated-too-frequently-alert"></a>Avviso "I ruoli vengono attivati con una frequenza eccessiva"
Questo avviso viene attivato se un utente attiva lo stesso ruolo con privilegi più volte entro l'intervallo temporale specificato. È possibile configurare il periodo di tempo e il numero di attivazioni.

* **Intervallo di tempo per il rinnovo delle attivazioni**: specificare in giorni, ore, minuti e secondi il periodo per cui si vuole rilevare rinnovi sospetti.
* **Numero di rinnovi di attivazioni**: specificare il numero di attivazioni, da 2 a 100, considerati idonei per un avviso, entro l'intervallo di tempo specificato. È possibile modificare questa impostazione spostando il dispositivo di scorrimento o digitando un numero nella casella di testo.

### <a name="there-are-too-many-global-administrators-alert"></a>Avviso "Il numero di amministratori globali è eccessivo"
Se vengono soddisfatti due criteri diversi ed è possibile configurarli entrambi, PIM attiva l'avviso. È necessario prima raggiungere una determinata soglia di amministratori globali. Una determinata percentuale del totale di assegnazioni di ruoli deve essere rappresentata da amministratori globali. Se viene soddisfatto uno solo di questi criteri, l'avviso non verrà visualizzato.  

* **Numero minimo di amministratori globali**: specificare il numero di amministratori globali, da 2 a 100, che si considera un valore non sicuro.
* **Percentuale di amministratori globali**: specificare la percentuale di amministratori che sono amministratori globali, da 0% a 100%, che si considera non sicura nell'ambiente.

### <a name="administrators-arent-using-their-privileged-roles-alert"></a>Avviso "Gli amministratori non usano i ruoli con privilegi"
Questo avviso viene attivato se un utente non attiva un ruolo dopo un determinato periodo.

* **Numero di giorni**: specificare il numero di giorni, da 0 a 100, per cui un utente può non attivare un ruolo.

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_dash.png
[2]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_settings.png
