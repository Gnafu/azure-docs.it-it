---
title: Integrazione di Desktop remoto Gateway con Azure multi-factor authentication Extension-Azure Active Directory
description: Integrare l'infrastruttura Gateway Desktop remoto con Azure MFA usando l'estensione NPS (Network Policy Server, Server dei criteri di rete) per Microsoft Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf9188502dd2b17bcd898e2655138b06cfe5cebf
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898546"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Integrare l'infrastruttura Gateway Desktop remoto con Azure MFA usando l'estensione NPS (Network Policy Server, Server dei criteri di rete) e Azure AD

Questo articolo contiene i dettagli relativi all'integrazione dell'infrastruttura Gateway Desktop remoto con Azure Multi-Factor Authentication (MFA) usando l'estensione NPS (Network Policy Server, Server dei criteri di rete) per Microsoft Azure.

L'estensione Server dei criteri di rete per Azure consente agli utenti di proteggere l'autenticazione di client Remote Authentication Dial-In User Service tramite [Multi-Factor Authentication](multi-factor-authentication.md) di Azure basato sul cloud. Questa soluzione fornisce la verifica in due passaggi per l'aggiunta di un secondo livello di sicurezza agli accessi e alle transazioni degli utenti.

Questo articolo contiene istruzione dettagliate per l'integrazione dell'infrastruttura Gateway Desktop remoto con Azure MFA tramite l'estensione NPS per Azure. Questo consente la verifica sicura per gli utenti che tentano di accedere a Gateway Desktop remoto.

> [!NOTE]
> Questo articolo non deve essere usato con distribuzioni di server MFA e deve essere usato solo con distribuzioni di Azure MFA (basate sul cloud).

Servizi di accesso e criteri di rete consente alle organizzazioni di eseguire le operazioni seguenti:

* Definire posizioni centrali per la gestione e il controllo delle richieste di rete, indicando quali utenti possono connettersi, le ore del giorno in cui sono consentite le connessioni, la durata delle connessioni, il livello di sicurezza che i client devono usare per la connessione e così via. Invece che specificare questi criteri in ogni VPN o server Gateway Desktop remoto, è possibile specificarli una sola volta in una posizione centrale. Il protocollo RADIUS fornisce servizi centralizzati di autenticazione, autorizzazione e accounting.
* Stabilire e applicare criteri di integrità client di Protezione accesso alla rete che determinano se ai dispositivi viene concesso l'accesso alle risorse di rete con o senza restrizioni.
* Fornire un mezzo per imporre l'autenticazione e l'autorizzazione per l'accesso a commutatori Ethernet e punti di accesso wireless che supportano 802.1x.

In genere, le organizzazioni usano NPS (RADIUS) per semplificare e centralizzare la gestione dei criteri VPN. Tuttavia, molte organizzazioni usano anche Server dei criteri di rete per semplificare e centralizzare la gestione di criteri di autorizzazione connessioni Desktop remoto.

Le organizzazioni possono anche integrare Server dei criteri di rete con Azure MFA per migliorare la sicurezza e offrire un livello elevato di conformità. In questo modo si garantisce che gli utenti eseguano la verifica in due passaggi per accedere a Gateway Desktop remoto. Affinché venga concesso loro l'accesso, gli utenti devono specificare la combinazione di nome utente/password con informazioni sotto il controllo dell'utente. Queste informazioni devono essere attendibili e non facilmente duplicabili, ad esempio un numero di telefono cellulare, un numero di rete fissa, un'applicazione in un dispositivo mobile e così via. RDG supporta attualmente la telefonata e le notifiche push dai metodi delle app Microsoft Authenticator per 2FA. Per altre informazioni sui metodi di autenticazione supportati, vedere la sezione [Determinare i metodi di autenticazione che è possibile usare](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use).

Prima che fosse disponibile l'estensione NPS per Azure, i clienti che volevano implementare la verifica in due passaggi per ambienti Server dei criteri di rete e Azure MFA integrati dovevano configurare e gestire un server MFA distinto nell'ambiente locale, come documentato nello scenario relativo a [Gateway Desktop remoto e server Azure Multi-Factor Authentication con RADIUS](howto-mfaserver-nps-rdg.md).

