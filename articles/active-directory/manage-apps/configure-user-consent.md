---
title: Configurare il consenso utente in un'applicazione Azure Active Directory | Microsoft Docs
description: Informazioni su come gestire il modo in cui gli utenti forniscono il consenso alle autorizzazioni dell'applicazione. È possibile semplificare l'esperienza utente concedendo il consenso dell'amministratore. Questi metodi si applicano a tutti gli utenti finali nel tenant di Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4951984d05e75b0271cf6592c77c54ad13678994
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476549"
---
# <a name="configure-the-way-end-users-consent-to-an-application-in-azure-active-directory"></a>Configurare il consenso utente in un'applicazione Azure Active Directory
Informazioni su come configurare il modo in cui gli utenti forniscono il consenso alle autorizzazioni dell'applicazione. È possibile semplificare l'esperienza utente concedendo il consenso dell'amministratore. Questo articolo illustra i diversi modi con cui è possibile configurare il consenso dell'utente. Questi metodi si applicano a tutti gli utenti finali nel tenant di Azure Active Directory (Azure AD). 

Per altre informazioni sul consenso alle applicazioni, vedere [framework di consenso di Azure Active Directory](../develop/consent-framework.md).

## <a name="prerequisites"></a>Prerequisiti

Per ottenere il consenso dell'amministratore è necessario accedere come amministratore globale, amministratore di applicazioni o amministratore di applicazioni cloud.

Per limitare l'accesso alle applicazioni è necessario richiedere l'assegnazione dell'utente, quindi assegnare utenti o gruppi all'applicazione.  Per altre informazioni, vedere [Metodi per l'assegnazione di utenti e gruppi](methods-for-assigning-users-and-groups.md).

## <a name="grant-admin-consent-to-enterprise-apps-in-the-azure-portal"></a>Concedere il consenso dell'amministratore alle app aziendali nel portale di Azure

Per concedere il consenso dell'amministratore a un'app aziendale:

1. Accedere come amministratore globale, amministratore di applicazioni o amministratore di applicazioni cloud nel [portale di Azure](https://portal.azure.com).
2. Fare clic su **Tutti i servizi** nella parte superiore del menu di spostamento a sinistra. Aprire l'estensione **Azure Active Directory**.
3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.
4. Scegliere **Applicazioni aziendali** dal menu di spostamento.
5. Selezionare l'app per il consenso.
6. Selezionare **le autorizzazioni** e quindi fare clic su **concedere il consenso dell'amministratore**. Verrà richiesto l'accesso per amministrare l'applicazione.
7. Accedere con un account che disponga delle autorizzazioni per concedere il consenso dell'amministratore per l'applicazione. 
8. Fornire il consenso alle autorizzazioni dell'applicazione.

Questa opzione funziona solo se l'applicazione è: 

- Registrata nel tenant, o
- Registrata in un altro tenant di Azure AD e se è stato fornito il consenso da almeno un utente finale. Una volta che un utente finale ha fornito il consenso a un'applicazione, Azure AD elenca l'applicazione sotto **App aziendali** nel portale di Azure.

## <a name="grant-admin-consent-when-registering-an-app-in-the-azure-portal"></a>Concedere il consenso dell'amministratore quando si registra un'app nel portale di Azure

Per concedere il consenso dell'amministratore quando si registra un'app: 

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale.
2. Passare al pannello **Registrazioni per l'App**.
3. Selezionare l'applicazione per il consenso.
4. Selezionare **Autorizzazioni API**.
5. Fare clic su **concedere il consenso dell'amministratore**.


## <a name="grant-admin-consent-through-a-url-request"></a>Concedere il consenso dell'amministratore tramite una richiesta URL

Per concedere il consenso dell'amministratore tramite una richiesta URL:

1. Creare una richiesta in *login.microsoftonline.com* con le configurazioni dell'app e aggiungerla in `&prompt=admin_consent`. 
2. Dopo avere eseguito l'accesso con le credenziali di amministratore, all'app viene concesso il consenso per tutti gli utenti.


## <a name="force-user-consent-through-a-url-request"></a>Forzare il consenso utente tramite una richiesta URL

Per richiedere agli utenti finali di fornire il consenso a un'applicazione ogni volta che viene eseguita l'autenticazione, accodare `&prompt=consent` all'URL della richiesta per l'autenticazione.

## <a name="next-steps"></a>Passaggi successivi

[Consenso e integrazione di applicazioni con Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

[Consenso e concessione delle autorizzazioni per le app con convergenza di Azure Active Directory v2.0](../develop/active-directory-v2-scopes.md)

[Azure AD in Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
