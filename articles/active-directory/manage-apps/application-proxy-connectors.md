---
title: Comprendere i connettori del proxy applicazione Azure AD | Microsoft Docs
description: Tratta i fondamenti dei connettori del proxy applicazione di Azure AD.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: c4666a9d084f6fc12cd68b69d5c71cafc3de0439
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807787"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Comprendere i connettori del proxy applicazione Azure AD

I connettori sono ciò che rende possibile il proxy di applicazione di Azure AD. Sono semplici, estremamente potenti e facile da distribuire e gestire. Questo articolo illustra i connettori, il loro funzionamento e alcune procedure consigliate per ottimizzare al meglio la distribuzione.

## <a name="what-is-an-application-proxy-connector"></a>Informazioni su un connettore proxy di applicazione

I connettori sono agenti semplici che si trovano a livello locale e facilitano la connessione in uscita al servizio proxy di applicazione. I connettori devono essere installati su un server Windows dotato di accesso all'applicazione back-end. È possibile organizzare i connettori in gruppi di connettori, con ogni gruppo che gestisce il traffico per applicazioni specifiche.

## <a name="requirements-and-deployment"></a>Requisiti e distribuzione

Per distribuire correttamente il proxy di applicazione, è necessario almeno un connettore, ma sono consigliati due o più connettori per assicurare maggiore resilienza. Installare il connettore in un computer che esegue Windows Server 2012 R2 o versione successiva. Il connettore deve poter comunicare con il servizio proxy di applicazione e con le applicazioni locali che vengono pubblicate.

### <a name="windows-server"></a>Windows Server
È necessario un server che esegue Windows Server 2012 R2 o versione successiva in cui poter installare il connettore del proxy applicazione. Questo server deve connettersi ai servizi proxy di applicazione in Azure e alle applicazioni locali che vengono pubblicate.

È necessario abilitare TLS 1.2 per Windows Server prima di installare il connettore del proxy di applicazione. Per abilitare TLS 1.2 nel server:

1. Impostare le chiavi del Registro di sistema seguenti:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Riavviare il server

