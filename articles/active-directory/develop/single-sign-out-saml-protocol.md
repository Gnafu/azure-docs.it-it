---
title: Protocollo SAML per Single Sign-Out di Azure | Documentazione Microsoft
description: Questo articolo illustra il protocollo SAML per Single Sign-Out in Azure Active Directory
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 0e4aa75d-d1ad-4bde-a94c-d8a41fb0abe6
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/19/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66c509b1b901889241d6837611a2c373750fdb3a
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834785"
---
# <a name="single-sign-out-saml-protocol"></a>Protocollo SAML per Single Sign-Out

Azure Active Directory (Azure AD) supporta il profilo di Single Sign-Out per Web browser SAML 2.0. Perché la disconnessione Single Sign-Out funzioni correttamente il **LogoutURL** per l'applicazione deve essere registrato in modo esplicito con Azure AD durante la registrazione dell'applicazione. Azure AD usa LogoutURL per reindirizzare gli utenti dopo che si sono disconnessi.

Il diagramma seguente descrive il flusso di lavoro del processo Single Sign-Out di Azure AD.

![Flusso di lavoro di Single Sign-Out di Azure AD](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
Il servizio cloud invia un messaggio `LogoutRequest` ad Azure AD per indicare che è stata terminata una sessione. L'estratto seguente contiene un esempio di elemento `LogoutRequest` .

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
L'elemento `LogoutRequest` inviato ad Azure AD richiede gli attributi seguenti:

* `ID`: identifica la richiesta di disconnessione. Il valore di `ID` non deve iniziare con un numero. La procedura tipica consiste nell'aggiungere **id** alla rappresentazione di stringa di un GUID.
* `Version`: impostare il valore di questo elemento su **2.0**. Questo valore è obbligatorio.
* `IssueInstant`: è una stringa `DateTime` con un valore di UTC (Coordinate Universal Time) e il [formato round trip ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD prevede un valore di questo tipo, ma non lo impone.

### <a name="issuer"></a>Rilasciato da
L'elemento `Issuer` in una `LogoutRequest` deve corrispondere esattamente a uno dei **ServicePrincipalNames** nel servizio cloud in Azure AD. Viene in genere impostato sull' **URI ID app** specificato durante la registrazione dell'applicazione.

### <a name="nameid"></a>NameID
Il valore dell'elemento `NameID` deve corrispondere esattamente al `NameID` dell'utente che viene disconnesso.

## <a name="logoutresponse"></a>LogoutResponse
Azure AD invia un elemento `LogoutResponse` in risposta a un elemento `LogoutRequest`. L'estratto seguente contiene un esempio di elemento `LogoutResponse`.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Azure AD imposta i valori `ID`, `Version` e `IssueInstant` nell'elemento `LogoutResponse`. Imposta anche l'elemento `InResponseTo` sul valore dell'attributo `ID` dell'elemento `LogoutRequest` che ha provocato la risposta.

### <a name="issuer"></a>Rilasciato da
Azure ad imposta questo valore su `https://login.microsoftonline.com/<TenantIdGUID>/` dove \<TenantIdGUID > è l'ID tenant del tenant di Azure ad.

Per valutare il valore dell'elemento `Issuer` usare il valore dell' **URI ID app** specificato durante la registrazione dell'applicazione.

### <a name="status"></a>Stato
Azure AD usa l'elemento `StatusCode` nell'elemento `Status` per indicare l'esito positivo o negativo della disconnessione. Quando il tentativo di disconnessione non riesce, l'elemento `StatusCode` può anche contenere messaggi di errore personalizzati.
