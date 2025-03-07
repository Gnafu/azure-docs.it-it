---
title: Configurare le informazioni di sicurezza (anteprima) per l'uso dell'indirizzo di posta elettronica - Azure Active Directory | Microsoft Docs
description: Come configurare le informazioni di sicurezza per verificare la propria identità usando l'indirizzo di posta elettronica.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec93574a8da051b5cf77a37af1030d0c22dfc4a9
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382908"
---
# <a name="set-up-security-info-preview-to-use-your-email-address"></a>Configurare le informazioni di sicurezza (anteprima) per usare l'indirizzo di posta elettronica

È possibile seguire questi passaggi per aggiungere il metodo di reimpostazione della password. Dopo averle configurate per la prima volta, è possibile tornare alla pagina **Info di sicurezza** per aggiungere, aggiornare o eliminare le informazioni di sicurezza.

Dopo aver configurato il metodo di reimpostazione della password, è necessario configurare anche il metodo di verifica a due fattori, usando un [app di autenticazione](security-info-setup-auth-app.md), l'[invio di SMS](security-info-setup-text-msg.md) o una [chiamata telefonica](security-info-setup-phone-number.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="set-up-your-email-address-from-the-security-info-page"></a>Configurare l'indirizzo di posta elettronica dalla pagina Info di sicurezza

A seconda delle impostazioni dell'organizzazione, potrebbe essere possibile usare l'indirizzo di posta elettronica come metodo per le informazioni di sicurezza.

>[!Note]
>È consigliabile usare un indirizzo di posta elettronica che non richieda alcuna password di rete per accedere. Se l'opzione relativa alla posta elettronica non viene visualizzata, è possibile che l'organizzazione non consenta l'uso della posta elettronica per la verifica. In questo caso, è necessario scegliere un altro metodo oppure contattare l'amministratore per ottenere assistenza.

### <a name="to-set-up-your-email-address"></a>Per configurare il proprio indirizzo di posta elettronica

1. Accedere all'account aziendale o dell'istituto di istruzione e quindi passare alla pagina https://myprofile.microsoft.com/.

    ![Pagina Profilo personale con i collegamenti evidenziati delle Informazioni di sicurezza](media/security-info/securityinfo-myprofile-page.png)

