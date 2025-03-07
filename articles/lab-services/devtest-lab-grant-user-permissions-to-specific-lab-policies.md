---
title: Concedere le autorizzazioni utente per specifici criteri di lab | Documentazione Microsoft
description: Informazioni su come concedere le autorizzazioni utente per criteri di lab specifici nei laboratori di sviluppo/test in base alle esigenze di ogni utente
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 5ca829f0-eb69-40a1-ae26-03a629db1d7e
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 70469a9e8737a9df18628951a061c97081c74080
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127379"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Concedere le autorizzazioni utente per specifici criteri di lab
## <a name="overview"></a>Panoramica
In questo articolo viene illustrato come usare PowerShell per concedere agli utenti autorizzazioni per un particolare criterio di lab. In questo modo, le autorizzazioni possono essere applicate in base alle esigenze di ciascun utente. Ad esempio, è possibile concedere a un determinato utente la possibilità di modificare le impostazioni dei criteri delle macchine virtuali, ma non i criteri dei costi.

## <a name="policies-as-resources"></a>Criteri come risorse
Come descritto nell'articolo [Controllo degli accessi in base al ruolo di Azure](../role-based-access-control/role-assignments-portal.md) , il controllo degli accessi in base al ruolo consente la gestione specifica degli accessi delle risorse per Azure. Usando il Controllo degli accessi in base al ruolo di Azure, è possibile separare compiti all'interno del team DevOps e concedere agli utenti solo la quantità di accesso di cui hanno bisogno per svolgere il proprio lavoro.

Nei lab di sviluppo/test un criterio è un tipo di risorsa che abilita l'azione del controllo degli accessi in base al ruolo **Microsoft.DevTestLab/labs/policySets/policies/** . Ogni criterio di lab è una risorsa del tipo di risorsa Criterio e può essere assegnato come ambito a un ruolo del controllo degli accessi in base al ruolo.

Ad esempio, per concedere le autorizzazioni di lettura/scrittura agli utenti per il **Allowed VM Sizes** dei criteri, è possibile creare un ruolo personalizzato che funziona con i **Microsoft.DevTestLab/labs/policySets/policies/** azione e quindi assegnare gli utenti al ruolo nell'ambito del **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Per altre informazioni sui ruoli personalizzati in RBAC, vedere il [controllo di accesso ai ruoli personalizzati](../role-based-access-control/custom-roles.md).

## <a name="creating-a-lab-custom-role-using-powershell"></a>Creazione di un ruolo personalizzato lab tramite PowerShell
Per iniziare, è necessario [installare Azure PowerShell](/powershell/azure/install-az-ps). 

Dopo aver configurato i cmdlet PowerShell di Azure, è possibile eseguire le attività seguenti:

* Elencare tutte le operazioni o azioni di un provider di risorse
* Elencare le azioni di un particolare ruolo:
* Creare un ruolo personalizzato

Lo script di PowerShell seguente offre alcuni esempi di esecuzione di queste attività:

    ‘List all the operations/actions for a resource provider.
    Get-AzProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzRoleDefinition -Role $policyRoleDef)

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Assegnazione agli utenti delle autorizzazioni per un criterio specifico tramite ruoli personalizzati
Dopo aver definito i ruoli personalizzati, è possibile assegnarli agli utenti. Per assegnare un ruolo personalizzato a un utente, è necessario prima ottenere il valore **ObjectId** che rappresenta l'utente. A tale scopo, usare il **Get-AzADUser** cmdlet.

Nell'esempio seguente il valore **ObjectId** dell'utente *SomeUser* è 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Dopo aver creato il **ObjectId** per l'utente e un nome di ruolo personalizzate, è possibile assegnare tale ruolo all'utente con il **New-AzRoleAssignment** cmdlet:

    PS C:\>New-AzRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

Nell'esempio precedente viene usato il criterio **AllowedVmSizesInLab** . È possibile usare uno dei seguenti criteri:

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo aver concesso le autorizzazioni utente per specifici criteri di lab, considerare i seguenti passaggi successivi:

* [Proteggere l'accesso a un lab](devtest-lab-add-devtest-user.md)
* [Impostare i criteri del lab](devtest-lab-set-lab-policy.md)
* [Creare un modello di lab](devtest-lab-create-template.md)
* [Creare elementi personalizzati per le VM](devtest-lab-artifact-author.md)
* [Aggiungere una VM a un lab](devtest-lab-add-vm.md)

