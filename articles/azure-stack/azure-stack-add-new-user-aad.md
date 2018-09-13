---
title: Aggiungere un nuovo account tenant di Azure Stack in Azure Active Directory | Microsoft Docs
description: Dopo aver distribuito Microsoft Azure Stack Development Kit, è necessario creare almeno un account utente tenant in modo da poter esplorare il portale tenant.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: c312658750c1e9ef024a837ccc16e5cd5be8a5ef
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2018
ms.locfileid: "35998501"
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Aggiungere un nuovo account tenant di Azure Stack in Azure Active Directory
Dopo aver [distribuzione di Azure Stack Development Kit](azure-stack-run-powershell-script.md), è necessario un account utente di tenant in modo da poter esplorare il portale tenant e testare i piani e offerte. È possibile creare un account tenant dal [usando il portale di Azure](#create-an-azure-stack-tenant-account-using-the-azure-portal) o tramite [usando PowerShell](#create-an-azure-stack-tenant-account-using-powershell).

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Creare un account tenant di Azure Stack usando il portale di Azure
Per usare il portale di Azure, è necessario disporre di una sottoscrizione di Azure.

1. Accedere al [Azure](https://portal.azure.com).
2. Nella barra di spostamento sinistra di Microsoft Azure fare clic su **Active Directory**.
3. Nell'elenco di directory fare clic sulla directory che si vuole usare per Azure Stack o crearne una nuova.
4. In questa pagina della directory fare clic su **Utenti**.
5. Fare clic su **Add User**.
6. Nel **Add user** procedura guidata il **tipo di utente** scegliere **nuovo utente nell'organizzazione**.
7. Nella casella **Nome utente** digitare un nome per l'utente.
8. Nella procedura guidata per l' **@** scegliere la voce appropriata.
9. Fare clic sulla freccia avanti.
10. Nel **profilo utente** pagina della procedura guidata, digitare un **First name**, **Last name**, e **nome visualizzato**.
11. Nel **ruolo** casella di riepilogo **utente**.
12. Fare clic sulla freccia avanti.
13. Nel **Ottieni password temporanea** pagina, fare clic su **crea**.
14. Copiare la **nuova password**.
15. Accedi a Microsoft Azure con il nuovo account. Modificare la password quando viene richiesto.
16. Accedi a `https://portal.local.azurestack.external` con il nuovo account per visualizzare il portale tenant.

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>Creare un account tenant di Azure Stack usando PowerShell
Se non hai una sottoscrizione di Azure, non è possibile usare il portale di Azure per aggiungere un account utente di tenant. In questo caso, è possibile usare invece il modulo Azure Active Directory per Windows PowerShell.

> [!NOTE]
> Se si usa l'Account Microsoft (Live ID) per distribuire Azure Stack Development Kit, è possibile utilizzare AAD PowerShell per creare account tenant. 
> 
> 

1. Installare il [Assistente per l'accesso per i professionisti IT RTW ai Microsoft Online Services](https://www.microsoft.com/en-us/download/details.aspx?id=41950).
2. Installare il [modulo di Azure Active Directory per Windows PowerShell (versione a 64 bit)](http://go.microsoft.com/fwlink/p/?linkid=236297) e aprirlo.
3. Eseguire i cmdlet seguenti:

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Accedi a Microsoft Azure con il nuovo account. Modificare la password quando viene richiesto.
2. Accedi a `https://portal.local.azurestack.external` con il nuovo account per visualizzare il portale tenant.

