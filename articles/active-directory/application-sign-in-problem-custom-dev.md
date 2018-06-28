---
title: Problemi di accesso a un'applicazione personalizzata | Microsoft Docs
description: Errori comuni che potrebbero impedire l'accesso a un'applicazione sviluppata con Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 3cbc9f981ab528efe2d739022c674cef48dfeb51
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "36330887"
---
# <a name="problems-signing-in-to-an-custom-developed-application"></a>Problemi di accesso a un'applicazione personalizzata

Ci sono diversi errori che potrebbero impedire l'accesso a un'app. Il motivo più frequente che causa questo problema è l'errata configurazione delle app.

## <a name="errors-related-to--misconfigured-apps"></a>Errori correlati alle app configurate in modo errato

* Verificare che le configurazioni nel portale corrispondano a quelle nell'app. In particolare, confrontare ID applicazione/client, URL di risposta, chiavi/segreti client e URI ID app.

* Confrontare la risorsa a cui si richiede l'accesso nel codice con le autorizzazioni configurate nella scheda **Risorse necessarie** per assicurarsi di richiedere solo le risorse configurate.

* Vedere [Azure AD in StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory) per errori o problemi simili.

## <a name="next-steps"></a>Passaggi successivi

[Guida per gli sviluppatori di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Integrazione di applicazioni con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications>)<br>

[Consenso e concessione delle autorizzazioni per le app con convergenza di Azure Active Directory v2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD in Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory>)
