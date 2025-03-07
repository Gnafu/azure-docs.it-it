---
title: Gestire gli amministratori del server in Azure Analysis Services | Microsoft Docs
description: Informazioni su come gestire gli amministratori del server per un server Analysis Services in Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: eb5248d374fa6212398ad652b4db1496833473ae
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68696302"
---
# <a name="manage-server-administrators"></a>Gestire gli amministratori del server

Gli amministratori del server devono essere un utente o un gruppo di sicurezza valido in Azure Active Directory (Azure AD) per il tenant in cui si trova il server. È possibile usare **Amministratori di Analysis Services** per il server nel portale di Azure, in Proprietà server in SSMS, PowerShell o API REST per gestire gli amministratori del server. 

I **gruppi di sicurezza** devono essere abilitati per la [posta elettronica](https://docs.microsoft.com/exchange/recipients-in-exchange-online/manage-mail-enabled-security-groups) con la `MailEnabled` proprietà impostata su. `True` Quando si specifica un gruppo in base all' `obj:groupid@tenantid`indirizzo di posta elettronica, usare.

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Per aggiungere amministratori del server usando il portale di Azure

1. Nel portale, per il server fare clic su **Amministratori di Analysis Services**.
2. Nel riquadro **\<nomeserver> - Amministratori di Analysis Services** fare clic su **Aggiungi**.
3. In **Aggiungi amministratori del server** selezionare gli account utente da Azure AD o invitare gli utenti esterni in base all'indirizzo di posta elettronica.

    ![Amministratori del server nel portale di Azure](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Per aggiungere amministratori del server usando SSMS

1. Fare clic con il pulsante destro del mouse sul server > **Proprietà**.
2. In **Proprietà computer Analysis Server** fare clic su **Sicurezza**.
3. Fare clic su **Aggiungi** e quindi immettere l'indirizzo di posta elettronica per un utente o un gruppo in Azure AD.
   
    ![Aggiungere gli amministratori del server in SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Usare il cmdlet [New-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/new-azanalysisservicesserver) per specificare il parametro Administrator durante la creazione di un nuovo server. <br>
Usare il cmdlet [set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver) per modificare il parametro Administrator per un server esistente.

## <a name="rest-api"></a>API REST

Usare [Crea](https://docs.microsoft.com/rest/api/analysisservices/servers/create) per specificare la proprietà asAdministrator quando si crea un nuovo server. <br>
Usare [Aggiorna](https://docs.microsoft.com/rest/api/analysisservices/servers/update) per specificare la proprietà asAdministrator quando si modifica un server esistente. <br>



## <a name="next-steps"></a>Passaggi successivi 

[Autenticazione e autorizzazioni utente](analysis-services-manage-users.md)  
[Gestire ruoli e utenti del database](analysis-services-database-users.md)  
[Controllo degli accessi in base al ruolo](../role-based-access-control/overview.md)  

