---
title: Errore RequestDisallowedByPolicy con i criteri delle risorse di Azure | Microsoft Docs
description: Descrive la causa dell'errore RequestDisallowedByPolicy.
services: azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: e862637c688fd473b112fdfc0ee197da0444d02f
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71121240"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Errore RequestDisallowedByPolicy con i criteri delle risorse di Azure

Questo articolo descrive la causa dell'errore RequestDisallowedByPolicy e indica la relativa soluzione.

## <a name="symptom"></a>Sintomo

Durante la distribuzione, è possibile che venga visualizzato un errore **RequestDisallowedByPolicy** che impedisce la creazione della risorsa. Nell'esempio seguente viene descritto l'errore:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Risoluzione dei problemi

Per recuperare i dettagli sui criteri bloccati nella distribuzione, usare uno dei metodi seguenti:

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In PowerShell specificare questo identificatore di criterio come parametro `Id` per recuperare i dettagli relativi ai criteri che hanno bloccato la distribuzione.

```powershell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Nell'interfaccia della riga di comando di Azure specificare il nome della definizione del criterio:

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Soluzione

Per ragioni di sicurezza o conformità, è possibile che gli amministratori di sottoscrizione assegnino criteri che limitano la distribuzione di risorse. Ad esempio, è possibile che la sottoscrizione disponga di un criterio che impedisce la creazione di indirizzi IP pubblici, gruppi di sicurezza di rete, route definite dall'utente o tabelle di route. Il messaggio di errore nella sezione **Sintomo** mostra il nome del criterio.
Per risolvere questo problema, esaminare i criteri delle risorse e determinare come distribuire risorse conformi a tali criteri.

Per altre informazioni, vedere gli articoli seguenti:

- [Informazioni su Criteri di Azure](../governance/policy/overview.md)
- [Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)