La disponibilità dell'estensione NPS per Azure offre ora alle organizzazioni la possibilità di scegliere se distribuire una soluzione MFA locale o una soluzione MFA basata sul cloud per l'autenticazione sicura dei client RADIUS.

## <a name="authentication-flow"></a>Flusso di autenticazione

Perché venga concesso loro l'accesso a risorse di rete tramite Gateway Desktop remoto, gli utenti devono soddisfare le condizioni specificate nei criteri di autorizzazione connessioni Desktop remoto e nei criteri di autorizzazione delle risorse di Desktop remoto. I criteri di autorizzazione connessioni Desktop remoto specificano gli utenti autorizzati a connettersi a Gateway Desktop remoto. I criteri di autorizzazione delle risorse di Desktop remoto specificano le risorse di rete, come desktop remoti o app remote, ai quali l'utente è autorizzato a connettersi tramite Gateway Desktop remoto.

Un'istanza di Gateway Desktop remoto può essere configurata per l'uso di un archivio centrale di criteri per i criteri di autorizzazione delle risorse di Desktop remoto. I criteri di autorizzazione delle risorse di Desktop remoto non possono usare criteri centrali, in quanto vengono elaborati da Gateway Desktop remoto. Un esempio di Gateway Desktop remoto configurato per l'uso di un archivio centrale di criteri per criteri di autorizzazione connessioni Desktop remoto è un client RADIUS connesso a un altro server NPS che funge da archivio centrale di criteri.

Quando l'estensione NPS per Azure è integrata con Server dei criteri di rete e Gateway Desktop remoto, il corretto flusso di autenticazione è quello illustrato di seguito:

1. Il server Gateway Desktop remoto riceve da un utente Desktop remoto una richiesta di autenticazione per la connessione a una risorsa, ad esempio una sessione Desktop remoto. Fungendo da client RADIUS, il server Gateway Desktop remoto converte la richiesta in un messaggio di richiesta di accesso RADIUS e invia il messaggio al server RADIUS (Server dei criteri di rete) in cui è installata l'estensione NPS.
1. La combinazione di nome utente e password viene verificata in Active Directory e l'utente viene autenticato.
1. Se vengono soddisfatte tutte le condizioni specificate nella richiesta di connessione NPS e nei criteri di rete (ad esempio per quanto riguarda le restrizioni relative all'ora del giorno o all'appartenenza a gruppi), l'estensione NPS attiva una richiesta di autenticazione secondaria con Azure MFA.
1. Azure MFA comunica con Azure AD, recupera i dettagli dell'utente ed esegue l'autenticazione secondaria tramite i metodi supportati.
1. Una volta completata la verifica MFA, Azure MFA comunica il risultato all'estensione NPS.
1. Il Server dei criteri di rete in cui è installata l'estensione invia un messaggio di autorizzazione di accesso RADIUS per i criteri di autorizzazione connessioni Desktop remoto al server Gateway Desktop remoto.
1. All'utente viene concesso l'accesso alla risorsa di rete richiesta tramite Gateway Desktop remoto.

## <a name="prerequisites"></a>Prerequisiti

Questa sezione illustra in modo dettagliato i prerequisiti necessari per l'integrazione di Azure MFA con Gateway Desktop remoto. Prima di iniziare, è necessario che siano soddisfatti i prerequisiti seguenti.  

* Infrastruttura Servizi Desktop remoto
* Licenza di Azure MFA
* Software Windows Server
* Ruolo Servizi di accesso e criteri di rete
* Azure Active Directory con sincronizzazione con Active Directory locale
* ID GUID di Azure Active Directory

### <a name="remote-desktop-services-rds-infrastructure"></a>Infrastruttura Servizi Desktop remoto

Deve essere presente un'infrastruttura Servizi Desktop remoto funzionante. In caso contrario, è possibile creare rapidamente questa infrastruttura in Azure usando il modello di avvio rapido seguente: [Create Remote Desktop Session Collection deployment](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment) (Creare una distribuzione della raccolta di sessioni di Desktop remoto).

