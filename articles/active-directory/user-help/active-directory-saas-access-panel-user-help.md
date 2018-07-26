---
title: Ottenere assistenza durante l'accesso e l'uso del portale delle app personali in Azure AD | Microsoft Docs
description: Ottenere assistenza durante l'accesso e l'esecuzione di attività comuni nel riquadro di accesso.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: lizross
ms.reviewer: japere
ms.openlocfilehash: e4e0dd91110eb56623bdb73b99eed4a35ccb751c
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/14/2018
ms.locfileid: "39058173"
---
# <a name="troubleshoot-issues-with-accessing-and-using-the-my-apps-portal"></a>Risolvere i problemi di accesso e uso del portale delle app personali

Se si verificano problemi durante l'accesso al portale delle app personali o durante il suo uso, fare riferimento ai suggerimenti sulla risoluzione dei problemi seguenti prima di contattare il supporto tecnico o l'amministratore per ottenere assistenza.

## <a name="i-am-having-trouble-signing-into-the-my-apps-portal"></a>Si è verificato un problema durante l'accesso al portale App personali

Fare riferimento ai suggerimenti generali seguenti:

- Verificare in primo luogo di usare l'URL corretto: [https://myapps.microsoft.com](https://myapps.microsoft.com).
- Provare ad aggiungere l'URL ai siti attendibili del browser.
- Assicurarsi che la password sia corretta e che non sia scaduta. Per altre informazioni, vedere [Reimpostare la password aziendale o scolastica](active-directory-passwords-update-your-own-password.md).
- Assicurarsi che le informazioni di contatto per l'autenticazione siano aggiornate e che non impediscano l'accesso. Per altre informazioni, vedere [Quali sono i vantaggi di Azure Multi-Factor Authentication?](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user).
- Cancellare i cookie del browser e riprovare ad accedere.

Se i problemi persistono durante i tentativi di accesso, rivolgersi all'amministratore.

## <a name="i-seem-to-be-having-password-issues"></a>Si è verificato un problema con la password

Se si dimentica la password, la si vuole modificare, il personale IT non l'ha mai inviata oppure se l'account è stato bloccato, vedere [Help, I forgot my Azure AD password](active-directory-passwords-update-your-own-password.md) (Password di Azure AD dimenticata).

## <a name="i-need-to-register-for-password-reset"></a>È necessario eseguire la registrazione per la reimpostazione della password

È possibile reimpostare la password o sbloccare l'account senza la necessità di rivolgersi a un'altra persona usando la funzionalità di reimpostazione della password self-service (SSPR). Per poter usare questa funzionalità, è prima necessario registrare i metodi di autenticazione in uso o confermare i metodi di autenticazione predefiniti richiesti dall'amministratore. Per altre informazioni, vedere [Eseguire la registrazione per la reimpostazione password self-service](active-directory-passwords-reset-register.md).

## <a name="i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Si riscontrano problemi con l'installazione dell'estensione di accesso sicuro per le app personali

Per il portale delle app personali è necessario un browser che supporti JavaScript e in cui sia abilitato CSS. Se si usano app con accesso Single Sign-On basato su password è necessario installare anche l'estensione corrispondente. L'estensione viene scaricata automaticamente quando si avvia un'applicazione configurata per le app con accesso Single Sign-On basato su password.

Verificare che vengano soddisfatti i requisiti del browser seguenti:

- **Microsoft Edge**: su Windows 10 Anniversary Edition o versioni successive.
- **Chrome**: in Windows 7 o versione successiva e in Mac OS X o versione successiva.
- **Firefox 26.0 o versioni successive**: in Windows XP SP2 o versioni successive e in Mac OS X 10.6 o versioni successive.
- **Internet Explorer 11**: in Windows 7 o versioni successive (supporto limitato).

È anche possibile scaricare l'estensione direttamente dai siti seguenti:

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

Se l'estensione è stata installata e continuano a verificarsi problemi, procedere come segue:

- Verificare che l'estensione sia abilitata nelle impostazioni delle estensioni del browser.
- Riavviare il browser e accedere al portale delle app personali.
- Cancellare i cookie del browser e accedere al portale delle app personali.
- Per accedere a uno strumento di diagnostica e per istruzioni passo passo sulla configurazione dell'estensione per Internet Explorer, seguire la procedura di [risoluzione dei problemi relativi all'estensione del riquadro di accesso per Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Usare l'estensione per l'accesso sicuro alle app personali
* Se si usa un URL delle app personali diverso da `https://myapps.microsoft.com`, configurare l'URL predefinito seguendo questa procedura:
   1. *Senza* accedere all'estensione, fare clic con il pulsante destro del mouse sull'icona dell'estensione.
   2. Nel menu selezionare **My Apps URL** (URL app personali).
   3. Selezionare l'URL predefinito.
   4. Selezionare l'icona dell'estensione.
   5. Per accedere all'estensione, selezionare **Sign in to get started** (Accedi per iniziare).

* Per accedere direttamente a un'app dal browser, seguire questa procedura:
   1. Dopo aver installato l'estensione, accedervi selezionando **Sign in to get started** (Accedi per iniziare).
   2. Accedere all'app con l'URL di accesso.  
       L'URL di accesso è in genere l'URL dell'app che visualizza il modulo di accesso.
      L'estensione dovrebbe cambiare stato e segnalare che è disponibile una password.
   3. Per accedere, selezionare l'icona dell'estensione.

* Per avviare un'app dall'estensione, seguire questa procedura:
   1. Dopo aver installato l'estensione, accedervi selezionando **Sign in to get started** (Accedi per iniziare).
   2. Selezionare l'icona dell'estensione per aprirne il menu.
   3. Cercare un'app disponibile nel portale delle app personali.
   4. Nell'elenco dei risultati della ricerca selezionare l'app.  
       Nell'elenco di collegamenti **Usati di recente** verranno visualizzate le ultime tre app usate.

> [!NOTE]
> Queste funzionalità sono disponibili solo per Microsoft Edge, Chrome e Firefox.

## <a name="how-do-i-add-a-new-app"></a>Come aggiungere una nuova app

1.  Nella pagina **App** selezionare **Aggiungi app**.
2.  Cercare l'applicazione che si desidera aggiungere e quindi selezionare **Aggiungi**.

   > [!NOTE]
   > * È possibile accedere a questa opzione solo se l'amministratore l'ha abilitata per l'account.
   > * Se l'app richiede l'autorizzazione, può essere necessario attendere l'approvazione dell'amministratore.

## <a name="how-do-i-manage-my-group-memberships"></a>Come gestire l'appartenenza ai gruppi

Selezionare il riquadro **Gruppi** e quindi eseguire una delle operazioni seguenti:
* Per creare un gruppo, in **Gruppi di cui si è proprietari** selezionare **Crea gruppo** e quindi seguire le istruzioni.
* Per partecipare a un gruppo, in **Gruppi a cui si appartiene** fare clic su **Partecipa a gruppo** e quindi seguire le istruzioni.

   > [!NOTE]
   > * È possibile accedere a questa opzione solo se l'amministratore l'ha abilitata per l'account.
   > * Se si è membri di un gruppo, è possibile visualizzarne i dettagli e abbandonare il gruppo.
   > * Se si è proprietari di un gruppo, è possibile visualizzarne i dettagli, aggiungere o rimuovere i membri e abbandonare il gruppo.