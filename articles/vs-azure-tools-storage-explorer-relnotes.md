---
title: Note sulla versione di Microsoft Azure Storage Explorer
description: Note sulla versione di Microsoft Azure Storage Explorer
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2018
ms.author: cawa
ms.openlocfilehash: 59415941172fab06b3e86ef4d34d464cf359ce8f
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025424"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Note sulla versione di Microsoft Azure Storage Explorer

Questo articolo contiene le note sulla versione di Azure Storage Explorer 1.2.0, nonché sulle versioni precedenti.

[Microsoft Azure Storage Explorer](./vs-azure-tools-storage-manage-with-storage-explorer.md) è un'app autonoma che consente di usare facilmente dati di Archiviazione di Azure in Windows, macOS e Linux.

## <a name="version-120"></a>Versione 1.2.0
06/12/2018

### <a name="download-azure-storage-explorer-120"></a>Scaricare Azure Storage Explorer 1.2.0
- [Azure Storage Explorer 1.2.0 per Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Azure Storage Explorer 1.2.0 per Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage Explorer 1.2.0 per Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Nuovo
* Se Storage Explorer non riesce a caricare le sottoscrizioni da un solo subset dei tenant, eventuali sottoscrizioni caricate correttamente verranno visualizzate insieme a un messaggio di errore specifico per i tenant che hanno dato esito negativo. [#159](https://github.com/Microsoft/AzureStorageExplorer/issues/159)
* In Windows, quando un aggiornamento è disponibile, è ora possibile eseguire l'aggiornamento alla chiusura. Quando si seleziona questa opzione, il programma di installazione per l'aggiornamento verrà eseguito dopo la chiusura di Storage Explorer. [#21](https://github.com/Microsoft/AzureStorageExplorer/issues/21)
* È stato aggiunto il ripristino dello snapshot al menu di scelta rapida dell'editor di condivisione file durante la visualizzazione di uno snapshot di condivisione file.[#131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* Il pulsante Cancella coda ora è sempre abilitato.[#135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
* È stato riabilitato il supporto per l'accesso ad AD FS di Azure Stack. Azure Stack 1804 o versione successiva è obbligatorio. [#150](https://github.com/Microsoft/AzureStorageExplorer/issues/150)

### <a name="fixes"></a>Correzioni
* Se si visualizzavano gli snapshot di una condivisione file il cui nome era un prefisso di un'altra condivisione file nello stesso account di archiviazione, venivano elencati anche gli snapshot di altre condivisioni file. Il problema è stato risolto. [#255](https://github.com/Microsoft/AzureStorageExplorer/issues/255)
* Quando si eseguiva il collegamento tramite firma di accesso condiviso, il ripristino di un file da uno snapshot di condivisione file restituiva un errore. Il problema è stato risolto. [#211](https://github.com/Microsoft/AzureStorageExplorer/issues/211)
* Quando si visualizzavano gli snapshot per un BLOB, l'azione di alzare di livello lo snapshot veniva abilitata quando erano selezionati il BLOB di base e un singolo snapshot. L'azione viene ora abilitata solo se è selezionato un singolo snapshot. [#230](https://github.com/Microsoft/AzureStorageExplorer/issues/230)
* Se si avviava un singolo processo (ad esempio il download di un BLOB) che aveva esito negativo, l'operazione non veniva riavviata automaticamente prima dell'avvio di un altro processo dello stesso tipo. Tutti i processi devono ora riavviarsi automaticamente, indipendentemente dal numero di processi in coda.
* Gli editor aperti per i nuovi contenitori BLOB creati in GPV2 e gli account di archiviazione BLOB non disponevano di una colonna di livello di accesso. Il problema è stato risolto. [#109](https://github.com/Microsoft/AzureStorageExplorer/issues/109)
* Una colonna di livello di accesso talvolta non veniva visualizzata quando un account di archiviazione o un contenitore BLOB veniva collegato tramite firma di accesso condiviso. La colonna ora viene sempre visualizzata, ma con un valore vuoto se non è impostato un livello di accesso. [#160](https://github.com/Microsoft/AzureStorageExplorer/issues/160)
* L'impostazione del livello di accesso di un nuovo BLOB in blocchi caricato era disabilitata. Il problema è stato risolto. [#171](https://github.com/Microsoft/AzureStorageExplorer/issues/171)
* Se il pulsante "Mantieni scheda aperta" veniva richiamato tramite tastiera, si perdeva lo stato attivo della tastiera. Lo stato attivo si sposta ora sulla scheda che è stata lasciata aperta. [#163](https://github.com/Microsoft/AzureStorageExplorer/issues/163)
* Per una query eseguita in Generatore di query, VoiceOver non forniva una descrizione utile dell'operatore corrente. La descrizione è ora più completa. [#207](https://github.com/Microsoft/AzureStorageExplorer/issues/207)
* I collegamenti di paginazione per i vari editor non erano descrittivi. Ora contengono maggiori descrizioni. [#205](https://github.com/Microsoft/AzureStorageExplorer/issues/205)
* Nella finestra di dialogo Aggiungi entità, VoiceOver non indicava la colonna di cui faceva parte un elemento di input. Il nome della colonna corrente è ora incluso nella descrizione dell'elemento. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* I pulsanti di opzione e le caselle di controllo non avevano un bordo visibile quando erano attivati. Il problema è stato risolto. [#237](https://github.com/Microsoft/AzureStorageExplorer/issues/237)

### <a name="known-issues"></a>Problemi noti
* Quando si usano gli emulatori, ad esempio Emulatore di archiviazione di Azure o Azurite, è necessario che rimangano in attesa di connessioni sulle porte predefinite. In caso contrario, Storage Explorer non potrà eseguire la connessione degli emulatori.
* Se si usa Visual Studio per Mac e non è mai stata creata una configurazione personalizzata di AAD, potrebbe non essere possibile effettuare l'accesso. Per risolvere il problema, eliminare il contenuto di ~/.IdentityService/AadConfigurations. Se con tale operazione non si viene sbloccati, aggiungere un commento su [questo problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite non ha ancora implementato completamente tutte le API di archiviazione. Per questo motivo, potrebbe esserci errori imprevisti o un comportamento imprevisto quando si usa Azurite per l'archivio di sviluppo.
* In rari casi, lo stato attivo dell'albero può rimanere bloccato in Accesso rapido. Per sbloccare lo stato attivo, è possibile eseguire l’operazione Aggiorna tutto.
* Il caricamento dalla cartella OneDrive non funziona a causa di un bug in NodeJS. Il bug è stato risolto ma non è ancora stato integrato in Electron.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta [qui](https://github.com/Azure/azure-storage-node/issues/317).
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

```
./StorageExplorer.exe --disable-gpu
```

* Per gli utenti Linux, è necessario installare [.NET Core 2.0](https://docs.microsoft.com/en-us/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```








## <a name="previous-releases"></a>Versioni precedenti

* [Versione 1.1.0](#version-110)
* [Versione 1.0.0](#version-100)
* [Versione 0.9.6](#version-096)
* [Versione 0.9.5](#version-095)
* [Versione 0.9.4 e 0.9.3](#version-094-and-093)
* [Versione 0.9.2](#version-092)
* [Versione 0.9.1 e 0.9.0](#version-091-and-090)
* [ 0.8.16](#version-0816)
* [Versione 0.8.14](#version-0814)
* [Versione 0.8.13](#version-0813)
* [Versione 0.8.12, 0.8.11 e 0.8.10](#version-0812-and-0811-and-0810)
* [Versione 0.8.9 e 0.8.8](#version-089-and-088)
* [Versione 0.8.7](#version-087)
* [Versione 0.8.6](#version-086)
* [Versione 0.8.5](#version-085)
* [Versione 0.8.4](#version-084)
* [Versione 0.8.3](#version-083)
* [Versione 0.8.2](#version-082)
* [Versione 0.8.0](#version-080)
* [Versione 0.7.20160509.0](#version-07201605090)
* [Versione 0.7.20160325.0](#version-07201603250)
* [Versione 0.7.20160129.1](#version-07201601291)
* [Versione 0.7.20160105.0](#version-07201601050)
* [Versione 0.7.20151116.0](#version-07201511160)


## <a name="version-110"></a>Versione 1.1.0
05/09/2018

### <a name="new"></a>Nuovo
* Storage Explorer supporta ora l'uso di Azurite. Nota: la connessione ad Azurite è hardcoded per gli endpoint di sviluppo predefiniti.
* Storage Explorer supporta ora i livelli di accesso solo per BLOB e gli account di archiviazione per utilizzo generico v2. Per altre informazioni sui livelli di accesso, vedere [qui](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-storage-tiers).
* L'ora di inizio non è più necessaria per la generazione di una firma di accesso condiviso.

### <a name="fixes"></a>Correzioni
* Il recupero delle sottoscrizioni per gli account US Government viene interrotto. Il problema è stato risolto. [#61](https://github.com/Microsoft/AzureStorageExplorer/issues/61)
* La scadenza per i criteri di accesso non viene salvata correttamente. Il problema è stato risolto. [#50](https://github.com/Microsoft/AzureStorageExplorer/issues/50)
* Quando si genera un URL di firma di accesso condiviso per un elemento in un contenitore, il nome dell'elemento non viene aggiunto all'URL. Il problema è stato risolto. [#44](https://github.com/Microsoft/AzureStorageExplorer/issues/44)
* Quando si crea una firma di accesso condiviso, viene usato come valore predefinito una scadenza già trascorsa. Questo problema era dovuto al fatto che Storage Explorer usava come valori predefiniti l'ultima ora di inizio e l'ultima scadenza usate. Ogni volta che si apre la finestra di dialogo di firma di accesso condiviso, viene generato ora un nuovo set di valori predefiniti. [#35](https://github.com/Microsoft/AzureStorageExplorer/issues/35)
* Quando si eseguono copie tra gli account di archiviazione, viene generata una firma di accesso condiviso di 24 ore. Se dura più di 24 ore la copia non riuscirà. La durata della firma di accesso condiviso è stata aumentata a una settimana per ridurre le probabilità che una copia non riesca a causa della scadenza di una firma di accesso condiviso. [#62](https://github.com/Microsoft/AzureStorageExplorer/issues/62)
* Per alcune attività non sempre funziona fare clic su "Annulla". Il problema è stato risolto. [#125](https://github.com/Microsoft/AzureStorageExplorer/issues/125)
* Per alcune attività la velocità di trasferimento è errata. Il problema è stato risolto. [#124](https://github.com/Microsoft/AzureStorageExplorer/issues/124)
* L'ortografia di "Indietro" nel menu Visualizza è errata. L'ortografia è stata ora corretta. [#71](https://github.com/Microsoft/AzureStorageExplorer/issues/71)
* Nella pagina finale di Windows Installer è presente il pulsante "Avanti". È stato modificato con il pulsante "Fine". [#70](https://github.com/Microsoft/AzureStorageExplorer/issues/70)
* Lo stato attivo delle schede non è visibile per i pulsanti nelle finestre di dialogo quando si usa il tema Nero a contrasto elevato. È ora visibile.[#64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* L'utilizzo di maiuscole e minuscole di "Risoluzione automatica" per le azioni nel log attività non è corretto. È ora corretto. [#51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* Quando si elimina un'entità da una tabella, nella finestra di dialogo in cui viene richiesto di confermare l'operazione viene visualizzata un'icona di errore. Nella finestra di dialogo viene visualizzata ora un'icona di avviso. [#148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>Problemi noti
* Se si usa Visual Studio per Mac e non è mai stata creata una configurazione personalizzata di AAD, potrebbe non essere possibile effettuare l'accesso. Per risolvere il problema, eliminare il contenuto di ~/.IdentityService/AadConfigurations. Se con tale operazione non si viene sbloccati, aggiungere un commento su [questo problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite non ha ancora implementato completamente tutte le API di archiviazione. Per questo motivo, potrebbe esserci errori imprevisti o un comportamento imprevisto quando si usa Azurite per l'archivio di sviluppo.
* In rari casi, lo stato attivo dell'albero può rimanere bloccato in Accesso rapido. Per sbloccare lo stato attivo, è possibile eseguire l’operazione Aggiorna tutto.
* Il caricamento dalla cartella OneDrive non funziona a causa di un bug in NodeJS. Il bug è stato risolto ma non è ancora stato integrato in Electron.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta [qui](https://github.com/Azure/azure-storage-node/issues/317).
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

```
./StorageExplorer.exe --disable-gpu
```

* Per gli utenti Linux, è necessario installare [.NET Core 2.0](https://docs.microsoft.com/en-us/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-100"></a>Versione 1.0.0
04/16/2018

### <a name="new"></a>Nuovo
* Autenticazione avanzata che consente a Storage Explorer di utilizzare lo stesso archivio account di Visual Studio 2017. Per utilizzare questa funzionalità, sarà necessario accedere nuovamente agli account e impostare nuovamente le sottoscrizioni filtrate.
* Per gli account Azure Stack supportati da AAD, Storage Explorer recupererà ora le sottoscrizioni Azure Stack quando "Stack Azure di destinazione" è abilitato. Non è necessario creare un ambiente di accesso personalizzato.
* Alcuni tasti di scelta rapida sono stati aggiunti per consentire una navigazione più veloce. Sono inclusi diversi pannelli di attivazione/disattivazione e lo spostamento tra gli editor. Vedere il menu Visualizza per altri dettagli.
* I feedback di Storage Explorer ora si basano su GitHub. È possibile raggiungere la pagina dei problemi facendo clic sul pulsante di feedback nella parte inferiore sinistra o andando su [https://github.com/Microsoft/AzureStorageExplorer/issues](https://github.com/Microsoft/AzureStorageExplorer/issues). È possibile fornire suggerimenti, segnalare un problema, porre domande o lasciare qualsiasi altra forma di feedback.
* Se si sono verificati problemi relativi ai certificati SSL ed è impossibile trovare il certificato che causa l'errore, è possibile avviare Storage Explorer dalla riga di comando con il flag `--ignore-certificate-errors`. Quando viene avviato con questo flag, Storage Explorer ignorerà gli errori del certificato SSL.
* È ora disponibile l'opzione "Scarica" nel menu di scelta rapida per gli elementi di BLOB e di file.
* Accessibilità e supporto di lettura dello schermo migliorati. Se ci si basa sulle funzionalità di accessibilità, vedere la nostra [documentazione di accessibilità](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-accessibility) per ulteriori informazioni.
* Storage Explorer usa ora Electron 1.8.3

### <a name="breaking-changes"></a>Modifiche di rilievo
* Storage Explorer è diventato una nuova libreria di autenticazione. A seguito del passaggio alla libreria, sarà necessario accedere nuovamente agli account e impostare nuovamente le sottoscrizioni filtrate
* Il metodo utilizzato per crittografare i dati sensibili è stato modificato. Questo può comportare che alcuni elementi di Accesso rapido devono essere nuovamente aggiunti e/o alcune delle risorse associate devono essere ricollegate.

### <a name="fixes"></a>Correzioni
* Alcuni utenti dietro i proxy hanno raggruppato caricamenti o download di BLOB a interrotti da un messaggio di errore "Impossibile risolvere". Il problema è stato risolto.
* Se durante l'uso di un collegamento diretto era necessario eseguire l'accesso, facendo clic sul prompt di accesso veniva visualizzata una finestra di dialogo vuota. Il problema è stato risolto.
* In Linux, se Storage Explorer non è in grado di avviarsi a causa di un arresto anomalo del processo GPU, si riceveranno informazioni sull'arresto anomalo, verrà indicato come utilizzare lo switch "-disable-gpu"e Storage Explorer verrà riavviato automaticamente con l'opzione abilitata.
* Era difficile l’identificazione di criteri di accesso non validi nella finestra di dialogo Criteri di accesso. Gli iD dei criteri di accesso non validi sono ora indicati in rosso per una maggiore visibilità.
* Il registro attività aveva talvolta vaste porzioni di spazi vuoti tra le diverse parti di un'attività. Il problema è stato risolto.
* Nell'editor di query di tabella, se vi fosse stata una clausola timestamp in uno stato non valido e quindi si fosse tentato di modificare un'altra clausola, l'editor si sarebbe bloccato. L'editor ripristinerà la clausola timestamp all’ultimo stato valido quando viene rilevata una modifica in un'altra clausola.
* Se ci si fosse arrestati durante la digitazione nella query di ricerca nella visualizzazione struttura ad albero, la ricerca sarebbe iniziata e lo stato attivo sarebbe stato rubato dalla casella di testo. A questo punto, avviare esplicitamente la ricerca, premendo "invio" o facendo clic sul pulsante per avviare la ricerca.
* Il comando "Ottieni firma di accesso condiviso" talvolta era disattivato quando si faceva clic con il tasto destro del mouse su un file in una condivisione file. Il problema è stato risolto.
* Se il nodo della struttura albero delle risorse con lo stato attivo fosse stato filtrato durante la ricerca, sarebbe stato impossibile aggiungere una tabulazione nell’albero delle risorse e usare i tasti di direzione per esplorare l'albero delle risorse. A questo punto, se il nodo dell'albero delle risorse con lo stato attivo è nascosto, il primo nodo nell'albero delle risorse verrà automaticamente attivato.
* Un separatore aggiuntivo era visibile in alcuni casi nella barra degli strumenti dell'editor. Il problema è stato risolto.
* Talvolta, la casella di testo di navigazione era in eccesso. Il problema è stato risolto.
* Gli editor BLOB e di condivisione file erano talvolta aggiornati costantemente durante il caricamento di molti file in una sola volta. Il problema è stato risolto.
* La funzionalità "Statistiche cartella" non aveva alcun ruolo nella visualizzazione di gestione snapshot di condivisione file. Ora è stata disabilitata.
* In Linux, il menu File non è stato visualizzato. Il problema è stato risolto.
* Durante il caricamento di una cartella in una condivisione file, per impostazione predefinita, venivano caricati solo i contenuti della cartella. A questo punto, il comportamento predefinito è caricare il contenuto della cartella in una cartella corrispondente nella condivisione file.
* L'ordinamento dei pulsanti in alcune finestre di dialogo era stato invertito. Il problema è stato risolto.
* Varie correzioni relative alla sicurezza.

### <a name="known-issues"></a>Problemi noti
* In rari casi, lo stato attivo dell'albero può rimanere bloccato in Accesso rapido. Per sbloccare lo stato attivo, è possibile eseguire l’operazione Aggiorna tutto.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta di seguito.
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

```
./StorageExplorer.exe --disable-gpu
```

* Per gli utenti Linux, è necessario installare [.NET Core 2.0](https://docs.microsoft.com/en-us/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-096"></a>Versione 0.9.6
28/02/2018

### <a name="fixes"></a>Correzioni
* Un problema impediva che i BLOB/file previsti fossero elencati nell'editor. Il problema è stato risolto.
* Un problema causava il passaggio da una visualizzazione snapshot a un'altra per visualizzare gli elementi in modo non corretto. Il problema è stato risolto.

### <a name="known-issues"></a>Problemi noti
* Azure Storage Explorer non supporta gli account ADFS.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta [qui](https://github.com/Azure/azure-storage-node/issues/317).
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* Il pannello delle impostazioni dell'account potrebbe indicare che è necessario immettere nuovamente le credenziali per filtrare le sottoscrizioni.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

```
./StorageExplorer.exe --disable-gpu
```

* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-095"></a>Version 0.9.5
02/06/2018

### <a name="new"></a>Nuovo

* Supporto per gli snapshot delle condivisioni file:
    * Creare e gestire snapshot per le condivisioni file.
    * Cambiare le viste degli snapshot delle condivisioni file durante l'esplorazione.
    * Ripristinare le versioni precedenti dei file.
* Supporto dell'anteprima per Archivio Azure Data Lake:
    * Connettersi alle risorse di Archivio Azure Data Lake tra più account.
    * Connettersi e condividere le risorse di Archivio Azure Data Lake tramite gli URI ADL.
    * Eseguire operazioni di base su file/cartella in modo ricorsivo.
    * Aggiungere singole cartelle all'Accesso rapido.
    * Visualizzare le statistiche della cartella.

### <a name="fixes"></a>Correzioni
* Miglioramenti delle prestazioni di avvio.
* Varie correzioni di bug.

### <a name="known-issues"></a>Problemi noti
* Azure Storage Explorer non supporta gli account ADFS.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta di seguito.
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* Il pannello delle impostazioni dell'account potrebbe indicare che è necessario immettere nuovamente le credenziali per filtrare le sottoscrizioni.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

```
./StorageExplorer.exe --disable-gpu
```

* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>Versione 0.9.4 e 0.9.3
21/01/2018

### <a name="new"></a>Nuovo
* La finestra Storage Explorer esistente verrà nuovamente usata nei casi seguenti:
    * Apertura di collegamenti diretti generati in Storage Explorer.
    * Apertura di Storage Explorer dal portale.
    * Apertura di Storage Explorer dall'estensione Azure Storage VS Code (disponibile a breve).
* Funzionalità aggiuntiva per aprire una nuova finestra di Storage Explorer da Storage Explorer.
    * Per Windows, è disponibile l'opzione "Nuova finestra" nel menu File e nel menu di scelta rapida della barra delle applicazioni.
    * Per Mac, è disponibile l'opzione "Nuova finestra" nel Menu app.

### <a name="fixes"></a>Correzioni
* Correzione di un problema di sicurezza. Passare alla versione 0.9.4 il prima possibile.
* La pulizia delle attività precedenti non è stata eseguita in modo appropriato. Ciò ha avuto un impatto sulle prestazioni dei processi con esecuzione prolungata. La pulizia è stata ora eseguita correttamente.
* Le azioni che interessano un numero elevato di file e directory potrebbero occasionalmente causare il blocco di Storage Explorer. Le richieste in Azure per le condivisioni file sono ora limitate per ridurre l'uso delle risorse di sistema.

### <a name="known-issues"></a>Problemi noti
* Azure Storage Explorer non supporta gli account ADFS.
* I tasti di scelta rapida per "Visualizza Explorer" e "Visualizza Gestione account" sono CTRL/CMD+ MAIUSC+E e CTRL/CMD+MAIUSC+A rispettivamente.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta di seguito.
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* Il pannello delle impostazioni dell'account potrebbe indicare che è necessario immettere nuovamente le credenziali per filtrare le sottoscrizioni.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:
```
./StorageExplorer --disable-gpu
```
* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>Versione 0.9.2
11/01/2017

### <a name="hotfixes"></a>Hotfix
* Erano possibili modifiche impreviste dei dati durante la modifica dei valori Edm.DateTime per le entità di tabella in base al fuso orario locale. Ora l'editor usa una casella di testo normale, fornendo un controllo preciso e coerente sui valori Edm.DateTime.
* Il caricamento o il download di un gruppo di BLOB collegato con un nome e una chiave non veniva avviato. Il problema è stato risolto.
* In precedenza Storage Explorer richiedeva di ripetere l'autenticazione di un account non aggiornato solo se venivano selezionate una o più sottoscrizioni dell'account. Ora Storage Explorer visualizza la richiesta anche se l'account è completamente filtrato.
* Il dominio degli endpoint per Azure per enti pubblici statunitensi era errato. Questo problema è stato risolto.
* Talvolta era difficile fare clic sul pulsante Applica nel pannello Gestisci account. Il problema non dovrebbe più verificarsi.

### <a name="new"></a>Nuovo
* Anteprima del supporto per Azure Cosmos DB:
    * [Documentazione online](./cosmos-db/storage-explorer.md)
    * Creare database e raccolte
    * Manipolare i dati
    * Eseguire query, creare ed eliminare documenti
    * Aggiornare stored procedure, funzioni definite dall'utente o trigger
    * Usare le stringhe di connessione per connettersi e gestire i database
* Miglioramento delle prestazioni del caricamento/download di molti BLOB di piccole dimensioni.
* Aggiunta di un'azione "Riprova tutto" se sono presenti errori in un gruppo di BLOB da caricare o scaricare.
* Azure Storage Explorer ora sospenderà l'iterazione durante il caricamento o download di BLOB se rileva che la connessione di rete si è interrotta. Quando la connessione di rete viene ristabilita, è possibile riprendere l'iterazione.
* Aggiunta delle funzionalità "Chiudi tutto", "Chiudi altre" e "Chiudi" per le schede tramite i menu di scelta rapida.
* Azure Storage Explorer ora usa le finestre di dialogo native e i menu di contesto nativi.
* Azure Storage Explorer ora è più accessibile. I miglioramenti includono:
    * Supporto migliorato per i lettori di schermo, per NVDA in Windows e VoiceOver su Mac
    * Miglioramento dei temi a contrasto elevato
    * Correzione di errori di spostamento con il tasto Tab e di spostamento dello stato attivo con la tastiera

### <a name="fixes"></a>Correzioni
* Se si tenta di aprire o scaricare un BLOB con un nome di file non valido per Windows, l'operazione non riesce. Azure Storage Explorer ora rileva se un nome di BLOB non è valido e chiede se si desidera codificarlo o ignorare il BLOB. Azure Storage Explorer rileva anche se un nome di file sembra essere codificato e chiede se si vuole decodificarlo prima del caricamento.
* Durante il caricamento di BLOB, l'editor per il contenitore di BLOB di destinazione potrebbe talvolta non aggiornarsi correttamente. Il problema è stato risolto.
* Il supporto per varie forme di stringhe di connessione e URI SAS è regredito. Sono stati risolti tutti i problemi noti, ma si prega di inviare commenti se si riscontrano ulteriori problemi.
* Le notifiche degli aggiornamenti non venivano mostrate ad alcuni utenti della versione 0.9.0. Questo problema è stato risolto e gli utenti interessati dal bug possono scaricare manualmente la versione più recente di Azure Storage Explorer [qui](https://azure.microsoft.com/features/storage-explorer/).

### <a name="known-issues"></a>Problemi noti
* Azure Storage Explorer non supporta gli account ADFS.
* I tasti di scelta rapida per "Visualizza Explorer" e "Visualizza Gestione account" sono CTRL/CMD+ MAIUSC+E e CTRL/CMD+MAIUSC+A rispettivamente.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta di seguito.
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* Il pannello delle impostazioni dell'account potrebbe indicare che è necessario immettere nuovamente le credenziali per filtrare le sottoscrizioni.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:
```
./StorageExplorer --disable-gpu
```
* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>Versione 0.9.1 e 0.9.0
10/20/2017
### <a name="new"></a>Nuovo
* Anteprima del supporto per Azure Cosmos DB:
    * [Documentazione online](./cosmos-db/storage-explorer.md)
    * Creare database e raccolte
    * Manipolare i dati
    * Eseguire query, creare ed eliminare documenti
    * Aggiornare stored procedure, funzioni definite dall'utente o trigger
    * Usare le stringhe di connessione per connettersi e gestire i database
* Miglioramento delle prestazioni del caricamento/download di molti BLOB di piccole dimensioni.
* Aggiunta di un'azione "Riprova tutto" se sono presenti errori in un gruppo di BLOB da caricare o scaricare.
* Azure Storage Explorer ora sospenderà l'iterazione durante il caricamento o download di BLOB se rileva che la connessione di rete si è interrotta. Quando la connessione di rete viene ristabilita, è possibile riprendere l'iterazione.
* Aggiunta delle funzionalità "Chiudi tutto", "Chiudi altre" e "Chiudi" per le schede tramite i menu di scelta rapida.
* Azure Storage Explorer ora usa le finestre di dialogo native e i menu di contesto nativi.
* Azure Storage Explorer ora è più accessibile. I miglioramenti includono:
    * Supporto migliorato per i lettori di schermo, per NVDA in Windows e VoiceOver su Mac
    * Miglioramento dei temi a contrasto elevato
    * Correzione di errori di spostamento con il tasto Tab e di spostamento dello stato attivo con la tastiera

### <a name="fixes"></a>Correzioni
* Se si tenta di aprire o scaricare un BLOB con un nome di file non valido per Windows, l'operazione non riesce. Azure Storage Explorer ora rileva se un nome di BLOB non è valido e chiede se si desidera codificarlo o ignorare il BLOB. Azure Storage Explorer rileva anche se un nome di file sembra essere codificato e chiede se si vuole decodificarlo prima del caricamento.
* Durante il caricamento di BLOB, l'editor per il contenitore di BLOB di destinazione potrebbe talvolta non aggiornarsi correttamente. Il problema è stato risolto.
* Il supporto per varie forme di stringhe di connessione e URI SAS è regredito. Sono stati risolti tutti i problemi noti, ma si prega di inviare commenti se si riscontrano ulteriori problemi.
* Le notifiche degli aggiornamenti non venivano mostrate ad alcuni utenti della versione 0.9.0. Questo problema è stato risolto e gli utenti interessati dal bug possono scaricare manualmente la versione più recente di Azure Storage Explorer [qui](https://azure.microsoft.com/features/storage-explorer/)

### <a name="known-issues"></a>Problemi noti
* Azure Storage Explorer non supporta gli account ADFS.
* I tasti di scelta rapida per "Visualizza Explorer" e "Visualizza Gestione account" sono CTRL/CMD+ MAIUSC+E e CTRL/CMD+MAIUSC+A rispettivamente.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta di seguito.
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* Il pannello delle impostazioni dell'account potrebbe indicare che è necessario immettere nuovamente le credenziali per filtrare le sottoscrizioni.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:
```
./StorageExplorer --disable-gpu
```
* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>Versione 0.8.16
21/08/2017

### <a name="new"></a>Nuovo
* Quando si apre un BLOB, Storage Explorer richiederà di caricare il file scaricato se viene rilevata una modifica
* Esperienza di accesso ad Azure Stack migliorata
* Miglioramento delle prestazioni per il caricamento/download contemporanei di molti file di piccole dimensioni


### <a name="fixes"></a>Correzioni
* Per alcuni tipi di BLOB, la scelta della sostituzione in presenza di un conflitto di caricamento talvolta comporta il riavvio del caricamento.
* Nella versione 0.8.15 i caricamenti rimangono talvolta bloccati al 99%.
* Durante il caricamento di file in una condivisione file, se si sceglie di caricare in una directory non ancora esistente, il caricamento ha esito negativo.
* Storage Explorer generava timestamp non corretti per le firme di accesso condiviso e le query di tabella.


### <a name="known-issues"></a>Problemi noti
* L'uso di una stringa di connessione con nome e chiave attualmente non funziona. Questo problema verrà risolto nella prossima versione. Fino ad allora è possibile collegarsi con nome e chiave.
* Se si tenta di aprire un file con un nome di file di Windows non valido, il download genera un errore di file non trovato.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Si tratta di un limite della libreria di nodi di Archiviazione di Azure.
* Dopo aver completato il caricamento di un blob, viene aggiornata la scheda che ha avviato il caricamento. Questa è una modifica rispetto al comportamento precedente. Verrà inoltre visualizzata nuovamente la radice del contenitore in cui ci si trova.
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* Il pannello delle impostazioni dell'account potrebbe indicare che è necessario immettere nuovamente le credenziali per filtrare le sottoscrizioni.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>Versione 0.8.14
22/06/2017

### <a name="new"></a>Nuovo

* Versione aggiornata di Electron a 1.7.2 per usufruire di numerosi aggiornamenti importanti sulla sicurezza
* È ora possibile accedere rapidamente alla guida online di risoluzione dei problemi dal menu Guida
* [Guida][2]alla risoluzione dei problemi di Storage Explorer
* [Istruzioni][3]per stabilire la connessione a una sottoscrizione di Azure Stack

### <a name="known-issues"></a>Problemi noti

* Su Linux, i pulsanti nella finestra di conferma Elimina cartella non vengono registrati con i clic del mouse. Una soluzione alternativa consiste nell'usare il tasto Invio
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale selezione
* Il caricamento simultaneo di più di 3 gruppi di BLOB o di file può causare errori
* Il pannello delle impostazioni dell'account potrebbe indicare che è necessario immettere nuovamente le credenziali per filtrare le sottoscrizioni
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* Ubuntu 14.04 richiede l'aggiornamento della versione gcc. La procedura per effettuare l'aggiornamento è riportata qui sotto:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>Versione 0.8.13
12/05/2017

#### <a name="new"></a>Nuovo

* [Guida][2]alla risoluzione dei problemi di Storage Explorer
* [Istruzioni][3]per stabilire la connessione a una sottoscrizione di Azure Stack

#### <a name="fixes"></a>Correzioni

* Corretto: il caricamento del file aveva una probabilità elevata di causare un errore di memoria insufficiente
* Corretto: è ora possibile accedere con PIN/smart card
* Corretto: l'azione Apri nel portale ora funziona con Azure Cina, Azure Germania, Azure per enti pubblici statunitensi e Azure Stack
* Corretto: durante il caricamento di una cartella in un contenitore BLOB, talvolta si verificava un errore "Operazione non valida"
* Corretto: selezionare tutto ciò che è stato disabilitato durante la gestione di snapshot
* Corretto: i metadati del BLOB di base avrebbero potuto venire sovrascritti dopo aver visualizzato le proprietà dei relativi snapshot

#### <a name="known-issues"></a>Problemi noti

* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale selezione
* Mentre viene eseguito lo zoom avanti o indietro, il livello di zoom può momentaneamente ripristinare il livello predefinito
* Il caricamento simultaneo di più di 3 gruppi di BLOB o di file può causare errori
* Il pannello delle impostazioni dell'account potrebbe indicare che è necessario immettere nuovamente le credenziali per filtrare le sottoscrizioni
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* Ubuntu 14.04 richiede l'aggiornamento della versione gcc. La procedura per effettuare l'aggiornamento è riportata qui sotto:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>Versione 0.8.12, 0.8.11 e 0.8.10
07/04/2017

#### <a name="new"></a>Nuovo

* Storage Explorer si chiude automaticamente quando si installa un aggiornamento dalla notifica di aggiornamento
* L'accesso rapido sul posto fornisce un'esperienza ottimizzata per l'uso con le risorse a cui si accede di frequente
* Nell'editor del contenitore BLOB, è ora possibile visualizzare a quale macchina virtuale appartiene un BLOB dedicato
* È ora possibile comprimere il pannello di sinistra
* L'azione Individuazione ora viene eseguito contemporaneamente al download
* Usare Statistiche negli editor Contenitore BLOB, Condivisione File e Tabelle per visualizzare le dimensioni della risorsa o selezione
* È ora possibile accedere agli account di Azure Stack basati su Azure Active Directory (AAD).
* È ora possibile caricare file di archivio superiori ai 32 MB per gli account di archiviazione Premium
* Supporto di accessibilità migliorato
* È ora possibile aggiungere certificati SSL attendibili X.509 codificati Base-64 andando su Modifica -&gt; Certificati SSL -&gt; Importa certificati

#### <a name="fixes"></a>Correzioni

* Corretto: dopo l'aggiornamento delle credenziali dell'account, la visualizzazione albero talvolta non veniva aggiornata automaticamente
* Corretto: la generazione di una firma di accesso condiviso per le code e le tabelle dell'emulatore dà origine a un URL non valido
* Corretto: gli account di archiviazione premium possono ora essere espansi mentre è abilitato un proxy
* Corretto: il pulsante Applica nella pagina di gestione degli account non funziona se si aveva selezionato 1 o 0 account
* Corretto: il caricamento di BLOB che richiedono le risoluzioni di conflitti potrebbe non andare a buon termine - corretto in 0.8.11
* Corretto: l'invio di commenti e suggerimenti veniva interrotto nella versione 0.8.11 - corretto in 0.8.12

#### <a name="known-issues"></a>Problemi noti

* Dopo l'aggiornamento a 0.8.10, è necessario aggiornare tutte le credenziali.
* Mentre viene eseguito lo zoom avanti o indietro, il livello di zoom può momentaneamente ripristinare il livello predefinito.
* Il caricamento simultaneo di più di 3 gruppi di BLOB o di file può causare errori.
* Il pannello delle impostazioni dell'account potrebbe indicare che è necessario immettere nuovamente le credenziali per filtrare le sottoscrizioni.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* Ubuntu 14.04 richiede l'aggiornamento della versione gcc. La procedura per effettuare l'aggiornamento è riportata qui sotto:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089-and-088"></a>Versione 0.8.9 e 0.8.8
23/02/2017

>[!VIDEO https://www.youtube.com/embed/R6gonK3cYAc?ecver=1]

>[!VIDEO https://www.youtube.com/embed/SrRPCm94mfE?ecver=1]


#### <a name="new"></a>Nuovo

* Storage Explorer 0.8.9 scaricherà automaticamente la versione più recente degli aggiornamenti.
* Hotfix: l'uso di un URI di firma di accesso condiviso generato da portale per collegare un account di archiviazione comporta un errore.
* È ora possibile creare, gestire e alzare di livello degli snapshot di BLOB.
* È ora possibile accedere agli account di Azure Cina, Azure Germania e Azure per enti pubblici statunitensi.
* È ora possibile modificare il livello di zoom. Usare le opzioni nel menu Visualizza per Zoom avanti, Zoom indietro e Reimposta zoom.
* I caratteri Unicode sono ora supportati nei metadati dell'utente per file e BLOB.
* Miglioramenti all'accessibilità.
* Le note sulla versione successiva possono essere visualizzate dalla notifica di aggiornamento. È inoltre possibile visualizzare le note sulla versione attuale dal menu Guida.

#### <a name="fixes"></a>Correzioni

* Corretto: il numero di versione viene ora visualizzato correttamente nel Pannello di controllo in Windows
* Corretto: la ricerca non è più limitata a 50.000 nodi
* Corretto: caricamento predefinito in una condivisione di file se non esiste già una directory di destinazione
* Corretto: maggiore stabilità per processi di caricamento e download prolungati

#### <a name="known-issues"></a>Problemi noti

* Mentre viene eseguito lo zoom avanti o indietro, il livello di zoom può momentaneamente ripristinare il livello predefinito.
* Accesso rapido funziona solo con gli elementi basati su sottoscrizione. Questa versione non supporta le risorse locali e le risorse collegate tramite chiave o token di firma di accesso condiviso.
* Accesso rapido può impiegare alcuni secondi per passare alla risorsa di destinazione, a seconda del numero di risorse presenti.
* Il caricamento simultaneo di più di 3 gruppi di BLOB o di file può causare errori.

16/12/2016
### <a name="version-087"></a>Versione 0.8.7

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>Nuovo

* È possibile scegliere come risolvere i conflitti all'inizio di una sessione di aggiornamento, download o copia nella finestra Attività
* È possibile passare il mouse su una scheda per visualizzare il percorso completo della risorsa di archiviazione
* Quando si fa clic su una scheda, si sincronizza con il relativo percorso nel riquadro di spostamento a sinistra

#### <a name="fixes"></a>Correzioni

* Corretto: Storage Explorer è ora un'app attendibile su Mac
* Corretto: Ubuntu 14.04 è supportato nuovamente
* Corretto: a volte l'interfaccia utente Aggiungi account lampeggiava durante il caricamento delle sottoscrizioni
* Corretto: a volte non tutte le risorse di archiviazione venivano elencate nel riquadro di spostamento a sinistra
* Corretto: a volte il riquadro azioni visualizzava azioni vuote
* Corretto: le dimensioni della finestra dell'ultima sessione chiusa vengono ora mantenute
* Corretto: è possibile aprire più schede per la stessa risorsa usando il menu di scelta rapida

#### <a name="known-issues"></a>Problemi noti

* Accesso rapido funziona solo con gli elementi basati su sottoscrizione. Questa versione non supporta le risorse locali e le risorse collegate tramite chiave o token di firma di accesso condiviso
* Accesso rapido può impiegare alcuni secondi per passare alla risorsa di destinazione, a seconda del numero di risorse presenti
* Il caricamento simultaneo di più di 3 gruppi di BLOB o di file può causare errori
* È possibile eseguire la ricerca in un massimo di 50.000 nodi circa. Se si supera tale limite, le prestazioni potrebbero risultare peggiorate o potrebbero essere generate eccezioni non gestite
* La prima volta che si usa Storage Explorer in macOS potrebbero essere visualizzate diverse richieste di autorizzazione dell'utente per l'accesso a Keychain. È consigliabile selezionare Consenti sempre per non visualizzare più la richiesta di conferma

18/11/2016
### <a name="version-086"></a>Versione 0.8.6

#### <a name="new"></a>Nuovo

* Possibilità di aggiungere ad Accesso rapido i servizi usati più di frequente per semplificare la navigazione
* Possibilità di aprire più editor in schede diverse. Clic singolo per aprire una scheda temporanea. Doppio clic per aprire una scheda permanente. È anche possibile fare clic sulla scheda temporanea per renderla permanente
* Sono stati apportati miglioramenti evidenti a prestazioni e stabilità per operazioni di caricamento e download, soprattutto per file di grandi dimensioni in computer veloci
* Possibilità di creare cartelle vuote "virtuali" in contenitori BLOB
* È stato reintrodotto l'ambito di ricerca con la nuova ricerca avanzata con sottostringa, pertanto ora vi sono due opzioni di ricerca:
    * Ricerca globale: è sufficiente immettere solo un termine di ricerca nella casella di testo di ricerca
    * Ricerca con ambito: fare clic sull'icona della lente di ingrandimento accanto a un nodo, quindi aggiungere un termine di ricerca alla fine del percorso o fare clic con il pulsante destro del mouse e selezionare "Cerca da qui"
* Sono stati aggiunti vari temi: Chiaro (impostazione predefinita), Scuro, Nero a contrasto elevato e Bianco a contrasto elevato. Scegliere Modifica -&gt; Temi per modificare le preferenze dei temi
* È possibile modificare le proprietà di BLOB e file
* Sono ora supportati i messaggi in coda codificati (base64) e non codificati
* In Linux è ora necessario un sistema operativo a 64 bit. Per questa versione è supportato solo Ubuntu 16.04.1 LTS a 64 bit
* È stato aggiornato il logo

#### <a name="fixes"></a>Correzioni

* Corretto: problemi di blocco della schermata
* Corretto: sicurezza avanzata
* Corretto: a volte venivano visualizzati account collegati doppi
* Corretto: un blob con un tipo di contenuto non definito poteva generare un'eccezione
* Corretto: non era possibile aprire il pannello Query in una tabella vuota
* Corretto: bug delle variabili in Cerca
* Corretto: il numero delle risorse che è possibile caricare tramite "Carica altro" è stato aumentato da 50 a 100
* Corretto: alla prima esecuzione, se si è effettuato l'accesso a un account, vengono ora selezionate tutte le sottoscrizioni per tale account per impostazione predefinita

#### <a name="known-issues"></a>Problemi noti

* Questa versione di Storage Explorer non può essere eseguita in Ubuntu 14.04
* Per aprire più schede per la stessa risorsa, non fare clic in modo continuo sulla stessa risorsa. Fare clic su un'altra risorsa, tornare indietro e quindi fare clic sulla risorsa originale per aprirla nuovamente in un'altra scheda
* Accesso rapido funziona solo con gli elementi basati su sottoscrizione. Questa versione non supporta le risorse locali e le risorse collegate tramite chiave o token di firma di accesso condiviso
* Accesso rapido può impiegare alcuni secondi per passare alla risorsa di destinazione, a seconda del numero di risorse presenti
* Il caricamento simultaneo di più di 3 gruppi di BLOB o di file può causare errori
* È possibile eseguire la ricerca in un massimo di 50.000 nodi circa. Se si supera tale limite, le prestazioni potrebbero risultare peggiorate o potrebbero essere generate eccezioni non gestite

03/10/2016
### <a name="version-085"></a>Versione 0.8.5

#### <a name="new"></a>Nuovo

* È ora possibile usare le chiavi di firma di accesso condiviso generate dal portale per collegarsi all'account di archiviazione e alle risorse

#### <a name="fixes"></a>Correzioni

* Corretto: a volte la race condition durante la ricerca faceva sì che i nodi diventassero non espandibili
* Corretto: "Usa HTTP" non funziona quando ci si connette agli account di archiviazione con il nome account e la chiave
* Corretto: le chiavi di firma di accesso condiviso (specialmente quelle generati da portale) restituiscono un errore "barra finale"
* Corretto: problemi di importazione della tabella
    * In alcuni casi la chiave di partizione e la chiave di riga venivano annullate
    * Impossibile leggere le chiavi di partizione "null"

#### <a name="known-issues"></a>Problemi noti

* È possibile eseguire la ricerca in un massimo di 50.000 nodi circa. Se si supera tale limite, le prestazioni potrebbero risultare peggiorate
* Azure Stack attualmente non supporta i file, quindi il tentativo di espandere File genera un errore

12/09/2016
### <a name="version-084"></a>Versione 0.8.4

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>Nuovo

* Generazione di collegamenti diretti ad account di archiviazione, contenitori, code, tabelle o condivisioni file per accedere facilmente alle risorse e condividerle - Supporto Windows e Mac OS
* Ricerca di contenitori BLOB, tabelle, code, condivisioni file o account di archiviazione dalla casella di ricerca
* È ora possibile raggruppare le clausole nel generatore di query della tabella
* Rinominare e copiare/incollare contenitori BLOB, condivisioni file, tabelle, BLOB, cartelle di BLOB, file e directory all'interno di contenitori e account collegati alla firma di accesso condiviso
* Quando vengono ridenominati e copiati contenitori BLOB e condivisioni file, proprietà e metadati vengono mantenuti

#### <a name="fixes"></a>Correzioni

* Corretto: impossibile modificare le voci della tabella se contengono le proprietà di tipo booleano o binario

#### <a name="known-issues"></a>Problemi noti

* È possibile eseguire la ricerca in un massimo di 50.000 nodi circa. Se si supera tale limite, le prestazioni potrebbero risultare peggiorate

03/08/2016
### <a name="version-083"></a>Versione 0.8.3

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>Nuovo

* Ridenominazione di contenitori, tabelle e condivisioni file
* Migliore esperienza con il Generatore di query
* Possibilità di salvare e caricare le query
* Collegamenti diretti ad account di archiviazione o contenitori, code, tabelle o condivisioni file per accedere facilmente alle risorse e condividerle (solo su Windows, supporto per macOS presto disponibile)
* Possibilità di gestire e configurare regole CORS

#### <a name="fixes"></a>Correzioni

* Corretto: gli account di Microsoft richiedono la riautenticazione ogni 8-12 ore

#### <a name="known-issues"></a>Problemi noti

* In alcuni casi l'interfaccia utente potrebbe sembrare bloccata. Per risolvere il problema, ingrandire la finestra
* L'installazione di macOS potrebbe richiedere autorizzazioni elevate
* Il pannello delle impostazioni dell'account potrebbe indicare che è necessario immettere nuovamente le credenziali per filtrare le sottoscrizioni
* Quando si ridenominano condivisioni file, contenitori BLOB e tabelle non vengono conservati i metadati o altre proprietà nel contenitore, ad esempio la quota di condivisione file, il livello di accesso pubblico o i criteri di accesso
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione
* La copia o la ridenominazione delle risorse non funziona all'interno di account associati alla firma di accesso condiviso

07/07/2016
### <a name="version-082"></a>Versione 0.8.2

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Nuovo

* Gli account di archiviazione vengono raggruppati in sottoscrizioni. L'archiviazione e le risorse di sviluppo collegate tramite chiave o firma di accesso condiviso vengono visualizzate nel nodo (locale e collegato)
* Disconnettersi dagli account nel pannello "Impostazioni account Azure"
* Configurare le impostazioni proxy per abilitare e gestire l'accesso
* Creare e interrompere i lease di blob
* Aprire contenitori BLOB, code, tabelle e i file con un unico clic

#### <a name="fixes"></a>Correzioni

* Corretto: i messaggi in coda con le librerie .NET o Java non vengono decodificati correttamente da base64
* Corretto: le tabelle $metrics non vengono visualizzate per gli account di archiviazione Blob
* Corretto: il nodo delle tabelle non funziona per l'archiviazione locale (sviluppo)

#### <a name="known-issues"></a>Problemi noti

* L'installazione di macOS potrebbe richiedere autorizzazioni elevate

15/06/2016
### <a name="version-080"></a>Versione 0.8.0

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>Nuovo

* Supporto per condivisione file: visualizzazione, caricamento, download, copia di file e directory, URI della firma di accesso condiviso (creazione e la connessione)
* Esperienza utente migliorata in termini di connessione a Storage con chiavi di account o URI della firma di accesso condiviso
* Risultati della query relativa alla tabella di esportazione
* Riordinamento e personalizzazione delle colonne della tabella
* Visualizzazione dei contenitori BLOB $logs e delle tabelle $metrics per gli account di archiviazione con le metriche abilitate
* Comportamento di esportazione e importazione migliorato, include ora il tipo di valore della proprietà

#### <a name="fixes"></a>Correzioni

* Corretto: il caricamento o il download di BLOB di grandi dimensioni può essere incompleto
* Corretto: la modifica, l'aggiunta o l'importazione di un'entità con un valore numerico ("1") ne comporta il raddoppiamento
* Corretto: impossibile espandere il nodo della tabella nell'ambiente di sviluppo locale

#### <a name="known-issues"></a>Problemi noti

* L tabelle $metrics non sono visibili per gli account di archiviazione Blob
* I messaggi in coda aggiunti a livello di programmazione potrebbe non essere visualizzati correttamente se i messaggi vengono codificati tramite la codifica Base64

17/05/2016
### <a name="version-07201605090"></a>Versione 0.7.20160509.0

#### <a name="new"></a>Nuovo

* Migliore gestione degli errori per i crash dell'app

#### <a name="fixes"></a>Correzioni

* Correzione del bug per cui i messaggi sulla barra delle informazioni talvolta non vengono visualizzati quando sono necessarie credenziali di accesso

#### <a name="known-issues"></a>Problemi noti

* Tabelle: in fase di aggiunta, modifica o importazione di un'entità che dispone di una proprietà con un valore numerico ambiguo, ad esempio "1" o "1.0", quando l'utente tenta di inviare il valore come un `Edm.String`, questo ritornerà indietro attraverso l'API del client come Edm.Double

31/03/2016

### <a name="version-07201603250"></a>Versione 0.7.20160325.0

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>Nuovo

* Supporto tabella: visualizzazione, esecuzione di query, esportazione, importazione e operazioni CRUD per le entità
* Supporto coda: visualizzazione, aggiunta, rimozione dei messaggi dalla coda
* Generazione di URI della firma di accesso condiviso per gli account di archiviazione
* La connessione agli account di archiviazione con l'URI della firma di accesso condiviso
* Notifiche di aggiornamento per gli aggiornamenti futuri di Storage Explorer
* Aspetto aggiornato

#### <a name="fixes"></a>Correzioni

* Miglioramenti dell'affidabilità e delle prestazioni

### <a name="known-issues-amp-mitigations"></a>Problemi noti &amp; Prevenzione

* Il download di file BLOB di grandi dimensioni non funziona correttamente. È consigliabile usare AzCopy fino alla risoluzione del problema
* Le credenziali dell'account non verranno recuperate o memorizzate nella cache se non è possibile trovare la cartella home o questa non può essere scritta
* In fase di aggiunta, modifica o importazione di un'entità che dispone di una proprietà con un valore numerico ambiguo, ad esempio "1" o "1.0", quando l'utente tenta di inviare il valore come un `Edm.String`, questo ritornerà indietro attraverso l'API del client come Edm.Double
* Quando si importano file CSV con i record su più righe, i dati possono venire tagliati o mescolati

03/02/2016

### <a name="version-07201601291"></a>Versione 0.7.20160129.1

#### <a name="fixes"></a>Correzioni

* Miglioramento delle prestazioni complessive durante il caricamento, il download e la copia di BLOB

14/01/2016

### <a name="version-07201601050"></a>Versione 0.7.20160105.0

#### <a name="new"></a>Nuovo

* Supporto Linux (funzionalità analoghe a OSX)
* Aggiunta di contenitori BLOB con la chiave di firma di accesso condiviso (SAS)
* Aggiunta degli account di archiviazione per Azure China
* Aggiunta di account di archiviazione con endpoint personalizzati
* Apertura e visualizzazione di BLOB di testo e immagine del contenuto
* Visualizzazione e modifica di metadati e proprietà di BLOB

#### <a name="fixes"></a>Correzioni

* Corretto: a volte il caricamento o il download di un numero elevato di BLOB (500+) può risultare in una schermata bianca dell'app
* Corretto: quando si imposta il livello di accesso pubblico del contenitore BLOB, il nuovo valore non viene aggiornato finché non si imposta nuovamente lo stato attivo sul contenitore. Inoltre, per impostazione predefinita la finestra di dialogo è sempre impostata su "Nessun accesso pubblico" e non sul valore effettivo corrente.
* Miglioramenti generali della tastiera/accessibilità e del supporto interfaccia utente
* Wrapping di testo della cronologia di navigazione quando è lungo con spazio
* La finestra di dialogo della firma di accesso condiviso supporta la convalida dell'input
* L'archiviazione locale continua a essere disponibile anche se le credenziali utente sono scadute
* Quando viene eliminato un contenitore BLOB aperto, viene chiuso Esplora risorse BLOB sul lato destro

#### <a name="known-issues"></a>Problemi noti

* Linux richiede l'aggiornamento della versione gcc. La procedura per effettuare l'aggiornamento è riportata qui sotto:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

18/11/2015
### <a name="version-07201511160"></a>Versione 0.7.20151116.0

#### <a name="new"></a>Nuovo

* Versioni per macOS e Windows
* Accesso per visualizzare gli account di archiviazione: possibilità di usare l'account aziendale, l'account Microsoft, 2FA e così via.
* Archivio di sviluppo locale (usare l'emulatore di archiviazione, solo Windows)
* Supporto di Azure Resource Manager e delle risorse Classic
* Creazione ed eliminazione di BLOB, code o tabelle
* Ricerca di BLOB, code o tabelle specifiche
* Esplorazione del contenuto dei contenitori BLOB
* Visualizzazione e spostamento tra directory
* Caricamento, download ed eliminazione di BLOB e cartelle
* Visualizzazione e modifica di metadati e proprietà di BLOB
* Generazione di chiavi di firma di accesso condiviso
* Gestione e creazione di SAP (Stored Access Policies)
* Ricerca di BLOB in base al prefisso
* Trascinamento della selezione di file da caricare o scaricare

#### <a name="known-issues"></a>Problemi noti

* Quando si imposta il livello di accesso pubblico del contenitore BLOB, il nuovo valore non viene aggiornato finché non si imposta nuovamente lo stato attivo sul contenitore
* Quando si apre la finestra di dialogo per impostare il livello di accesso pubblico, viene sempre visualizzato "Nessun accesso pubblico" come valore predefinito e non il valore corrente effettivo
* Impossibile rinominare i BLOB scaricati
* Le voci del log attività a volte rimangono "bloccate" in stato in corso quando si verifica un errore, e l'errore non viene visualizzato
* A volte si verifica un crash o diventa completamente bianco quando si cerca di caricare o scaricare un numero elevato di BLOB
* In alcuni casi l'annullamento di un'operazione di copia non funziona
* Durante la creazione di un contenitore (BLOB/coda/tabella), se si immette un nome non valido di input e si procede alla creazione di un altro nome in un altro tipo di contenitore non è possibile impostare lo stato attivo sul nuovo tipo
* Impossibile creare una nuova cartella o rinominare una cartella




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