Per ulteriori informazioni sui requisiti di rete per il server del connettore, vedere [Introduzione al proxy dell'applicazione e installazione di un connettore](application-proxy-add-on-premises-application.md).

## <a name="maintenance"></a>Manutenzione

I connettori e il servizio si occupano di tutte le attività che richiedono disponibilità elevata. Possono essere aggiunti o rimossi in modo dinamico. Ogni volta che arriva una nuova richiesta, questa viene indirizzata a uno dei connettori attualmente disponibili. Se un connettore è temporaneamente non disponibile, non risponde a questo traffico.

I connettori sono senza stato e senza dati di configurazione nel computer. Gli unici dati che archiviano sono le impostazioni per la connessione del servizio e il relativo certificato di autenticazione. Quando si connettono al servizio, eseguono il pull di tutti i dati di configurazione necessari e li aggiornano ogni due minuti.

I connettori eseguono inoltre il polling del server per verificare se è disponibile una versione più recente del connettore. Se ne viene individuata una, i connettori vengono aggiornati.

È possibile monitorare i connettori dal computer in cui vengono eseguiti usando il registro eventi e i contatori delle prestazioni. In alternativa è possibile vedere lo stato dalla pagina del proxy di applicazione del portale di Azure:

![Esempio: Connettori Proxy di applicazione AD Azure](./media/application-proxy-connectors/app-proxy-connectors.png)

Non è necessario eliminare manualmente i connettori che non vengono usati. Quando un connettore è in esecuzione, rimane attivo quando si connette al servizio. I connettori inutilizzati vengono contrassegnati come _inattivi_ e vengono rimossi dopo 10 giorni di inattività. Per disinstallare un connettore, disinstallare sia il servizio connettore che il servizio di aggiornamento dal server. Riavviare il computer per rimuovere completamente il servizio.

## <a name="automatic-updates"></a>Aggiornamenti automatici

Azure AD offre aggiornamenti automatici per tutti i connettori da distribuire. Fino a quando il servizio di aggiornamento del connettore proxy di applicazione è in esecuzione, i connettori vengono aggiornati automaticamente. Se non viene visualizzato il servizio di aggiornamento del connettore nel server, è necessario [reinstallare il connettore](application-proxy-add-on-premises-application.md) per ottenere gli aggiornamenti.

Se non si vuole attendere l'aggiornamento automatico del connettore, è possibile eseguire un aggiornamento manuale. Passare alla [pagina di download del connettore](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) nel server in cui si trova il connettore e selezionare **Download**. Grazie a questo processo viene avviato un aggiornamento del connettore locale.

In caso di tenant con più connettori, gli aggiornamenti automatici vengono applicati a un connettore per volta in ogni gruppo, al fine di evitare tempo di inattività nell'ambiente in uso.

Potrebbero verificarsi tempi di inattività quando viene aggiornato il connettore se:
  
- Si ha un solo connettore. È consigliabile installare un secondo connettore e [creare un gruppo di connettori](application-proxy-connector-groups.md): in questo modo è possibile evitare periodi di inattività e assicurare una disponibilità più elevata.  
- Un connettore si trovava nel mezzo di una transazione quando è iniziato l'aggiornamento. Anche se la transazione iniziale viene persa, il browser dovrebbe ripetere automaticamente l'operazione. In caso contrario, è possibile aggiornare la pagina. Quando la richiesta viene inviata di nuovo, il traffico viene indirizzato a un connettore di backup.

Per informazioni sulle versioni rilasciate in precedenza e le modifiche incluse, vedere [Cronologia delle versioni del proxy di applicazione](application-proxy-release-version-history.md).

## <a name="creating-connector-groups"></a>Creazione di gruppi di connettori

I gruppi di connettori consentono di assegnare connettori specifici per gestire applicazioni specifiche. È possibile raggruppare una serie di connettori e quindi assegnare ogni applicazione a un gruppo.

I gruppi di connettori rendono più semplice gestire le distribuzioni di grandi dimensioni. Migliorano inoltre la latenza per i tenant che dispongono di applicazioni ospitate in diverse aree geografiche, poiché è possibile creare gruppi di connettori basati sulla posizione per gestire solo le applicazioni in locale.

Per altre informazioni sui gruppi di connettori, vedere [Pubblicare applicazioni in reti e posizioni separate tramite i gruppi di connettori](application-proxy-connector-groups.md).

## <a name="capacity-planning"></a>Pianificazione della capacità

È importante assicurarsi di aver pianificato la capacità sufficiente tra i connettori per gestire il volume di traffico previsto. È consigliabile che ogni gruppo di connettori abbia almeno due connettori per assicurare scalabilità e disponibilità elevata. Con tre connettori è ottimale nel caso in cui potrebbe essere necessario un computer in qualsiasi punto del servizio.

In generale, più utenti si hanno, più sarà grande il computer necessario. Di seguito è una tabella fornisce una descrizione del volume e latenza prevista può gestire computer diversi. Tutto è basato sulle transazioni al secondo (TPS) previste e non sugli utenti, poiché i modelli di utilizzo variano e non possono essere usati per stimare il carico. Esistono inoltre alcune differenze basate sulla dimensione delle risposte e sul tempo di risposta dell'applicazione back-end. Dimensioni di risposta più grandi e tempi di risposta più lenti comporteranno un numero massimo di TPS inferiore. È anche consigliabile avere macchine aggiuntive in modo che il carico distribuito tra il computer fornisce sempre ampiamente buffer. La capacità aggiuntiva che si ottiene garantisce elevati livelli di disponibilità e resilienza.

|Core|RAM|Latenza prevista (MS)-P99|Numero massimo di TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|

\* Il computer usato un'impostazione personalizzata per la generazione di alcuni dei limiti di connessione predefinita di là .NET impostazioni consigliate. È consigliabile eseguire un test con le impostazioni predefinite prima di contattare il supporto per ottenere la modifica di questo limite per il tenant specifico.

> [!NOTE]
> Non c'è molta differenza nel numero massimo di TPS tra computer Core 4, 8 e 16. La differenza principale è la latenza prevista.  

## <a name="security-and-networking"></a>Sicurezza e rete

I connettori possono essere installati in qualsiasi punto della rete che consenta loro di inviare richieste al servizio proxy dell'applicazione. È importante che il computer che esegue il connettore abbia anche accesso alle app. È possibile installare i connettori all'interno della rete aziendale o in una macchina virtuale che viene eseguita nel cloud. I connettori possono eseguiti all'interno di una rete perimetrale, nota anche come un perimetrale (DMZ), ma non è necessario perché tutto il traffico in uscita in modo che la rete è protetta.

I connettori inviano le richieste soltanto in uscita. Il traffico in uscita viene inviato al servizio proxy applicazione e alle applicazioni pubblicate. Non è necessario aprire porte in ingresso perché il traffico scorre in entrambe le direzioni, dopo aver stabilito una sessione. Non è inoltre necessario configurare l'accesso in ingresso attraverso firewall.

Per maggiori informazioni sulla configurazione delle regole del firewall in uscita, vedere [Usare server proxy locali esistenti](application-proxy-configure-connectors-with-proxy-servers.md).

## <a name="performance-and-scalability"></a>Prestazioni e scalabilità

Anche se la scalabilità per il servizio proxy di applicazione è trasparente, è comunque un fattore per i connettori. È necessario disporre di connettori sufficienti per gestire il traffico di picco. Poiché i connettori sono senza stato, non vengono influenzati dal numero di utenti o sessioni. Dipendono invece dal numero di richieste e dalle dimensioni del payload. In un traffico Web standard, un computer medio può gestire circa duemila richieste al secondo. La capacità specifica dipende dalle esatte caratteristiche del computer.

Le prestazioni del connettore sono legate alla CPU e alla rete. Le prestazioni della CPU sono necessarie per la crittografia SSL e la decrittografia, mentre la rete è fondamentale per una connettività veloce alle applicazioni e al servizio online in Azure.

La memoria, al contrario, ha meno importanza per i connettori. Il servizio online si occupa di gran parte dell'elaborazione e di tutto il traffico non autenticato. Tutto ciò che può essere fatto nel cloud viene fatto nel cloud.

Se per qualsiasi motivo il connettore o il computer non sono più disponibili, il traffico inizierà ad andare su un altro connettore del gruppo. Questa resilienza esiste anche perché si consiglia di avere più connettori.

Un altro fattore che influenza le prestazioni è la qualità della connessione di rete tra i connettori, inclusi:

- **Il servizio online**: Connessioni lente o a elevata latenza al servizio proxy di applicazione in Azure influenzano le prestazioni del connettore. Per ottenere prestazioni ottimali, connettere l'organizzazione ad Azure con Express Route. In caso contrario, assicurarsi che il team di rete garantisca una gestione il più possibile efficiente delle connessioni ad Azure.
- **Applicazioni back-end**: In alcuni casi ci sono altri proxy tra il connettore e le applicazioni back-end che possono rallentare o impedire la connessione. Per risolvere questo scenario, aprire un browser dal server del connettore e tentare di accedere all'applicazione. Se si eseguono i connettori in Azure, ma le applicazioni sono locali, l'esperienza degli utenti potrebbe essere diversa da quella prevista.
- **I controller di dominio**: Se i connettori eseguono l'accesso Single Sign-On (SSO) mediante la delega vincolata Kerberos, essi contattano i controller di dominio prima di inviare la richiesta al back-end. I connettori hanno una cache dei ticket Kerberos, ma in ambienti affollati la velocità di risposta dei controller di dominio può influenzare le prestazioni. Questa situazione è più comune per i connettori eseguiti in Azure, ma che comunicano con i controller di dominio locali.

Per maggiori informazioni sull'ottimizzazione della rete, vedere [Considerazioni relative alla topologia di rete quando si usa il proxy di applicazione di Azure Active Directory](application-proxy-network-topology.md).

## <a name="domain-joining"></a>Aggiunta al dominio

I connettori possono essere eseguiti in un computer che non fa parte del dominio. È necessario tuttavia un computer appartenente al dominio se si sceglie di implementare un accesso Single Sign-On (SSO) per le applicazioni che usano l'autenticazione integrata di Windows (IWA). In questo caso i computer di connessione devono appartenere a un dominio che può eseguire la delega vincolata [Kerberos](https://web.mit.edu/kerberos) per conto degli utenti per le applicazioni pubblicate.

I connettori possono anche essere aggiunti a domini o foreste con attendibilità parziale o a controller di dominio di sola lettura.

## <a name="connector-deployments-on-hardened-environments"></a>Distribuzione dei connettori in ambienti protetti

Nella maggior parte dei casi la distribuzione dei connettori è molto semplice e non richiede una configurazione speciale. Esistono tuttavia alcune condizioni specifiche che devono essere considerate:

- Le organizzazioni che limitano il traffico in uscita devono [aprire le porte necessarie](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).
- Per i computer conformi FIPS potrebbe essere necessario modificare la configurazione per consentire ai processi connettore di generare e archiviare un certificato.
- Le organizzazioni, che bloccano il proprio ambiente in base ai processi che inviano le richieste di rete, devono assicurarsi che siano abilitati entrambi i servizi connettore per accedere a tutte le porte e agli indirizzi IP necessari.
- In alcuni casi il proxy di inoltro in uscita può interrompere l'autenticazione bidirezionale con certificato e non consentire la comunicazione.

## <a name="connector-authentication"></a>Autenticazione del connettore

Per garantire un servizio sicuro, i connettori devono eseguire l'autenticazione verso il servizio e il servizio deve eseguire l'autenticazione verso il connettore. Questa autenticazione viene eseguita usando i certificati client e server quando i connettori avviano la connessione. In questo modo il nome utente e la password dell'amministratore non sono archiviati sul computer di connessione.

I certificati utilizzati sono specifici per il servizio proxy applicazione. Vengono creati durante la registrazione iniziale e rinnovati automaticamente dai connettori ogni due mesi.

Se un connettore non viene connesso al servizio per molti mesi, i relativi certificati potrebbero non essere più aggiornati. In questo caso, disinstallare e reinstallare il connettore per attivare la registrazione. È possibile eseguire i seguenti comandi di PowerShell:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

## <a name="under-the-hood"></a>Dietro le quinte

I connettori sono basati su proxy di applicazione Web di Windows Server, per cui condividono la maggior parte degli strumenti di gestione, inclusi i log eventi di Windows

![Gestire i log eventi con il Visualizzatore eventi](./media/application-proxy-connectors/event-view-window.png)

e con i contatori delle prestazioni di Windows.

![Aggiungere contatori al connettore con Performance Monitor](./media/application-proxy-connectors/performance-monitor.png)

I connettori hanno sia log di amministrazione che log di sessione. I log di amministrazione includono gli eventi principali e i relativi errori. I log di sessione includono tutte le transazioni e i relativi dettagli di elaborazione.

Per visualizzare i log, passare al Visualizzatore eventi, aprire il menu **Visualizza** e abilitare **Visualizza registri analitici e di debug**. È necessario abilitarli per avviare la raccolta degli eventi. Questi log non appaiono nel proxy applicazione Web in Windows Server 2012 R2, in quanto i connettori sono basati su una versione più recente.

È possibile esaminare lo stato del servizio nella finestra Servizi. Il connettore è costituito da due servizi di Windows, ovvero il connettore stesso e il programma di aggiornamento. Entrambi devono essere eseguiti costantemente.

 ![Esempio: Finestra con servizi di Azure Active Directory locale di servizi](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Passaggi successivi

- [Pubblicare applicazioni in reti e posizioni separate tramite i gruppi di connettori](application-proxy-connector-groups.md)
- [Usare server proxy locali esistenti](application-proxy-configure-connectors-with-proxy-servers.md)
- [Risolvere i problemi di errore del proxy di applicazione e del connettore](application-proxy-troubleshoot.md)
- [Come eseguire un'installazione invisibile all'utente del connettore del proxy di applicazione di Azure AD](application-proxy-register-connector-powershell.md)
