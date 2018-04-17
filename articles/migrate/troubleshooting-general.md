---
title: Risolvere problemi relativi ad Azure Migrate | Microsoft Docs
description: Questo articolo offre una panoramica dei problemi noti relativi al servizio Azure Migrate e alcuni suggerimenti per la risoluzione degli errori comuni.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: troubleshooting
ms.date: 03/19/2018
ms.author: raynew
ms.openlocfilehash: b2c89a980411cac02f46bc91d53620bc94fa845b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshoot-azure-migrate"></a>Risolvere i problemi relativi ad Azure Migrate

## <a name="troubleshoot-common-errors"></a>Risoluzione dei problemi comuni

[Azure Migrate](migrate-overview.md) valuta i carichi di lavoro locali per la migrazione ad Azure. Attenersi a questo articolo per risolvere i problemi che possono verificarsi durante la distribuzione e l'utilizzo di Azure Migrate.


**L'agente di raccolta non è in grado di connettersi a Internet**

Ciò può verificarsi quando il computer in uso è protetto da un proxy. Assicurarsi di fornire le credenziali di autorizzazione, se richiesto dal proxy.
Se si usa qualsiasi proxy firewall basato su URL per controllare la connettività in uscita, assicurarsi di inserire nell'elenco dei consentiti questi URL richiesti:

**URL** | **Scopo**  
--- | ---
*.portal.azure.com | Necessario per controllare la connettività al servizio di Azure e convalidare i problemi relativi alla sincronizzazione dell'ora.
*.oneget.org | Necessario per il download del modulo vCenter PowerCLI basato su PowerShell.

**L'agente di raccolta non riesce a connettersi al progetto usando l'ID progetto e la chiave copiata dal portale.**

Verificare di aver copiato e incollato le informazioni corrette. Per risolvere il problema, installare Microsoft Monitoring Agent (MMA) e verificare se è in grado di connettersi al progetto come descritto di seguito:

