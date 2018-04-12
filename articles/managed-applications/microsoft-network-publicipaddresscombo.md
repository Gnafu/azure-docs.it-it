---
title: Elemento PublicIpAddressCombo dell'interfaccia utente di Azure | Microsoft Docs
description: Illustra l'elemento Microsoft.Network.PublicIpAddressCombo dell'interfaccia utente per il portale di Azure.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: bced85abaf24beb0997ec906a68e5947f0e67cca
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Elemento Microsoft.Network.PublicIpAddressCombo dell'interfaccia utente
Gruppo di controlli per la selezione di un indirizzo IP pubblico nuovo o esistente.

## <a name="ui-sample"></a>Esempio di interfaccia utente
![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- Se l'utente seleziona "Nessuno" come indirizzo IP pubblico, la casella di testo dell'etichetta del nome di dominio viene nascosta.
- Se l'utente seleziona un indirizzo IP pubblico esistente, la casella di testo dell'etichetta del nome di dominio viene disabilitata e il valore corrisponderà all'etichetta del nome di dominio dell'indirizzo IP selezionato.
- Il suffisso del nome di dominio (ad esempio, westus.cloudapp.azure.com) viene aggiornato automaticamente in base alla posizione selezionata.

## <a name="schema"></a>SCHEMA
```json
{
  "name": "element1",
  "type": "Microsoft.Network.PublicIpAddressCombo",
  "label": {
    "publicIpAddress": "Public IP address",
    "domainNameLabel": "Domain name label"
  },
  "toolTip": {
    "publicIpAddress": "",
    "domainNameLabel": ""
  },
  "defaultValue": {
    "publicIpAddressName": "ip01",
    "domainNameLabel": "mydomain"
  },
  "constraints": {
    "required": {
      "domainNameLabel": true
    }
  },
  "options": {
    "hideNone": false,
    "hideDomainNameLabel": false,
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Osservazioni
- Se `constraints.required.domainNameLabel` è impostato su **true**, l'utente deve specificare un'etichetta di nome di dominio quando crea un nuovo indirizzo IP pubblico. Gli indirizzi IP pubblici esistenti senza etichetta non sono disponibili per la selezione.
- Se `options.hideNone` è impostato su **true**, l'opzione per selezionare **Nessuno** per l'indirizzo IP pubblico viene nascosta. Il valore predefinito è **false**.
- Se `options.hideDomainNameLabel` è impostato su **true**, la casella di testo per l'etichetta del nome di dominio viene nascosta. Il valore predefinito è **false**.
- Se `options.hideExisting` è true, l'utente non può scegliere un indirizzo IP pubblico esistente. Il valore predefinito è **false**.

## <a name="sample-output"></a>Output di esempio
Se l'utente non seleziona alcun indirizzo IP pubblico, è previsto l'output seguente:
```json
{
  "newOrExistingOrNone": "none"
}
```

Se l'utente seleziona un indirizzo IP pubblico nuovo o esistente, è previsto l'output seguente:
```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "mydomain",
  "publicIPAllocationMethod": "Dynamic",
  "newOrExistingOrNone": "new"
}
```
- Quando `options.hideNone` è specificato come **true**, `newOrExistingOrNone` può avere solo il valore **new** o **existing**.
- Quando `options.hideDomainNameLabel` è specificato come **true**, `domainNameLabel` non viene dichiarato.

## <a name="next-steps"></a>Passaggi successivi
* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
