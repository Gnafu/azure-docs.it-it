---
title: Panoramica del protocollo .NET per Azure AD | Documentazione Microsoft
description: Come usare messaggi HTTP per autorizzare l'accesso ad applicazioni Web e API Web nel proprio tenant con Azure Active Directory.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/18/2018
ms.author: priyamo
ms.openlocfilehash: 72fb594265e69eb1dc16cb29ad4df6acb3a87720
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34663628"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Registrare l'applicazione nel tenant di Active Directory
Prima di tutto è necessario registrare l'applicazione nel tenant di Azure Active Directory (Azure AD). Verrà fornito un ID per l'applicazione, abilitandola per ricevere token.

* Accedere al [portale di Azure](https://portal.azure.com).
* Scegliere il tenant di Azure AD facendo clic sull'account nell'angolo superiore destro della pagina.
* Nel riquadro di spostamento a sinistra fare clic su **Azure Active Directory**.
* Fare clic su **Registrazioni per l'app** e scegliere **Registrazione nuova applicazione**.
* Seguire le istruzioni e creare una nuova applicazione. Per questa esercitazione non è rilevante che si tratti di un'applicazione Web o un'applicazione nativa, ma per esempi specifici per le applicazioni Web o native, consultare le [guide introduttive](../articles/active-directory/develop/active-directory-developers-guide.md).
  * Per le applicazioni Web fornire l'**URL accesso**, ovvero l'URL di base dell'app con cui gli utenti possono accedere, ad esempio `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * Per le applicazioni native fornire un **URI di reindirizzamento**, che Azure AD userà per restituire le risposte dei token. Immettere un valore specifico per l'applicazione in uso, ad esempio `http://MyFirstAADApp`
* Dopo avere completato la registrazione, Azure AD assegnerà all'applicazione un identificatore client univoco, l'**ID applicazione**. Poiché questo valore sarà necessario nelle sezioni successive, copiarlo dalla pagina dell'applicazione.
* Per individuare l'applicazione nel portale di Azure, fare clic su **Registrazioni per l'app**, quindi fare clic su **Visualizza tutte le applicazioni**.