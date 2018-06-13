---
title: Aggiungere o modificare i ruoli di amministratore della sottoscrizione di Azure | Documentazione Microsoft
description: Descrive come aggiungere o modificare il co-amministratore di Azure, l’amministratore del servizio e l’amministratore dell’account
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 09c2badf6116dd36add6cccc82486d7f5b8f8697
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2018
ms.locfileid: "34069147"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Aggiungere o modificare gli amministratori delle sottoscrizioni di Azure

Gli amministratori delle sottoscrizioni classiche di Azure e [Controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) di Azure sono due sistemi per gestire l'accesso alle risorse di Azure:

* I ruoli di amministratori delle sottoscrizioni classiche offrono la gestione dell'accesso di base e includono Amministratore account, Amministratore del servizio e Coamministratore.
    * Quando ci si iscrive per una nuova sottoscrizione di Azure, per impostazione predefinita l'account viene impostato sia come amministratore account che come amministratore del servizio.
    * I coamministratori possono essere aggiunti dopo l'iscrizione.
* Controllo degli accessi in base al ruolo è un sistema più recente che offre la gestione degli accessi con granularità con molti ruoli predefiniti, flessibilità di ambito e ruoli personalizzati.
    * Tuttavia, gli utenti che hanno solo ruoli di Controllo degli accessi in base al ruolo e non ruoli di amministrazione per le sottoscrizioni classiche non possono gestire le distribuzioni di Azure classiche.

Per garantire un migliore controllo e per semplificare la gestione dell'accesso, è consigliabile usare Controllo degli accessi in base al ruolo per tutte le esigenze di gestione dell'accesso. Se possibile, è consigliabile riconfigurare i criteri di accesso esistenti con Controllo degli accessi in base al ruolo. 

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-admin-for-a-subscription-in-azure-portal"></a>Aggiungere un amministratore proprietario di Controllo degli accessi in base al ruolo per una sottoscrizione nel portale di Azure 

Per aggiungere un utente come amministratore per l'amministrazione del servizio di sottoscrizione di Azure, assegnare all'utente il ruolo di proprietario di Controllo degli accessi in base al ruolo per la sottoscrizione. Il ruolo di proprietario può gestire le risorse nella sottoscrizione assegnata e non ha privilegi di accesso per altre sottoscrizioni.