2. Selezionare **Informazioni di sicurezza** dal riquadro di spostamento a sinistra o dal collegamento nel blocco **Informazioni di sicurezza**, quindi selezionare **Aggiungi metodo** dalla pagina **Informazioni di sicurezza**.

    ![Pagina Info di sicurezza con l'opzione Aggiungi metodo evidenziata](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Nella pagina **Aggiungi un metodo** selezionare **Posta elettronica** dall'elenco a discesa e selezionare **Aggiungi**.

    ![Finestra di dialogo Aggiungi un metodo, con l'opzione Posta elettronica selezionata](media/security-info/securityinfo-myprofile-addemail.png)

4. Nella pagina **Posta elettronica** digitare l'indirizzo di posta elettronica (ad esempio, alain@gmail.com) e quindi selezionare **Avanti**.

    ![Aggiungere il numero di telefono e scegliere le chiamate telefoniche](media/security-info/securityinfo-myprofile-emailaddress.png)

    >[!Important]
    >Questo non può essere l'indirizzo di posta elettronica aziendale o dell'istituto di istruzione.

5. Digitare il codice inviato all'indirizzo di posta elettronica specificato e quindi selezionare **Avanti**.

    ![Aggiungere il numero di telefono e scegliere gli SMS](media/security-info/securityinfo-myprofile-emailcode.png)

    Le informazioni di sicurezza vengono aggiornate ed è possibile usare l'indirizzo di posta elettronica per verificare l'identità quando si usa la reimpostazione della password.

## <a name="delete-your-email-address-from-your-security-info-methods"></a>Eliminare l'indirizzo di posta elettronica dai metodi di gestione delle informazioni di sicurezza

Per non usare più l'indirizzo di posta elettronica come metodo di gestione delle informazioni di sicurezza, è possibile rimuoverlo dalla pagina **Info di sicurezza**.

>[!Important]
>Se si elimina per errore l'indirizzo di posta elettronica, non è possibile annullare l'azione. Sarà necessario aggiungere nuovamente il metodo, seguendo la procedura illustrata nella sezione [Configurare l'indirizzo di posta elettronica](#set-up-your-email-address-from-the-security-info-page) di questo articolo.

### <a name="to-delete-your-email-address"></a>Per eliminare l'indirizzo di posta elettronica

1. Nella pagina **Info di sicurezza** selezionare il collegamento **Elimina** accanto all'opzione **Posta elettronica**.

    ![Collegamento per eliminare il metodo basato sul telefono dalle informazioni di sicurezza](media/security-info/securityinfo-myprofile-emaildelete.png)

2. Selezionare **Sì** nella finestra di dialogo di conferma per eliminare l'account di **posta elettronica**. L'account di posta elettronica, dopo essere stato eliminato, viene rimosso dalle informazioni di sicurezza e non viene più visualizzato nella pagina **Info di sicurezza**.

## <a name="additional-security-info-methods"></a>Metodi aggiuntivi per le informazioni di sicurezza

In base all'operazione da eseguire, sono disponibili opzioni aggiuntive relative alle modalità di contatto da parte dell'organizzazione per verificare l'identità dell'utente, tra cui:

- **App di autenticazione.** Scaricare e usare un'app di autenticazione per ottenere una notifica di approvazione o un codice di approvazione generato in modo casuale per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come configurare e usare l'app Microsoft Authenticator, vedere [Configurare le informazioni di sicurezza per l'uso di un'app di autenticazione](security-info-setup-auth-app.md).

- **Mobile device text.** (SMS su dispositivo mobile) Immettere il numero del dispositivo mobile per ricevere via SMS un codice da usare per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come verificare l'identità con un SMS, vedere [Configurare le informazioni di sicurezza per l'invio di SMS](security-info-setup-text-msg.md).

- **Chiamata sul dispositivo mobile o sul telefono dell'ufficio.** Immettere il numero del dispositivo mobile per ricevere una chiamata per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come verificare l'identità con un numero di telefono, vedere [Configurare le informazioni di sicurezza per l'uso di chiamate telefoniche](security-info-setup-phone-number.md).

- **Chiave di sicurezza.** Registrare la chiave di sicurezza compatibile con Microsoft e usarla insieme a un PIN per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come verificare l'identità con una chiave di sicurezza, vedere [configurare le informazioni di sicurezza per l'uso di una chiave di sicurezza](security-info-setup-security-key.md).

- **Domande di sicurezza.** Rispondere ad alcune domande di sicurezza create dall'amministratore per l'organizzazione. Questa opzione è disponibile solo per la reimpostazione della password e non per la verifica in due passaggi. Per istruzioni dettagliate su come configurare le domande di sicurezza, vedere l'articolo [Configurare le informazioni di sicurezza per l'uso di domande di sicurezza](security-info-setup-questions.md).

    >[!Note]
    >Se alcune opzioni non sono disponibili, è probabile che l'organizzazione non consenta di usare questi metodi. In questo caso, è necessario scegliere un metodo disponibile oppure contattare l'amministratore per ottenere assistenza.

## <a name="next-steps"></a>Passaggi successivi

- Se la password viene persa o dimenticata, reimpostarla dal [portale per la reimpostazione della password](https://passwordreset.microsoftonline.com/) oppure seguire i passaggi nell'articolo [Reimpostare la password aziendale o dell'istituto di istruzione](user-help-reset-password.md).

- Ottenere informazioni e suggerimenti per la risoluzione dei problemi di accesso nell'articolo [Quando non riesci ad accedere al tuo account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
