---
title: Elemento Section dell'interfaccia utente di Azure | Microsoft Docs
description: Illustra l'elemento Microsoft.Common.Section dell'interfaccia utente per il portale di Azure.
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
ms.openlocfilehash: c89b45dd4d8e6c2964f3d2bcbb6c3cef445c79e6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64698901"
---
# <a name="microsoftcommonsection-ui-element"></a>Elemento Microsoft.Common.Section dell'interfaccia utente
Controllo che raggruppa uno o più elementi sotto un'intestazione.

## <a name="ui-sample"></a>Esempio di interfaccia utente
![Microsoft.Common.Section](./media/managed-application-elements/microsoft.common.section.png)

## <a name="schema"></a>SCHEMA
```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Example section",
  "elements": [
    {
      "name": "text1",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 1"
    },
    {
      "name": "text2",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>Note
- `elements` deve avere almeno un elemento e può avere tutti i tipi di elemento, ad eccezione di `Microsoft.Common.Section`.
- Questo elemento non supporta la proprietà `toolTip`.

## <a name="sample-output"></a>Output di esempio
Per accedere ai valori di output degli elementi in `elements`, usare le funzioni [basics()](create-uidefinition-functions.md#basics) o [steps()](create-uidefinition-functions.md#steps) e la notazione punto:

```json
steps('configuration').section1.text1
```

Gli elementi di tipo `Microsoft.Common.Section` stessi non hanno valori di output.

## <a name="next-steps"></a>Passaggi successivi
* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