1. Aprire [**Sottoscrizioni** nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Selezionare la sottoscrizione a cui concedere l'accesso.
3. Selezionare **Aggiungi**  
   (Se manca il pulsante Aggiungi, non si è autorizzati ad aggiungere autorizzazioni.)
4. Selezionare **Controllo di accesso (IAM)** nel menu.
5. Nella casella **Ruolo** selezionare **Proprietario**. 
6. Nella casella **Assegna accesso a** selezionare **Applicazione, gruppo o utente di Azure AD**. 
7. Nella casella **Seleziona** digitare l'indirizzo di posta elettronica dell'utente che si vuole aggiungere come proprietario. Selezionare l'utente e quindi selezionare **Salva**.

    ![Schermata che mostra il pulsante Proprietario selezionato](./media/billing-add-change-azure-subscription-administrator/add-role.png)

Questo ruolo garantisce all'utente l'accesso completo a tutte le risorse, compreso il diritto di delegare l'accesso ad altri utenti. Per concedere l'accesso per un ambito diverso, ad esempio un gruppo di risorse, vedere il menu IAM per tale ambito. 

## <a name="add-or-change-co-administrator"></a>Aggiungere o modificare un coamministratore

Solo un proprietario può essere aggiunto come coamministratore. Gli altri utenti con ruoli come collaboratore e lettore non possono essere aggiunti come coamministratori.

> [!TIP]
> È solo necessario aggiungere l'account "Proprietario" come coamministratore se l'utente deve gestire distribuzioni di Azure classiche. È consigliabile usare Controllo degli accessi in base al ruolo per tutti gli altri scopi.

1. Se non è già stato fatto, aggiungere un utente come proprietario seguendo le istruzioni illustrate in precedenza.
2. **Fare clic con il pulsante destro del mouse** sull'utente proprietario appena aggiunto e quindi scegliere **Aggiungi come coamministratore**. Se l'opzione **Aggiungi come coamministratore** non è visibile, aggiornare la pagina o provare a usare un altro browser Internet. 

    ![Schermata per l'aggiunta di un coamministratore](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    Per rimuovere l'autorizzazione di coamministratore, **fare clic con il pulsante destro del mouse** sull'utente "Coamministratore" e quindi scegliere **Rimuovi coamministratore**.

    ![Schermata per la rimozione di un coamministratore](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>Modificare l'amministratore del servizio per una sottoscrizione di Azure

Solo l'amministratore account può modificare l'amministratore del servizio per una sottoscrizione. Per impostazione predefinita, al momento dell'iscrizione, l'amministratore del servizio corrisponde all'amministratore account. Se un altro utente viene impostato come amministratore del servizio, l'amministratore account perde l'accesso al portale di Azure. Tuttavia, l'amministratore account può sempre usare il Centro account per reimpostare se stesso come amministratore del servizio.

1. Assicurarsi che lo scenario sia supportato verificando le [limitazioni per la modifica degli amministratori del servizio](#limits).
1. Accedere al [Centro account](https://account.windowsazure.com/subscriptions) come amministratore account.
1. Selezionare una sottoscrizione.
1. Sul lato destro selezionare **Modifica i dettagli della sottoscrizione**.

    ![Screenshot che mostra il pulsante di modifica della sottoscrizione nel Centro account](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. Nel casella **AMMINISTRATORE DEL SERVIZIO** immettere l'indirizzo di posta elettronica del nuovo amministratore del servizio.

    ![Screenshot che mostra la casella per modificare l'indirizzo e-mail dell'amministratore del servizio](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>Limitazioni per la modifica degli amministratori del servizio

* Ogni sottoscrizione è associata a una directory di Azure AD. Per trovare la directory a cui è associata la sottoscrizione, passare a [**Sottoscrizioni**](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), quindi selezionare una sottoscrizione per visualizzare la directory.
* Se l'accesso è stato eseguito con un account aziendale o dell'istituto di istruzione, è possibile aggiungere altri account dell'organizzazione come amministratori del servizio. Ad esempio, abby@contoso.com può aggiungere bob@contoso.com come amministratore del servizio, ma non può aggiungere john@notcontoso.com a meno che john@notcontoso.com non si trovi nella directory contoso.com. Gli utenti che hanno eseguito l'accesso con un account aziendale o dell'istituto di istruzione possono continuare ad aggiungere utenti con account Microsoft come amministratori del servizio.

  | Metodo di accesso | Aggiunta di un utente con account Microsoft come amministratore del servizio? | Aggiunta di un account aziendale o dell'istituto di istruzione nella stessa organizzazione come amministratore del servizio? | Aggiunta di un account aziendale o dell'istituto di istruzione in un'organizzazione diversa come amministratore del servizio? |
  | --- | --- | --- | --- |
  |  Account Microsoft |Sì |No  |No  |
  |  Account aziendale o dell'istituto di istruzione |Sì |Sì |No  |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>Modificare l'amministratore account per una sottoscrizione di Azure

L'amministratore account è l'utente che inizialmente ha effettuato la sottoscrizione di Azure ed è il responsabile della fatturazione della sottoscrizione. Per modificare l'amministratore account di una sottoscrizione, vedere [Trasferimento della proprietà di una sottoscrizione di Azure a un altro account](billing-subscription-transfer.md).

<a name="check-the-account-administrator-of-the-subscription"></a>

**Non si è sicuri di chi sia l'amministratore account?** A tale scopo, seguire questa procedura:

1. Aprire [**Sottoscrizioni** nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Scegliere la sottoscrizione da controllare e quindi controllare la sezione **Impostazioni**.
1. Selezionare **Proprietà**. L'amministratore account della sottoscrizione viene visualizzato nella casella **Amministratore account**.  

## <a name="types-of-classic-subscription-admins"></a>Tipi di amministratore della sottoscrizione classica

 Amministratore account, Amministratore del servizio e Coamministratore sono i tre tipi di ruoli di amministratore per una sottoscrizione classica in Azure. L'account usato per iscriversi a Azure viene impostato automaticamente come amministratore account e amministratore del servizio. I coamministratori possono essere aggiunti in seguito. La tabella seguente descrive la differenza esatta tra questi tre ruoli amministrativi. 

> [!TIP]
> Per un migliore controllo e una gestione degli accessi granulare, è consigliabile usare Controllo degli accessi in base al ruolo di Azure, che consente di aggiungere gli utenti a più ruoli. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo di Azure Active Directory](../role-based-access-control/overview.md).

| Amministratore sottoscrizione classico | Limite | DESCRIZIONE |
| --- | --- | --- |
| Amministratore dell'account |1 per ogni account di Azure |Indica l'utente che si è iscritto per la sottoscrizione di Azure ed è autorizzato ad accedere al [Centro account](https://account.azure.com/Subscriptions) ed eseguire diverse attività di gestione. Queste includono la possibilità di creare nuove sottoscrizioni, annullare sottoscrizioni, modificare la fatturazione per una sottoscrizione e modificare l'amministratore del servizio. Concettualmente, l'amministratore account è il proprietario della fatturazione della sottoscrizione. In Controllo degli accessi in base al ruolo, all'amministratore account non è assegnato alcun ruolo.|
| Amministratore del servizio |1 per ogni sottoscrizione di Azure |Questo ruolo è autorizzato a gestire i servizi nel [portale di Azure](https://portal.azure.com). Per impostazione predefinita, per una nuova sottoscrizione l'amministratore account è anche amministratore del servizio. In Controllo degli accessi in base al ruolo, il ruolo Proprietario viene assegnato all'amministratore del servizio per l'ambito della sottoscrizione.|
| Coamministratore |200 per ogni sottoscrizione |Questo ruolo ha gli stessi privilegi di accesso dell'amministratore del servizio, ma non può modificare l'associazione di sottoscrizioni alle directory di Azure. In Controllo degli accessi in base al ruolo, il ruolo Proprietario viene assegnato al coamministratore per l'ambito della sottoscrizione.|

## <a name="learn-more-about-resource-access-control-and-active-directory"></a>Altre informazioni sul controllo dell'accesso alle risorse e Active Directory

* Per altre informazioni sul controllo dell'accesso alle risorse in Microsoft Azure, vedere [Informazioni sull'accesso alle risorse in Azure](../role-based-access-control/rbac-and-directory-admin-roles.md).
* Per altre informazioni sul modo in cui Azure Active Directory, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md) e [Assegnazione dei ruoli di amministratore in Azure Active Directory](../active-directory/active-directory-assign-admin-roles-azure-portal.md).

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.

Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
