---
title: Disabilitare la verifica tramite posta elettronica durante l'iscrizione dell'utente in Azure Active Directory B2C | Microsoft Docs
description: Questo argomento illustra come disabilitare la verifica tramite posta elettronica durante l'iscrizione dell'utente in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d827843bdbaff1ad95dce3318f39867abe8b2d0f
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065631"
---
# <a name="disable-email-verification-during-consumer-sign-up-in-azure-active-directory-b2c"></a>Disabilitare la verifica tramite posta elettronica durante l'iscrizione dell'utente in Azure Active Directory B2C
Se abilitata, Azure Active Directory B2C (Azure AD B2C) consente a un consumer di iscriversi alle applicazioni fornendo un indirizzo di posta elettronica e creando un account locale. Azure AD B2C garantisce la validità degli indirizzi di posta elettronica richiedendone la verifica agli utenti durante il processo di iscrizione. Impedisce anche a processi dannosi automatizzati di generare account fittizi per le applicazioni.

Alcuni sviluppatori di applicazioni preferiscono ignorare la verifica tramite posta elettronica durante il processo di iscrizione, chiedendo agli utenti di verificare l'indirizzo di posta elettronica in un secondo momento. A tale scopo, è possibile configurare Azure AD B2C in modo da disabilitare la verifica tramite posta elettronica. In questo modo si ottiene un processo di iscrizione più semplice e gli sviluppatori hanno la possibilità di distinguere gli utenti che hanno verificato l'indirizzo di posta elettronica da quelli che non lo hanno fatto.

Per impostazione predefinita, nei flussi utente di iscrizione la verifica tramite posta elettronica è abilitata. Per disabilitarla, seguire questa procedura:

1. Fare clic su **Flussi utente**.
2. Fare clic sul flusso utente, ad esempio "B2C_1_SiUp", per aprirlo.
3. Fare clic su **Layout di pagina**.
4. Fare clic su **Pagina di iscrizione dell'account locale**.
5. Fare clic su **Indirizzo di posta elettronica** nella colonna **Nome** sotto la sezione **Attributi utente**.
6. In **Richiede la verifica** selezionare **No**.
7. Fare clic su **Salva** nella parte superiore del pannello. L'operazione è completata.

> [!NOTE]
> Disabilitando la verifica tramite posta elettronica nel processo di iscrizione si potrebbe ricevere posta indesiderata. Se si disabilita il sistema di verifica predefinito, è consigliabile aggiungerne uno personalizzato.
>
>
