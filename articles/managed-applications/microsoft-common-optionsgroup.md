---
title: Elemento OptionsGroup dell'interfaccia utente di Azure | Microsoft Docs
description: Illustra l'elemento Microsoft.Common.OptionsGroup dell'interfaccia utente per il portale di Azure.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 9aee881844e9338cc1da2484a94c8355f2516c82
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720770"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Elemento Microsoft.Common.OptionsGroup dell'interfaccia utente
Controllo di selezione con una riga di opzioni disponibili.

## <a name="ui-sample"></a>Esempio di interfaccia utente
![Microsoft.Common.OptionsGroup](./media/managed-application-elements/microsoft.common.optionsgroup.png)

## <a name="schema"></a>SCHEMA
```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
  "defaultValue": "Value two",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ],
    "required": true
  },
  "visible": true
}
```

## <a name="remarks"></a>Note
- L'etichetta per `constraints.allowedValues` è il testo visualizzato per un elemento e il rispettivo valore è il valore di output dell'elemento in caso di selezione.
- Se specificato, il valore predefinito deve essere un'etichetta presente in `constraints.allowedValues`. Se non è specificato, viene selezionato il primo elemento in `constraints.allowedValues` per impostazione predefinita. Il valore predefinito è **null**.
- `constraints.allowedValues` deve contenere almeno un elemento.

## <a name="sample-output"></a>Output di esempio
```json
"two"
```

## <a name="next-steps"></a>Passaggi successivi
* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
