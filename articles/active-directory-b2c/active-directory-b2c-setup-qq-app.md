---
title: Configurazione di QQ in Azure Active Directory B2C | Microsoft Docs
description: Fornire l'iscrizione e l'accesso agli utenti con account QQ nelle applicazioni protette da Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1f9a0f56158f08dd3b22078f111c9ec6911b726c
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444430"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-qq-accounts"></a>Azure Active Directory B2C: fornire l'iscrizione e l'accesso agli utenti con account QQ

> [!NOTE]
> Questa funzionalità è in anteprima.
> 

## <a name="create-a-qq-application"></a>Creare un'applicazione QQ

Per usare QQ come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare un'applicazione QQ e inserire i parametri corretti. Per eseguire questa operazione è necessario disporre di un account QQ. Se non si ha un account, è possibile crearne uno nel sito [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Registrazione per il programma per sviluppatori QQ

1. Passare al [portale per sviluppatori QQ](http://open.qq.com) e accedere con le credenziali dell'account QQ.
2. Dopo l'accesso passare a [http://open.qq.com/reg](http://open.qq.com/reg) per registrarsi come sviluppatore.
3. Nel menu selezionare **个人** (sviluppatore singolo).
4. Immettere le informazioni richieste nel modulo e fare clic su **下一步** (avanti).
5. Questa operazione completerà il processo di verifica email.

> [!NOTE]
> È necessario attendere l'approvazione per alcuni giorni dopo la registrazione come sviluppatore. 

### <a name="register-a-qq-application"></a>Registrare un'applicazione QQ

1. Passare a [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
2. Fare clic su **应用管理** (gestione app).
3. Fare clic su **创建应用** (crea app).
4. Immettere le informazioni di connessione necessarie.
5. Fare clic su **创建应用** (crea app).
6. Immettere le informazioni necessarie.
7. Per **授权回调域** (URL di callback) immettere `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`. Ad esempio, se `tenant_name` è contoso.onmicrosoft.com, impostare l'URL a `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
8. Fare clic su **创建应用** (crea app).
9. Nella pagina di conferma fare clic su **应用管理** (gestione app) per tornare alla pagina di gestione delle app.
10. Fare clic su **查看** (visualizza) accanto all'app appena creata.
11. Fare clic su **修改** (modifica).
12. Nella parte superiore della pagina copiare l'**ID APP** e la **CHIAVE APP**.

## <a name="configure-qq-as-an-identity-provider-in-your-tenant"></a>Configurare QQ come provider di identità nel tenant
1. Seguire questa procedura per [passare al pannello delle funzionalità B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) nel portale di Azure.
2. Nel pannello delle funzionalità di B2C, fare clic su **Provider di identità**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. Fornire un **Nome** per la configurazione del provider di identità. Ad esempio, immettere "QQ".
5. Fare clic su **Tipo di provider di identità**, selezionare **QQ** e fare clic su **OK**.
6. Fare clic su **Configura questo provider di identità**
7. Immettere la **chiave app** copiata in precedenza come **ID client**.
8. Immettere il **segreto app** copiato in precedenza come **segreto client**.
9. Fare clic su **OK** e su **Crea** per salvare la configurazione di QQ.