1. Scaricare [MMA](https://go.microsoft.com/fwlink/?LinkId=828603) nella macchina virtuale dell'agente di raccolta.
2. Per avviare l'installazione, fare doppio clic sul file scaricato.
3. Nella pagina di **benvenuto** della procedura di configurazione fare clic su **Avanti**. Nella pagina **Condizioni di licenza** fare clic su **Accetto** per accettare la licenza.
4. In **Cartella di destinazione** mantenere o modificare la cartella di installazione predefinita e quindi fare clic su **Avanti**.
5. In **Opzioni di installazione dell'agente** selezionare **Azure Log Analytics (OMS)** > **Avanti**.
6. Fare clic su **Aggiungi** per aggiungere una nuova area di lavoro di Log Analytics. Incollare l'ID progetto e la chiave copiata in precedenza. Quindi fare clic su **Next**.
7. Verificare che l'agente riesca a connettersi al progetto. In caso contrario, verificare le impostazioni. Se l'agente riesce a connettersi, ma non l'agente di raccolta, contattare il supporto tecnico.


**Errore 802: viene visualizzato un errore di sincronizzazione di data e ora.**

È possibile che l'orologio del server abbia perso la sincronizzazione con l'ora corrente da più di cinque minuti. Modificare l'ora nella macchina virtuale dell'agente di raccolta in modo che corrisponda all'ora corrente, come illustrato di seguito:

1. Nella macchina virtuale aprire un prompt dei comandi come amministratore.
2. Per controllare il fuso orario, eseguire w32tm /tz.
3. Per sincronizzare l'ora, eseguire w32tm /resync.

**Nella parte finale della chiave di progetto sono presenti simboli "= =", codificati in altri caratteri alfanumerici dall'agente di raccolta. È normale?**

Sì, ogni chiave di progetto termina con "= =". L'agente di raccolta esegue la crittografia della chiave di progetto prima di elaborarla.

**I dati sulle prestazioni relative ai dischi e alle schede di rete sono pari a zero** 

Questo problema può verificarsi se il livello delle impostazioni relative alle statistiche nel server vCenter è impostato su un valore inferiore a tre. Se impostato sul livello tre o superiore, vCenter archivia la cronologia delle prestazioni della macchina virtuale per scopi di calcolo, archiviazione e rete. Se impostato su un livello inferiore a tre, vCenter non archivia dati di archiviazione e di rete ma solo dati relativi alla CPU e alla memoria. In questo scenario, i dati sulle prestazioni risultano pari a zero in Azure Migrate, che fornisce indicazioni sulle dimensioni di dischi e reti in base ai metadati raccolti dalle macchine locali.

Per abilitare la raccolta dei dati sulle prestazioni di dischi e reti, impostare il livello relativo alle impostazioni delle statistiche su tre. Attendere quindi almeno un giorno per identificare l'ambiente e valutarlo.

**Ho installato gli agenti e usato la visualizzazione delle dipendenze per creare gruppi. Ora, in seguito a un failover, sulle macchine viene visualizzata l'azione "Installa agente" anziché "Visualizza dipendenze"**
* In seguito a un failover pianificato o non pianificato, le macchine locali vengono disattivate e le macchine equivalenti vengono riattivate in Azure. Queste macchine acquisiscono un indirizzo MAC diverso ed eventualmente anche un indirizzo IP diverso, in base alla scelta dell'utente di mantenere o meno l'indirizzo IP locale. Se gli indirizzi IP e MAC sono diversi, Azure Migrate non associa le macchine locali ai dati sulle dipendenze di Elenco dei servizi e chiede all'utente di installare gli agenti anziché visualizzare le dipendenze.
* In seguito al failover di test, le macchine locali rimangono attivate come previsto. Le macchine equivalenti riattivate in Azure acquisiscono un indirizzo MAC diverso ed eventualmente anche un indirizzo IP diverso. Se l'utente non blocca il traffico di OMS in uscita da queste macchine, Azure Migrate non associa le macchine locali ai dati sulle dipendenze di Elenco dei servizi e chiede all'utente di installare gli agenti anziché visualizzare le dipendenze.


## <a name="troubleshoot-readiness-issues"></a>Risolvere problemi di idoneità

**Problema** | **Correzione**
--- | ---
Il tipo di avvio non è supportato | Azure non supporta le macchine virtuali con tipo di avvio EFI. È consigliabile convertire il tipo di avvio in BIOS prima di eseguire una migrazione. <br/><br/>Per eseguire la migrazione di queste macchine virtuali è possibile usare [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-migrate-on-premises-to-azure), che converte il tipo di avvio della macchina virtuale in BIOS durante la migrazione.
Sistema operativo Windows supportato in modo condizionale | Il sistema operativo ha superato la data di fine del supporto e necessita di un contratto di supporto personalizzato per usufruire del [supporto in Azure](https://aka.ms/WSosstatement). Provare ad aggiornare il sistema operativo prima di eseguire la migrazione ad Azure.
Sistema operativo Windows non supportato | Azure supporta solo [determinate versioni del sistema operativo Windows](https://aka.ms/WSosstatement). Provare ad aggiornare il sistema operativo della macchina virtuale prima di eseguire la migrazione ad Azure.
Sistema operativo Linux con approvazione condizionale | Azure approva solo [determinate versioni del sistema operativo Linux](../virtual-machines/linux/endorsed-distros.md). Provare ad aggiornare il sistema operativo della macchina virtuale prima di eseguire la migrazione ad Azure.
Sistema operativo Linux non approvato | La macchina virtuale può essere avviata in Azure, ma Azure non fornisce alcun supporto del sistema operativo. Provare ad aggiornare il sistema operativo a una [versione di Linux approvata](../virtual-machines/linux/endorsed-distros.md) prima di eseguire la migrazione ad Azure.
Sistema operativo sconosciuto | Il sistema operativo della macchina virtuale è stato specificato come "Altro" nel server vCenter, pertanto Azure Migrate non è in grado di identificare l'idoneità per Azure della macchina virtuale. Verificare che il sistema operativo in esecuzione nella macchina sia [supportato](https://aka.ms/azureoslist) da Azure prima di eseguire la migrazione della macchina.
Numero di bit del sistema operativo non supportati | Le macchine virtuali con sistema operativo a 32 bit possono essere avviate in Azure, ma è consigliabile aggiornare il sistema operativo della macchina virtuale da 32 bit a 64 bit prima di eseguire la migrazione ad Azure.
Richiede una sottoscrizione di Visual Studio. | Nelle macchine virtuali è in esecuzione un sistema operativo client Windows supportato solo nella sottoscrizione di Visual Studio.
Non è stata trovata alcuna macchina virtuale per le prestazioni di archiviazione richieste. | Le prestazioni di archiviazione (IOPS/velocità effettiva) richieste per la macchina superano le prestazioni supportate dalla macchina virtuale di Azure. Prima di eseguire la migrazione, ridurre i requisiti di archiviazione per la macchina.
Non è stata trovata alcuna macchina virtuale per le prestazioni di rete richieste. | Le prestazioni di rete (ingresso/uscita) richieste per la macchina superano le prestazioni supportate dalla macchina virtuale di Azure. Ridurre i requisiti di rete per la macchina.
Non è stata trovata alcuna macchina virtuale nel piano tariffario specificato. | Se il piano tariffario è impostato su Standard, è consigliabile ridurre le dimensioni della macchina virtuale prima di eseguire la migrazione ad Azure. Se il piano tariffario è impostato su Base, è consigliabile cambiare il piano tariffario della valutazione su Standard.
La macchina virtuale non è stata trovata nella località specificata. | Usare una località di destinazione diversa prima di eseguire migrazione.
Uno o più dischi non idonei. | Uno o più dischi collegati alla macchina virtuale non soddisfano i requisiti di Azure. Per ogni disco collegato alla macchina virtuale, verificare che le dimensioni disco siano < 4 TB; in caso contrario, ridurre le dimensioni disco prima di eseguire la migrazione ad Azure. Assicurarsi che le prestazioni necessarie per ogni disco (IOPS/velocità effettiva) siano supportata dai [dischi gestiti della macchina virtuale](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits) di Azure.   
Una o più schede di rete non idonee. | Prima di eseguire la migrazione, rimuovere le schede di rete non usate dalla macchina.
Il numero di dischi supera il limite | Rimuovere i dischi non usati dalla macchina prima di eseguire la migrazione.
Le dimensioni del disco hanno superato il limite | Azure supporta dischi di dimensioni fino a 4 TB. Ridurre le dimensioni del disco a meno di 4 TB prima di eseguire la migrazione.
Il disco non è disponibile nella località specificata | Assicurarsi che il disco si trovi nella località di destinazione prima di eseguire la migrazione.
Il disco non è disponibile per la ridondanza specificata | Il disco deve usare il tipo di archiviazione di ridondanza definito nelle impostazioni di valutazione (per impostazione predefinita, LRS).
Non è stato possibile determinare l'idoneità del disco a causa di un errore interno | Provare a creare una nuova valutazione per il gruppo.
La macchina virtuale con core e memoria richiesti non è stata trovata | Azure non può trovare un tipo di macchina virtuale idonea. Prima di eseguire la migrazione, quindi, ridurre la memoria e il numero di core della macchina locale.
Non è stato possibile determinare l'idoneità della macchina virtuale a causa di un errore interno. | Provare a creare una nuova valutazione per il gruppo.
Non è stato possibile determinare l'idoneità di uno o più dischi a causa di un errore interno. | Provare a creare una nuova valutazione per il gruppo.
Non è stato possibile determinare l'idoneità di una o più schede di rete a causa di un errore interno. | Provare a creare una nuova valutazione per il gruppo.


## <a name="collect-logs"></a>Raccogliere i log

**Come è possibile raccogliere i log nella macchina virtuale dell'agente di raccolta?**

La funzionalità di creazione di log è abilitata per impostazione predefinita. I log si trovano nei percorsi seguenti:

- C:\Profiler\ProfilerEngineDB.sqlite
- C:\Profiler\Service.log
- C:\Profiler\WebApp.log

Per raccogliere Event Trace for Windows, seguire questa procedura:

1. Nella macchina virtuale dell'agente di raccolta aprire una finestra di comando di PowerShell.
2. Eseguire **Get-EventLog -LogName Application | export-csv eventlog.csv**.

**Come è possibile raccogliere i log relativi al traffico di rete del portale?**

1. Aprire il browser e accedere al [portale](https://portal.azure.com).
2. Premere F12 per avviare Strumenti di sviluppo. Se necessario, deselezionare l'impostazione **Cancella voci durante l'esplorazione**.
3. Fare clic sulla scheda **Rete** e avviare l'acquisizione del traffico di rete:
 - In Chrome selezionare **Preserve log** (Mantieni log). La registrazione viene avviata automaticamente. Un cerchio rosso indica che è in corso l'acquisizione del traffico. Se non viene visualizzato, fare clic sul cerchio nero per avviarla.
 - Anche in Microsoft Edge/Internet Explorer la registrazione viene avviata automaticamente. In caso contrario, fare clic sul pulsante di esecuzione verde.
4. Provare a riprodurre l'errore.
5. Dopo aver riscontrato l'errore durante la registrazione, arrestare la registrazione e salvare una copia dell'attività registrata:
 - In Chrome fare clic con il pulsante destro del mouse su **Save as HAR with content** (Salva come HAR con contenuto). In questo modo i log vengono compressi ed esportati come file con estensione har.
 - In Microsoft Edge/Internet Explorer fare clic sull'icona **Esporta traffico catturato**. In questo modo il log viene compresso ed esportato.
6. Passare alla scheda **Console** per verificare la presenza di eventuali avvisi o errori. Per salvare il log della console:
 - In Chrome fare clic con il pulsante destro del mouse in un punto qualsiasi del log della console. Selezionare **Salva con nome** per esportare e comprimere il log.
 - In Microsoft Edge/Internet Explorer fare clic con il pulsante destro del mouse sugli errori e selezionare **Copia tutto**.
7. Chiudere Strumenti di sviluppo.


## <a name="vcenter-errors"></a>Errori di vCenter

### <a name="error-unhandledexception-internal-error-occured-systemiofilenotfoundexception"></a>Si è verificato l'errore interno UnhandledException: System.IO.FileNotFoundException

Si tratta di un problema che si è verificato nelle versioni dell'agente di raccolta precedenti alla 1.0.9.5. Se si usa la versione dell'agente di raccolta 1.0.9.2 o le versioni precedenti a GA quali 1.0.8.59, si riscontrerà questo problema. Seguire il [collegamento ai forum specificato qui per avere una risposta dettagliata](https://social.msdn.microsoft.com/Forums/azure/en-US/c1f59456-7ba1-45e7-9d96-bae18112fb52/azure-migrate-connect-to-vcenter-server-error?forum=AzureMigrate).

[Aggiornare l'agente di raccolta per risolvere il problema](https://aka.ms/migrate/col/checkforupdates).

### <a name="error-unabletoconnecttoserver"></a>Errore UnableToConnectToServer

Impossibile connettersi al server vCenter "Servername.com:9443" a causa dell'errore: There was no endpoint listening at https://Servername.com:9443/sdk that could accept the message. (Nessun endpoint in ascolto su https://Servername.com:9443/sdk in grado di accettare il messaggio).

Questo errore si verifica quando il computer dell'agente di raccolta non è in grado di risolvere il nome del server vCenter specificato o la porta specificata non è corretta. Per impostazione predefinita, se la porta non è specificata, l'agente di raccolta tenterà di connettersi alla porta numero 443.

1. Provare a effettuare il ping di Servername.com dal computer dell'agente di raccolta.
2. Se il passaggio 1 ha esito negativo, provare a connettersi al server vCenter sull'indirizzo IP.
3. Identificare il numero di porta corretto per connettersi al server vCenter.
4. Infine controllare che il server vCenter sia attivo e in esecuzione.

## <a name="collector-error-codes-and-recommended-actions"></a>Codici di errore dell'agente di raccolta e azioni consigliate

|           |                                |                                                                               |                                                                                                       |                                                                                                                                            | 
|-----------|--------------------------------|-------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------| 
| Codice di errore | Nome errore                      | Message                                                                       | Possibili cause                                                                                        | Azione consigliata                                                                                                                          | 
| 601       | CollectorExpired               | L'agente di raccolta è scaduto.                                                        | Agente di raccolta scaduto.                                                                                    | Scaricare una nuova versione dell'agente di raccolta e riprovare.                                                                                      | 
| 751       | UnableToConnectToServer        | Non è possibile connettersi al server vCenter "%Name;" a causa dell'errore: %ErrorMessage;     | Per altre informazioni, controllare il messaggio di errore.                                                             | Risolvere il problema e riprovare.                                                                                                           | 
| 752       | InvalidvCenterEndpoint         | Il server "%Name;" non è un server vCenter.                                  | Specificare i dettagli del server vCenter.                                                                       | Ritentare l'operazione con i dettagli del server vCenter corretti.                                                                                   | 
| 753       | InvalidLoginCredentials        | Non è possibile connettersi al server vCenter "%Name;" a causa dell'errore: %ErrorMessage; | La connessione al server vCenter non è riuscita a causa delle credenziali di accesso non valide.                             | Verificare che le credenziali di accesso specificate siano corrette.                                                                                    | 
| 754       | NoPerfDataAvaialable           | Non sono disponibili dati sulle prestazioni.                                               | Controllare il livello di statistiche nel server vCenter. Perché i dati sulle prestazioni siano disponibili, questo deve essere impostato su 3. | Modificare il livello di statistiche a 3 (per una durata di 5 minuti, 30 minuti e 2 ore) e riprovare dopo avere atteso almeno un giorno.                   | 
| 756       | NullInstanceUUID               | È stato rilevato un computer con valore InstanceUUID null                                  | Il server vCenter può presentare un oggetto non idoneo.                                                      | Risolvere il problema e riprovare.                                                                                                           | 
| 757       | VMNotFound                     | Impossibile trovare la macchina virtuale                                                  | La macchina virtuale potrebbe essere stata eliminata: %VMID;                                                                | Verificare che le macchine virtuali selezionate nella definizione dell'ambito dell'inventario vCenter esistano durante l'individuazione                                      | 
| 758       | GetPerfDataTimeout             | Timeout della richiesta vCenter. Messaggio %Message;                                  | Le credenziali del server vCenter non sono corrette                                                              | Controllare le credenziali del server vCenter e assicurarsi che questo sia raggiungibile. Ripetere l'operazione. Se il problema persiste, contattare il supporto tecnico. | 
| 759       | VmwareDllNotFound              | DLL VMWare.Vim non trovata.                                                     | PowerCLI non è installato correttamente.                                                                   | Accertarsi che PowerCLI sia installato correttamente. Ripetere l'operazione. Se il problema persiste, contattare il supporto tecnico.                               | 
| 800       | ServiceError                   | Il servizio Agente di raccolta di Azure Migrate non è in esecuzione.                               | Il servizio Agente di raccolta di Azure Migrate non è in esecuzione.                                                       | Usare il comando services.msc per avviare il servizio e ripetere l'operazione.                                                                             | 
| 801       | PowerCLIError                  | L'installazione di VMware PowerCLI non è riuscita.                                          | L'installazione di VMware PowerCLI non è riuscita.                                                                  | Ripetere l'operazione. Se il problema persiste, installarlo manualmente e ritentare l'operazione.                                                   | 
| 802       | TimeSyncError                  | L'ora non è sincronizzata con il server di riferimento ora Internet.                            | L'ora non è sincronizzata con il server di riferimento ora Internet.                                                    | Verificare che l'ora del computer sia impostata in modo accurato per il fuso orario del computer e ripetere l'operazione.                                 | 
| 702       | OMSInvalidProjectKey           | È stata specificata una chiave del progetto non valida.                                                | È stata specificata una chiave del progetto non valida.                                                                        | Ripetere l'operazione con la chiave del progetto corretta.                                                                                              | 
| 703       | OMSHttpRequestException        | Si è verificato un errore durante l'invio della richiesta. Messaggio %Message;                                | Controllare l'ID e la chiave del progetto e assicurarsi che l'endpoint sia raggiungibile.                                       | Ripetere l'operazione. Se il problema persiste, contattare il supporto tecnico Microsoft.                                                                     | 
| 704       | OMSHttpRequestTimeoutException | Timeout della richiesta HTTP. Messaggio %Message;                                     | Controllare l'ID e la chiave del progetto e assicurarsi che l'endpoint sia raggiungibile.                                       | Ripetere l'operazione. Se il problema persiste, contattare il supporto tecnico Microsoft.                                                                     | 
