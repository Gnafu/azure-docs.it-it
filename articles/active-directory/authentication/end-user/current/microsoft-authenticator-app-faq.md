---
title: Informazioni sull'app Microsoft Authenticator - Azure AD | Microsoft Docs
description: Elenca le domande frequenti e le risposte correlate all'app Microsoft Authentication e ad Azure Multi-Factor Authentication.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/08/2018
ms.author: lizross
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: 9027b09c186dfb7661fc63f200f4d2e5da96a70a
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130102"
---
# <a name="microsoft-authenticator-app-faq"></a>Domande frequenti sull'app Microsoft Authenticator

Questo articolo risponde a domande comuni sull'app Microsoft Authenticator. Se non viene visualizzata una risposta alla domanda, accedere al [forum dell'app Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp). È anche possibile consultare altre domande frequenti su una funzionalità specifica relativa all'app, le [domande frequenti sull'accesso con il telefono](microsoft-authenticator-app-phone-signin-faq.md).

L'app Microsoft Authenticator ha sostituito l'app Azure Authenticator ed è consigliata quando si usa Azure Multi-Factor Authentication. L'app Microsoft Authenticator è disponibile per [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) e [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="what-data-does-the-authenticator-store-on-my-behalf-and-how-can-i-delete-it"></a>Quali dati vengono archiviati da Authenticator per conto dell'utente e com'è possibile eliminarli?

Microsoft Authenticator archivia le informazioni sull'account create al momento dell'aggiunta dell'account. Quando si usa Authenticator, viene creato un log di diagnostica a scopo di debug che archivia dati utili che consentono a Microsoft di diagnosticare eventuali problemi imprevisti. È possibile accedere ai dati di log aprendo **?** > **Invia log** > **Visualizza log**.

È possibile eliminare i dati eliminando il riquadro dell'account. L'eliminazione dell'account comporta anche l'eliminazione di tutte le informazioni sull'account usate dall'applicazione, inclusi i log. 

Per altre informazioni sul modo in cui Microsoft usa i dati, visitare: https://servicetrust.microsoft.com/ViewPage/PrivacyGettingStarted

### <a name="what-are-the-codes-in-the-app-for-why-does-the-number-keep-counting-down"></a>A cosa servono i codici nell'app? Perché il numero continua a diminuire?

Quando si apre l'app Microsoft Authenticator vengono visualizzati gli account aggiunti e un numero di sei o otto cifre vicino a ognuno di essi. Può essere presente anche un timer di trenta secondi che procede a ritroso.

Questi codici vengono usati quando si accede al proprio account. Dopo aver immesso il nome utente e la password, potrebbe essere necessario immettere un codice di verifica. Aprire l'app Microsoft Authenticator e copiare il codice visualizzato al momento. Immettere il codice nella pagina di accesso per terminare.

I codici cambiano ogni 30 secondi in modo che non venga usato lo stesso codice due volte. A differenza di una password, che deve essere nota all'utente, l'idea è che solo un utente con accesso al telefono possa entrare in possesso del codice di verifica.

I codici non richiedono l'uso di Internet e non creano traffico dati, pertanto non è necessario che il servizio telefonico sia attivo per eseguire l'accesso. Quando viene chiusa, l'app non continua a essere eseguita in background e quindi non scarica la batteria. È possibile chiudere l'app e ignorarla fino al successivo accesso.  

### <a name="i-only-get-notifications-when-i-have-the-app-open-if-the-app-isnt-open-i-dont-get-any-notifications"></a>Ricevo le notifiche solo quando l'app è aperta. Se l'applicazione non è aperta, non ricevo alcuna notifica.

Se si ricevono le notifiche, ma non si sentono rumori o vibrazioni nonostante la suoneria sia attivata, verificare innanzitutto le impostazioni dell'applicazione. Abilitare l'app a emettere suoni o vibrazioni quando riceve una notifica.

Se non si ricevono le notifiche, controllare quanto segue:

- Il telefono è in modalità non disturbare o non interattiva? Tale modalità può impedire l'invio di notifiche da parte delle app.
- Si ricevono notifiche da altre app? In questo caso, potrebbe esserci un problema con le connessioni di rete sul telefono o con il canale di notifiche Android o Apple. Nel primo caso è possibile risolvere il problema con le impostazioni del telefono, mentre nel secondo caso è possibile che sia necessario contattare il provider di servizi per assistenza specifica.
- Si ricevono notifiche per alcuni account sull'app, ma non per altri? In caso affermativo, rimuovere l'account problematico dall'app e aggiungerlo nuovamente per abilitare le notifiche push.

