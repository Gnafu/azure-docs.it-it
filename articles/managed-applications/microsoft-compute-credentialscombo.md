---
title: Elemento CredentialsCombo dell'interfaccia utente di Azure | Microsoft Docs
description: Illustra l'elemento Microsoft.Compute.CredentialsCombo dell'interfaccia utente per il portale di Azure.
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
ms.date: 09/29/2018
ms.author: tomfitz
ms.openlocfilehash: 3036e5882e236dbbb9cf4f9aae17617822422a82
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742098"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Elemento Microsoft.Compute.CredentialsCombo dell'interfaccia utente
Si tratta di un gruppo di controlli con convalida predefinita per le chiavi pubbliche SSH e le password di Windows e Linux.

## <a name="ui-sample"></a>Esempio di interfaccia utente

Per Windows, gli utenti visualizzeranno:

![Microsoft.Compute.CredentialsCombo Windows](./media/managed-application-elements/microsoft.compute.credentialscombo-windows.png)

Per Linux con la password selezionata, gli utenti visualizzeranno:

![Password Microsoft.Compute.CredentialsCombo Linux](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-password.png)

Per Linux con chiave pubblica SSH selezionata, gli utenti visualizzeranno:

![Chiave Microsoft.Compute.CredentialsCombo Linux](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-key.png)

## <a name="schema"></a>Schema
Per Windows, usare il seguente schema:

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": {
    "password": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
    "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false
  },
  "osPlatform": "Windows",
  "visible": true
}
```

Per **Linux**, usare il seguente schema:

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "authenticationType": "Authentication type",
    "password": "Password",
    "confirmPassword": "Confirm password",
    "sshPublicKey": "SSH public key"
  },
  "toolTip": {
    "authenticationType": "",
    "password": "",
    "sshPublicKey": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
    "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false,
    "hidePassword": false
  },
  "osPlatform": "Linux",
  "visible": true
}
```

## <a name="remarks"></a>Note
- È necessario specificare `osPlatform`, che può essere **Windows** o **Linux**.
- Se `constraints.required` è impostato su **true**, perché la convalida abbia esito positivo le caselle di testo della password o della chiave pubblica SSH devono disporre di valori. Il valore predefinito è **true**.
- Se la proprietà `options.hideConfirmation` è impostata su **true**, la seconda casella di testo per la conferma della password dell'utente è nascosta. Il valore predefinito è **false**.
- Se `options.hidePassword` è impostato su **true**, l'opzione per l'uso dell'autenticazione della password è nascosta. È possibile usarla solo quando `osPlatform` è **Linux**. Il valore predefinito è **false**.
- La proprietà `customPasswordRegex` permette di implementare vincoli aggiuntivi sulle password consentite. La stringa in `customValidationMessage` viene visualizzata quando una password non supera la convalida personalizzata. Il valore predefinito per entrambe le proprietà è **null**.

## <a name="sample-output"></a>Esempio di output
Se `osPlatform` è **Windows** oppure `osPlatform` è **Linux** e l'utente ha specificato una password anziché una chiave pubblica SSH, il controllo restituisce l'output seguente:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rddem0",
}
```

Se `osPlatform` è **Linux** e l'utente ha specificato una chiave pubblica SSH, il controllo restituisce l'output seguente:

```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="next-steps"></a>Passaggi successivi
* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