Se si vuole creare manualmente un'infrastruttura Servizi Desktop remoto locale per scopi di test, completare i passaggi di distribuzione.
**Altre informazioni**: Distribuire Servizi Desktop remoto [con avvio rapido di Azure e la](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-in-azure) [distribuzione dell'infrastruttura RDS di base](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

### <a name="azure-mfa-license"></a>Licenza di Azure MFA

È richiesta una licenza per Azure MFA, disponibile tramite Azure AD Premium o altri pacchetti che la includono. Le licenze in base al consumo per Azure Multi-Factor Authentication, ad esempio le licenze per utente o per autenticazione, non sono compatibili con l'estensione Server dei criteri di rete. Per altre informazioni, vedere [Come ottenere Azure Multi-Factor Authentication](concept-mfa-licensing.md). A scopo di test, è possibile usare una sottoscrizione della versione di valutazione gratuita.

### <a name="windows-server-software"></a>Software Windows Server

L'estensione NPS richiede Windows Server 2008 R2 SP1 o versione successiva con il servizio ruolo NPS installato. Tutti i passaggi in questa sezione sono stati eseguiti usando Windows Server 2016.

### <a name="network-policy-and-access-services-nps-role"></a>Ruolo Servizi di accesso e criteri di rete

Il servizio ruolo NPS fornisce le funzionalità di client e server RADIUS, nonché il servizio di integrità Protezione accesso alla rete. Questo ruolo deve essere installato in almeno due computer dell'infrastruttura: il computer Gateway Desktop remoto e un altro server membro o controller di dominio. Per impostazione predefinita, il ruolo è già presente nel computer configurato come Gateway Desktop remoto.  È necessario installare il ruolo NPS almeno anche in un altro computer, ad esempio un controller di dominio o un server membro.

Per informazioni sull'installazione del servizio ruolo NPS di Windows Server 2012 o versioni successive, vedere [Install a NAP Health Policy Server](https://technet.microsoft.com/library/dd296890.aspx) (Installare un server criteri di integrità Protezione accesso alla rete). Per una descrizione delle procedure consigliate per Server dei criteri di rete, inclusi i consigli sull'installazione di Server dei criteri di rete in un controller di dominio, vedere [Best Practices for NPS](https://technet.microsoft.com/library/cc771746) (Procedure consigliate per Server dei criteri di rete).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory con sincronizzazione con Active Directory locale

Per usare l'estensione NPS, gli utenti locali devono essere sincronizzati con Azure AD e abilitati per MFA. Questa sezione presuppone che gli utenti locali siano sincronizzati con Azure AD tramite AD Connect. Per informazioni su Azure AD Connect, vedere [Integrare le directory locali con Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>ID GUID di Azure Active Directory

Per installare l'estensione Server dei criteri di rete, è necessario conoscere il GUID di Azure AD. Le istruzioni per individuare il GUID di Azure AD sono disponibili nella sezione seguente.

## <a name="configure-multi-factor-authentication"></a>Configurare Multi-Factor Authentication

Questa sezione contiene le istruzioni per l'integrazione di Azure MFA con Gateway Desktop remoto. Un amministratore deve configurare il servizio Azure MFA prima che gli utenti possano effettuare la registrazione automatica dei propri dispositivi o applicazioni a più fattori.

Per informazioni su come abilitare MFA per gli utenti di Azure AD, seguire i passaggi descritti in [Introduzione ad Azure Multi-Factor Authentication nel cloud](howto-mfa-getstarted.md).

### <a name="configure-accounts-for-two-step-verification"></a>Configurare gli account per la verifica in due passaggi

Dopo aver abilitato un account per MFA, è possibile accedere alle risorse governate dai criteri MFA solo dopo aver configurato correttamente un dispositivo attendibile da usare per il secondo fattore di autenticazione e averlo autenticato con la verifica in due passaggi.

Per comprendere al meglio e configurare correttamente i dispositivi per MFA con il proprio account utente, seguire i passaggi descritti in [Quali sono i vantaggi di Azure Multi-Factor Authentication?](../user-help/multi-factor-authentication-end-user.md).

## <a name="install-and-configure-nps-extension"></a>Installare e configurare l'estensione NPS

Questa sezione fornisce istruzioni per configurare l'infrastruttura Servizi Desktop remoto per l'uso di Azure MFA per l'autenticazione client con Gateway Desktop remoto.

### <a name="acquire-azure-active-directory-guid-id"></a>Acquisire l'ID GUID di Azure Active Directory

Come parte della configurazione dell'estensione NPS, è necessario fornire le credenziali di amministratore e l'ID di Azure AD per il tenant di Azure AD. La procedura seguente mostra come ottenere l'ID tenant.

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale del tenant di Azure.
1. Nel riquadro di spostamento sinistro fare clic sull'icona di **Azure Active Directory**.
1. Selezionare **Proprietà**.
1. Nel pannello Proprietà, accanto all'ID directory, fare clic sull'icona **Copia**, mostrata di seguito, per copiare l'ID negli Appunti.

   ![Recupero dell'ID directory dalla portale di Azure](./media/howto-mfa-nps-extension-rdg/image1.png)

### <a name="install-the-nps-extension"></a>Installare l'estensione di Server dei criteri di rete

Installare l'estensione NPS in un server con il ruolo Servizi di accesso e criteri di rete installato. Questo funge da server RADIUS per la progettazione.

> [!Important]
> Assicurarsi di non installare l'estensione NPS nel server Gateway Desktop remoto.
>

1. Scaricare l'[estensione NPS](https://aka.ms/npsmfa).
1. Copiare il file eseguibile di installazione (NpsExtnForAzureMfaInstaller.exe) nel server NPS.
1. Nel server NPS fare doppio clic su **NpsExtnForAzureMfaInstaller.exe**. Se richiesto, fare clic su **Esegui**.
1. Nella finestra di dialogo NPS Extension For Azure MFA Setup (Estensione Server dei criteri di rete per la configurazione di Azure MFA) leggere le condizioni di licenza software, selezionare **Accetto i termini e le condizioni di licenza** e fare clic su **Installa**.
1. Nella finestra di dialogo NPS Extension For Azure MFA Setup (Estensione Server dei criteri di rete per la configurazione di Azure MFA) fare clic su **Chiudi**.

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Configurare i certificati per l'uso con l'estensione NPS tramite uno script di PowerShell

Per garantire comunicazioni protette e sicure, è necessario configurare i certificati che l'estensione NPS può usare. I componenti di Server dei criteri di rete includono uno script di Windows PowerShell che configura un certificato autofirmato per l'uso con Server dei criteri di rete.

Lo script esegue le azioni seguenti:

* Crea un certificato autofirmato
* Associa la chiave pubblica del certificato all'entità servizio in Azure AD
* Archivia il certificato nell'archivio del computer locale
* Concede l'accesso alla chiave privata del certificato all'utente di rete
* Riavvia il servizio Server dei criteri di rete

Per usare certificati personali è necessario associare la chiave pubblica del certificato all'entità servizio in Azure AD e così via.

Per usare lo script, specificare l'estensione con le credenziali amministrative di Azure AD e l'ID tenant di Azure AD copiato in precedenza. Eseguire lo script in ogni server NPS in cui è stata installata l'estensione NPS. Eseguire quindi le operazioni seguenti:

1. Aprire un prompt di Windows PowerShell come amministratore.
1. Al prompt di PowerShell digitare `cd ‘c:\Program Files\Microsoft\AzureMfa\Config’` e premere **INVIO**.
1. Digitare `.\AzureMfaNpsExtnConfigSetup.ps1` e premere **INVIO**. Lo script verifica se il modulo di PowerShell per Azure Active Directory è installato. Se non è installato, lo installa.

   ![Esecuzione di AzureMfaNpsExtnConfigSetup. ps1 in Azure AD PowerShell](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. Dopo che lo script ha verificato l'installazione del modulo di PowerShell, viene visualizzata la finestra di dialogo relativa al modulo di PowerShell per Azure Active Directory. Nella finestra di dialogo immettere le credenziali e la password di amministratore di Azure AD e fare clic su **Accedi**.

   ![Autenticazione per Azure AD in PowerShell](./media/howto-mfa-nps-extension-rdg/image5.png)

1. Quando richiesto, incollare l'ID directory copiato negli Appunti in precedenza e premere **invio**.

   ![Inserimento dell'ID directory in PowerShell](./media/howto-mfa-nps-extension-rdg/image6.png)

1. Lo script crea un certificato autofirmato e apporta altre modifiche alla configurazione. L'output è come quello nell'immagine seguente.

   ![Output di PowerShell che mostra un certificato autofirmato](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>Configurare i componenti di Server dei criteri di rete in Gateway Desktop remoto

In questa sezione si configureranno i criteri di autorizzazione connessioni Gateway Desktop remoto e altre impostazioni RADIUS.

Il flusso di autenticazione richiede che i messaggi RADIUS vengano scambiati tra il Gateway Desktop remoto e il server NPS in cui è installata l'estensione NPS. Questo significa che è necessario configurare le impostazioni del client RADIUS sia su Gateway Desktop remoto sia sul server NPS in cui è installata l'estensione NPS.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Configurare criteri di autorizzazione connessioni per Gateway Desktop remoto per l'uso di un archivio centrale

I criteri di autorizzazione connessioni Desktop remoto specificano i requisiti per la connessione a un server Gateway Desktop remoto. I criteri di autorizzazione connessioni Desktop remoto possono essere archiviati in locale (impostazione predefinita) oppure in un archivio di criteri di autorizzazione connessioni Desktop remoto centrale che esegue Server dei criteri di rete. Per configurare l'integrazione di Azure MFA con Servizi Desktop remoto, è necessario specificare l'uso di un archivio centrale.

1. Nel server Gateway Desktop remoto aprire **Server Manager**.
1. Scegliere **Strumenti** dal menu, fare clic su **Servizi Desktop remoto** e quindi su **Gestione Gateway Desktop remoto**.
1. In Gestione Gateway Desktop remoto fare clic con il pulsante destro del mouse su **\[Nome server\] (locale)** e quindi scegliere **Proprietà**.
1. Nella finestra di dialogo Proprietà selezionare la scheda **Archivio criteri di autorizzazione connessioni Desktop remoto**.
1. Nella scheda Archivio criteri di autorizzazione connessioni Desktop remoto selezionare **Server dei criteri di rete centrale**. 
1. Nel campo **Immettere il nome o l'indirizzo IP del Server dei criteri di rete** digitare l'indirizzo IP o il nome del server in cui è stata installata l'estensione NPS.

   ![Immettere il nome o l'indirizzo IP del server NPS](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Fare clic su **Aggiungi**.
1. Nella finestra di dialogo **Segreto condiviso** immettere un segreto condiviso e quindi fare clic su **OK**. Assicurarsi di registrare il segreto condiviso e di archiviare il record in modo sicuro.

   >[!NOTE]
   >Il segreto condiviso viene usato per stabilire un trust tra i server e i client RADIUS. Creare un segreto lungo e complesso.
   >

   ![Creazione di un segreto condiviso per stabilire una relazione di trust](./media/howto-mfa-nps-extension-rdg/image11.png)

1. Fare clic su **OK** per chiudere la finestra di dialogo.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>Configurare il valore di timeout RADIUS in Server dei criteri di rete per Gateway Desktop remoto

Per garantire il tempo necessario per convalidare le credenziali degli utenti, eseguire la verifica in due passaggi, ricevere le risposte e rispondere a messaggi RADIUS, è necessario regolare il valore di timeout RADIUS.

1. Nel server Gateway Desktop remoto aprire Gestione server. Nel menu fare clic su **Strumenti** e quindi su **Server dei criteri di rete**.
1. Nella console **Server dei criteri di rete (locale)** espandere **Client e server RADIUS** e quindi selezionare **Gruppi di server RADIUS remoti**.

   ![Console di gestione server dei criteri di rete che mostra il server RADIUS remoto](./media/howto-mfa-nps-extension-rdg/image12.png)

1. Nel riquadro dei dettagli fare doppio clic su **GRUPPO DI SERVER GATEWAY DI SERVIZI TERMINAL**.

   >[!NOTE]
   >Questo gruppo di server RADIUS è stato creato durante la configurazione del server centrale per i criteri NPS. Gateway Desktop remoto inoltra i messaggi RADIUS a questo server o gruppo di server, se il gruppo ne contiene più di uno.
   >

1. Nella finestra di dialogo **Proprietà GRUPPO DI SERVER GATEWAY DI SERVIZI TERMINAL** selezionare l'indirizzo IP o il nome del server NPS configurato per archiviare i criteri di autorizzazione connessioni Desktop remoto e quindi fare clic su **Modifica**.

   ![Selezionare l'indirizzo IP o il nome del server NPS configurato in precedenza](./media/howto-mfa-nps-extension-rdg/image13.png)

1. Nella finestra di dialogo **Modifica server RADIUS** selezionare la scheda **Bilanciamento del carico**.
1. Nella campo **Numero di secondi senza risposta prima che la richiesta venga annullata** della scheda **Bilanciamento del carico** modificare il valore predefinito da 3 a un valore compreso tra 30 e 60 secondi.
1. Nel campo **Numero di secondi tra le richieste quando il server viene identificato come non disponibile** modificare il valore di 30 secondi in un valore uguale o maggiore del valore specificato nel passaggio precedente.

   ![Modificare le impostazioni di timeout del server RADIUS nella scheda bilanciamento del carico](./media/howto-mfa-nps-extension-rdg/image14.png)

1. Fare clic su **OK** due volte per chiudere le finestre di dialogo.

### <a name="verify-connection-request-policies"></a>Verificare i criteri di richiesta di connessione

Per impostazione predefinita, quando si configura Gateway Desktop remoto per l'uso di un archivio centrale di criteri per i criteri di autorizzazione connessioni, Gateway Desktop remoto viene configurato per inoltrare richieste di criteri di autorizzazione connessioni al server NPS. Il server NPS in cui è installata l'estensione per Azure MFA elabora la richiesta di accesso RADIUS. I passaggi seguenti mostrano come verificare i criteri di richiesta di connessione predefiniti.

1. Nella console Server dei criteri di rete (locale) in Gateway Desktop remoto espandere **Criteri** e selezionare **Criteri di richiesta di connessione**.
1. Fare doppio clic su **CRITERI DI AUTORIZZAZIONE GATEWAY DI SERVIZI TERMINAL**.
1. Nella finestra **Proprietà CRITERI DI AUTORIZZAZIONE GATEWAY DI SERVIZI TERMINAL** fare clic sulla scheda **Impostazioni**.
1. Nella scheda **Impostazioni** fare clic su **Autenticazione** in Inoltro richiesta di connessione. Il client RADIUS è configurato per inoltrare le richieste di autenticazione.

   ![Configurare le impostazioni di autenticazione che specificano il gruppo di server](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Fare clic su **Annulla**.

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Configurare Server dei criteri di rete nel server in cui è installata l'estensione NPS

Il server NPS in cui è installata l'estensione NPS deve essere in grado di scambiare messaggi RADIUS con il server NPS in Gateway Desktop remoto. Per consentire questo scambio di messaggi, è necessario configurare i componenti di Server dei criteri di rete nel server in cui è installata l'estensione NPS.

### <a name="register-server-in-active-directory"></a>Registrare il server in Active Directory

Per il corretto funzionamento in questo scenario, è necessario registrare il server NPS in Active Directory.

1. Nel Server dei criteri di rete aprire **Gestione server**.
1. In Server Manager fare clic su **Strumenti** e quindi su **Server dei criteri di rete**.
1. Nella console di Server dei criteri di rete fare clic con il pulsante destro del mouse su **Server dei criteri di rete (locale)** e quindi scegliere **Registra server in Active Directory**.
1. Fare clic su **OK** due volte.

   ![Registrare il server NPS in Active Directory](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Lasciare aperta la console per la procedura successiva.

### <a name="create-and-configure-radius-client"></a>Creare e configurare il client RADIUS

Gateway Desktop remoto deve essere configurato come client RADIUS per il server NPS.

1. Nel server NPS in cui è installata l'estensione NPS fare clic con il pulsante destro del mouse su **Client RADIUS** nella console **Server dei criteri di rete (locale)** e scegliere **Nuovo**.

   ![Creare un nuovo client RADIUS nella console NPS](./media/howto-mfa-nps-extension-rdg/image17.png)

1. Nella finestra di dialogo **Nuovo client RADIUS** immettere un nome descrittivo, ad esempio _Gateway_, e l'indirizzo IP o il nome DNS del server Gateway Desktop remoto.
1. Nei campi **Segreto condiviso** e **Conferma segreto condiviso** immettere lo stesso segreto usato in precedenza.

   ![Configurare un nome descrittivo e l'indirizzo IP o DNS](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Fare clic su **OK** per chiudere la finestra di dialogo Nuovo client RADIUS.

### <a name="configure-network-policy"></a>Configurare i criteri di rete

Tenere presente che il Server dei criteri di rete con l'estensione per Azure MFA è l'archivio centrale di criteri designato per i criteri di autorizzazione delle connessioni. Di conseguenza, è necessario implementare criteri di autorizzazione connessioni nel server NPS per autorizzare richieste di connessione valide.  

1. Nel server di Server dei criteri di rete aprire la console Server dei criteri di rete (locale), espandere **Criteri** e fare clic su **Criteri di rete**.
1. Fare clic con il pulsante destro del mouse su **Connessioni ad altri server di accesso** e scegliere **Duplica criterio**.

   ![Duplica la connessione ad altri criteri di accesso ai server](./media/howto-mfa-nps-extension-rdg/image19.png)

1. Fare clic con il pulsante destro del mouse su **Copia di Connessioni ad altri server di accesso remoto** e scegliere **Proprietà**.
1. Nella finestra di dialogo **Copia di Connessioni ad altri server di accesso** immettere un nome appropriato, ad esempio _RDG_CAP_ in **Nome criterio**. Selezionare **Criterio attivato** e fare clic su **Concedi accesso**. Facoltativamente, in **Tipo di server di accesso alla rete** selezionare **Gateway Desktop remoto** oppure lasciare l'impostazione **Non specificato**.

   ![Assegnare un nome al criterio, abilitare e concedere l'accesso](./media/howto-mfa-nps-extension-rdg/image21.png)

1. Fare clic sulla scheda **Vincoli** e selezionare **Consenti ai client di connettersi senza negoziare il metodo di autenticazione**.

   ![Modificare i metodi di autenticazione per consentire ai client di connettersi](./media/howto-mfa-nps-extension-rdg/image22.png)

1. Facoltativamente, è possibile fare clic sulla scheda **Condizioni** e aggiungere le condizioni che devono essere soddisfatte perché la connessione venga autorizzata, ad esempio l'appartenenza a un gruppo di Windows specifico.

   ![Specificare facoltativamente le condizioni di connessione](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Fare clic su **OK**. Quando viene chiesto se visualizzare l'argomento della Guida corrispondente, fare clic su **No**.
1. Assicurarsi che i nuovi criteri siano visualizzati all'inizio dell'elenco, che siano abilitati e che concedano l'accesso.

   ![Spostare i criteri nella parte superiore dell'elenco](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Verificare la configurazione

Per verificare la configurazione è necessario accedere a Gateway Desktop remoto con un client RDP appropriato. Assicurarsi di usare un account consentito dai criteri di autorizzazione connessioni e abilitato per Azure MFA.

Come mostrato nell'immagine seguente, è possibile usare la pagina **Accesso Web Desktop remoto**.

![Test in Desktop remoto Accesso Web](./media/howto-mfa-nps-extension-rdg/image25.png)

Dopo aver immesso correttamente le credenziali per l'autenticazione primaria, la finestra di dialogo Connessione desktop remoto indica lo stato Avvio della connessione remota, come mostrato di seguito. 

Se l'autenticazione con il metodo di autenticazione secondario configurato in precedenza in Azure MFA avviene correttamente, viene stabilita una connessione alla risorsa. Se invece l'autenticazione secondaria non ha esito positivo, l'accesso alla risorsa viene negato. 

![Connessione Desktop remoto l'avvio di una connessione remota](./media/howto-mfa-nps-extension-rdg/image26.png)

Nell'esempio seguente viene usata l'app Authenticator su un dispositivo Windows Phone per fornire l'autenticazione secondaria.

![Esempio Windows Phone app Authenticator che mostra la verifica](./media/howto-mfa-nps-extension-rdg/image27.png)

Dopo aver eseguito correttamente l'autenticazione con il metodo secondario, viene effettuato l'accesso a Gateway Desktop remoto nel modo normale. Tuttavia, poiché è necessario usare un metodo di autenticazione secondario usando un'app per dispositivi mobili in un dispositivo attendibile, il processo di accesso è più sicuro di quanto sarebbe altrimenti.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Visualizzare i log del Visualizzatore eventi per individuare gli eventi di accesso riusciti

Per visualizzare gli eventi di accesso riusciti nei log del Visualizzatore eventi di Windows, è possibile usare il comando seguente di Windows PowerShell per eseguire una query sui log di Servizi terminal Windows e di sicurezza di Windows.

Per eseguire correttamente una query per individuare gli eventi di accesso riusciti nei log operativi del gateway _(Visualizzatore eventi\Registri applicazioni e servizi\Microsoft\Windows\TerminalServices-Gateway\Operational)_ , usare i comandi di PowerShell seguenti:

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* Questo comando visualizza gli eventi di Windows che mostrano che l'utente ha soddisfatto i requisiti dei criteri di autorizzazione risorse e gli è stato concesso l'accesso.

![Visualizzazione di eventi tramite PowerShell](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* Questo comando visualizza gli eventi che mostrano che l'utente ha soddisfatto i requisiti dei criteri di autorizzazione connessioni.

![Visualizzazione dei criteri di autorizzazione della connessione tramite PowerShell](./media/howto-mfa-nps-extension-rdg/image29.png)

È anche possibile visualizzare questo log e filtrarlo in base agli ID evento 300 e 200. Per eseguire una query per individuare gli eventi di accesso riusciti nei log del visualizzatore eventi di sicurezza, usare il comando seguente:

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* Questo comando può essere eseguito nel server NPS centrale o nel server Gateway Desktop remoto.

![Esempi di eventi di accesso riusciti](./media/howto-mfa-nps-extension-rdg/image30.png)

È anche possibile aprire la visualizzazione personalizzata del log di sicurezza o di Servizi di accesso e criteri di rete, come illustrato di seguito:

![Servizi di accesso e criteri di rete Visualizzatore eventi](./media/howto-mfa-nps-extension-rdg/image31.png)

Nel server in cui è installata l'estensione NPS per Azure MFA è possibile trovare i log applicazioni del Visualizzatore eventi specifici per l'estensione in _Registri applicazioni e servizi\Microsoft\AzureMfa_.

![Visualizzatore eventi log applicazioni AuthZ](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Guida per la risoluzione dei problemi

Se la configurazione non funziona come previsto, è consigliabile iniziare verificando che l'utente sia configurato per usare Azure MFA. Chiedere all'utente di connettersi al [portale di Azure](https://portal.azure.com). Se all'utente viene richiesta l'autenticazione secondaria e può eseguire l'autenticazione, la configurazione di Azure MFA è corretta.

Se Azure MFA funziona correttamente, esaminare i log eventi pertinenti. I log da esaminare sono i log operativi del gateway e quelli relativi a eventi di sicurezza e Azure MFA, illustrati nella sezione precedente.

Di seguito è illustrato un esempio di output di un log di sicurezza che mostra un evento di accesso non riuscito (ID evento 6273).

![Esempio di evento di accesso non riuscito](./media/howto-mfa-nps-extension-rdg/image33.png)

Di seguito è illustrato un evento correlato dei log di Azure MFA:

![Log dell'autenticazione a più fattori di Azure di esempio in Visualizzatore eventi](./media/howto-mfa-nps-extension-rdg/image34.png)

Per opzioni avanzate di risoluzione dei problemi, consultare i file di log in formato database di Server dei criteri di rete nella posizione in cui è installato il servizio Server dei criteri di rete. Questi file di log vengono creati nella cartella _%SystemRoot%\System32\Logs_ come file di testo con valori delimitati da virgole.

Per una descrizione di questi file di log, vedere [Interpret NPS Database Format Log Files](https://technet.microsoft.com/library/cc771748.aspx) (Interpretare i file di log in formato database di Server dei criteri di rete). Le voci di questi file di log sono difficili da interpretare senza importarle in un foglio di calcolo o in un database. Online sono disponibili diversi parser IAS che possono aiutare a interpretare i file di log.

L'immagine seguente mostra l'output di una di queste [applicazioni shareware](https://www.deepsoftware.com/iasviewer) che è possibile scaricare.

![Analizzatore IAS app Shareware di esempio](./media/howto-mfa-nps-extension-rdg/image35.png)

Infine, per altre opzioni di risoluzione dei problemi, è possibile usare uno strumento di analisi di protocolli, ad esempio [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx).

L'immagine seguente di Microsoft Message Analyzer mostra il traffico di rete filtrato in base al protocollo RADIUS e che contiene il nome utente **Contoso\AliceC**.

![Analizzatore messaggi di Microsoft che mostra il traffico filtrato](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Passaggi successivi

[Come ottenere Azure Multi-Factor Authentication](concept-mfa-licensing.md)

[Gateway Desktop remoto e server Azure Multi-Factor Authentication utilizzando RADIUS](howto-mfaserver-nps-rdg.md)

[Integrare le directory locali con Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