Se i problemi persistono anche dopo aver provato questi suggerimenti, è possibile inviare i log per la diagnostica. Andare alle impostazioni dell'app e quindi selezionare **Guida e commenti** e **Invia log**. Accedere quindi al [forum delle app di Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) per illustrare il problema e descrivere i passaggi provati fino ad ora.

### <a name="im-already-using-the-microsoft-authenticator-application-for-verification-codes-how-do-i-switch-to-one-click-push-notifications"></a>Sto già usando l'app Microsoft Authenticator per i codici di verifica. Come posso attivare le notifiche push con un clic?
L'approvazione dell'accesso tramite notifica push è disponibile solo per account Microsoft personali o account Microsoft aziendali o dell'istituto di istruzione, non per account di terze parti come Google o Facebook. Se si ha un account Microsoft aziendale o dell'istituto di istruzione, tuttavia, l'organizzazione può scegliere di disabilitare questa opzione.

Se si usa un account Microsoft per l'account personale e si vogliono attivare le notifiche push, è necessario aggiungere nuovamente l'account. Registrare nuovamente il dispositivo con il proprio account e impostare le notifiche push.  

Se si usa Microsoft Authenticator per l'account aziendale o dell'istituto di istruzione, l'organizzazione decide se consentire le notifiche con un clic.

### <a name="do-one-click-push-notifications-work-for-non-microsoft-accounts"></a>Le notifiche push con un clic funzionano con gli account non Microsoft?
No, le notifiche push funzionano solo con gli account Microsoft e gli account Azure Active Directory. Se l'azienda o l'istituto di istruzione usa account di Azure AD, è possibile che disabilitino questa funzionalità.  

### <a name="i-got-a-new-device-or-restored-my-device-from-a-backup-how-do-i-set-up-my-accounts-in-the-microsoft-authenticator-app-again"></a>Dopo avere ricevuto un nuovo dispositivo o aver ripristinato il dispositivo da un backup, come si configurano di nuovo gli account personali nell'app Microsoft Authenticator?
Se si esegue un dispositivo iOS, è stato attivato il **backup automatico** ed è stata creata una copia di backup degli account nel dispositivo precedente, è possibile usare tale backup per ripristinare le credenziali degli account nel nuovo dispositivo. Per altre informazioni, vedere l'articolo [Eseguire il backup e il ripristino delle credenziali dell'account con l'app Microsoft Authenticator](../../../../multi-factor-authentication/end-user/microsoft-authenticator-app-backup-and-recovery.md). 

### <a name="i-lost-my-device-or-moved-on-to-a-new-device-how-do-i-make-sure-notifications-dont-continue-to-go-to-my-old-device"></a>In caso di smarrimento del dispositivo o dopo il passaggio a un nuovo dispositivo, come è possibile assicurarsi che le notifiche non continuino a essere inviate al dispositivo precedente?  
L'aggiunta dell'app Microsoft Authenticator al nuovo dispositivo iOS non rimuove automaticamente l'app dal dispositivo precedente. Anche l'eliminazione dell'app dal dispositivo precedente non è sufficiente. È necessario sia eliminare l'app dal dispositivo precedente sia comunicare a Microsoft o all'organizzazione di rimuovere il dispositivo precedente e annullarne la registrazione dall'account personale.
- **Per rimuovere l'app da un dispositivo usando un account Microsoft personale.** Passare all'area di verifica in due passaggi della pagina [Sicurezza dell'account](https://account.microsoft.com/security)  e scegliere di disattivare la verifica per il dispositivo precedente.  
- **Per rimuovere l'app da un dispositivo usando un account Microsoft aziendale o dell'istituto di istruzione.** Passare all'area di verifica in due passaggi della pagina [MyApps](https://myapps.microsoft.com/) o al portale personalizzato dell'organizzazione e scegliere di disattivare la verifica per il dispositivo precedente. 



### <a name="how-do-i-remove-an-account-from-the-app"></a>Come rimuovere un account dall'app?
* iOS: dalla schermata principale, scorrere rapidamente verso sinistra un riquadro dell'account. Selezionare **Elimina**.
* Windows Phone: dalla schermata principale, selezionare il pulsante di menu, quindi **Modifica account**. Toccare la **X** accanto al nome dell'account.
* Android: dalla schermata principale, selezionare il pulsante di menu e quindi **Modifica account**. Toccare la **X** accanto al nome dell'account.

Se si ha un dispositivo registrato con l'organizzazione, potrebbe essere necessario completare un passaggio aggiuntivo per rimuovere l'account. In questi dispositivi l'app Microsoft Authenticator viene registrata automaticamente come amministratore del dispositivo. Se si vuole disinstallare completamente l'app, è necessario prima annullare la registrazione dell'app nelle impostazioni relative.

### <a name="why-does-the-app-request-so-many-permissions"></a>Perché l'app richiede così tante autorizzazioni?
Di seguito è riportato l'elenco completo delle autorizzazioni che è possibile richiedere con una descrizione di come vengono usate nell'app. Le autorizzazioni specifiche visualizzate dipendono dal tipo di telefono in uso.

* **Fotocamera**: usata per effettuare la scansione dei codici a matrice quando si aggiunge un account aziendale, dell'istituto di istruzione o non Microsoft.
* **Contatti e telefono**: usata per semplificare il processo mediante la ricerca di account esistenti nel telefono quando si esegue l'accesso con l'account Microsoft personale.
* **SMS**: usata per assicurarsi che il numero di telefono corrisponda al numero registrato. Quando si accede con l'account Microsoft personale per la prima volta.  Viene inviato un SMS al telefono su cui è stata scaricata l'app, che include un codice di verifica di 6-8 cifre. Anziché richiedere di trovare il codice e immetterlo nell'app, viene inserito nel testo del messaggio.
* **Visualizzazione anche in altre app**: la notifica ricevuta per verificare l'identità viene visualizzata anche in qualsiasi altra app che potrebbe essere in esecuzione.
* **Ricezione di dati da Internet**: questa autorizzazione è necessaria per l'invio di notifiche.
* **Impedire la sospensione del telefono**: se si registra il dispositivo nell'organizzazione, l'organizzazione può modificare questo criterio nel telefono.
* **Controllo vibrazione**: è possibile scegliere se si vuole che il dispositivo vibri alla ricezione di una notifica per la verifica dell'identità.
* **Uso dell'hardware per la lettura delle impronte digitali**: alcuni account aziendali e dell'istituto di istruzione richiedono un PIN aggiuntivo al momento della verifica dell'identità. Per semplificare il processo, è possibile usare l'impronta digitale anziché immettere il PIN.
* **Visualizzazione delle connessioni di rete**: quando si aggiunge un account Microsoft, l'app richiede la connessione di rete o Internet.
* **Lettura del contenuto della risorsa di archiviazione**: questa autorizzazione viene usata solo quando si segnala un problema tecnico tramite le impostazioni dell'app. Vengono raccolte alcune informazioni dalla risorsa di archiviazione per diagnosticare il problema.
* **Accesso completo alla rete**: questa autorizzazione è necessaria per l'invio di notifiche per la verifica dell'identità.
* **Esecuzione all'avvio**: se si riavvia il telefono, questa autorizzazione consente di continuare a ricevere notifiche per la verifica dell'identità.

### <a name="why-does-the-microsoft-authenticator-app-allow-you-to-approve-a-request-without-unlocking-the-device"></a>Perché l'app Microsoft Authenticator consente di approvare una richiesta senza sbloccare il dispositivo?

Non è necessario sbloccare il dispositivo per approvare le richieste di verifica, poiché è sufficiente dimostrare che si ha un telefono a portata di mano. La verifica in due passaggi richiede di dimostrare di conoscere una determinata informazione e di essere in possesso di qualcosa, ovvero di conoscere la password. e di essere in possesso del telefono, configurato con l'app Microsoft Authenticator e registrato come prova MFA. Avendo il telefono e approvando la richiesta vengono quindi soddisfatti i criteri per il secondo fattore dell'autenticazione.

### <a name="what-does-the-lock-icon-in-the-account-list-mean"></a>Cosa significa l'icona a forma di lucchetto nell'elenco degli account?

L'icona del lucchetto indica che il dispositivo è registrato in Azure AD e registrato per l'account. La registrazione del dispositivo per iOS avviene durante l'enrollment di Microsoft Intune.

## <a name="next-steps"></a>Passaggi successivi

### <a name="contact-us"></a>Contatti
Se questo articolo non ha fornito le risposte necessarie, saranno apprezzati commenti e suggerimenti. Visitare il [forum relativo all'app Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) per pubblicare una domanda e ricevere assistenza dalla community o lasciare un commento in questa pagina.


### <a name="related-topics"></a>Argomenti correlati
* [Informazioni sulla verifica in due passaggi](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) per l'account Microsoft
* [Problemi con la verifica in due passaggi](multi-factor-authentication-end-user-troubleshoot.md) per un account aziendale o dell'istituto di istruzione
* [Usare Microsoft Authenticator per accedere dal telefono](microsoft-authenticator-app-phone-signin-faq.md)
