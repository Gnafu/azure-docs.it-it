---
title: Note sulla versione dell'agente Sincronizzazione file di Azure | Microsoft Docs
description: Note sulla versione dell'agente Sincronizzazione file di Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 8/14/2019
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 7286d8465d857b24c72c46e9d671abb83ccefc21
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259364"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Note sulla versione dell'agente Sincronizzazione file di Azure
Sincronizzazione file di Azure consente di centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Le installazioni Windows Server vengono trasformate in una cache rapida della condivisione file di Azure. Per accedere ai dati in locale, è possibile usare qualsiasi protocollo disponibile in Windows Server, inclusi SMB, NFS e FTPS. Si può usare qualsiasi numero di cache in tutto il mondo.

Questo articolo illustra le note sulla versione per le versioni supportate dell'agente Sincronizzazione file di Azure.

## <a name="supported-versions"></a>Versioni supportate
L'agente Sincronizzazione file di Azure supporta le versioni seguenti:

| Attività cardine | Numero di versione dell'agente | Data di rilascio | Stato |
|----|----------------------|--------------|------------------|
| Aggiornamento cumulativo di luglio 2019- [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 24 luglio 2019 | Supportato |
| Aggiornamento cumulativo di luglio 2019- [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 12 luglio 2019 | Supportato |
| Versione v7- [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | 19 giugno 2019 | Supportato |
| Aggiornamento cumulativo di giugno 2019- [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | 27 giugno 2019 | Supportato |
| Aggiornamento cumulativo di giugno 2019- [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | 13 giugno, 2019 | Supportato |
| Aggiornamento cumulativo di maggio 2019- [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | 7 maggio 2019 | Supportato |
| Versione V6- [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | 21 aprile 2019 | Supportato |
| Aggiornamento cumulativo di aprile 2019- [KB4481061](https://support.microsoft.com/help/4481061)| 5.2.0.0 | 4 aprile 2019 | Supportato |
| Aggiornamento cumulativo di marzo 2019- [KB4481060](https://support.microsoft.com/help/4481060)| 5.1.0.0 | 7 marzo 2019 | Supportato |
| Versione v5 - [KB4459989](https://support.microsoft.com/help/4459989)| 5.0.2.0 | 12 febbraio 2019 | Supportato |
| Gennaio 2019 aggiornamento cumulativo - [KB4481059](https://support.microsoft.com/help/4481059)| 4.3.0.0 | 14 gennaio 2019 | Supportato-la versione dell'agente scadrà il 5 novembre 2019 |
| Aggiornamento cumulativo di dicembre 2018 - [KB4459990](https://support.microsoft.com/help/4459990)| 4.2.0.0 | 10 dicembre 2018 | Supportato-la versione dell'agente scadrà il 5 novembre 2019 |
| Aggiornamento cumulativo di dicembre 2018 | 4.1.0.0 | 4 dicembre 2018 | Supportato-la versione dell'agente scadrà il 5 novembre 2019 |
| Versione v4 | 4.0.1.0 | 13 novembre 2018 | Supportato-la versione dell'agente scadrà il 5 novembre 2019 |
| Versione V3 | 3.1.0.0-3.4.0.0 | Non supportata | Non supportato-le versioni dell'agente scadono il 19 agosto 2019 |
| Agenti pre-GA | 1.1.0.0 - 3.0.13.0 | N/D | Non supportato: versione dell'agente scaduta il 1° ottobre 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Criteri di aggiornamento dell'agente Sincronizzazione file di Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-7200"></a>Versione dell'agente 7.2.0.0
Le note sulla versione seguenti sono relative alla versione 7.2.0.0 dell'agente Sincronizzazione file di Azure rilasciato il 24 luglio 2019. Queste note si aggiungono alle note sulla versione elencate per la versione 7.0.0.0.

Elenco dei problemi risolti in questa versione:  
- L'agente di sincronizzazione archiviazione (FileSyncSvc) si arresta in modo anomalo se la configurazione del proxy è null.
- L'endpoint server avvierà BCDR (Error 0x80c80257-ECS_E_BCDR_IN_PROGRESS) se più endpoint nel server hanno lo stesso nome.
- Miglioramenti dell'affidabilità di suddivisione in livelli nel cloud.

## <a name="agent-version-7100"></a>Versione dell'agente 7.1.0.0
Le note sulla versione seguenti sono relative alla versione 7.1.0.0 dell'agente Sincronizzazione file di Azure rilasciato il 12 luglio 2019. Queste note si aggiungono alle note sulla versione elencate per la versione 7.0.0.0.

Elenco dei problemi risolti in questa versione:  
- L'accesso o l'esplorazione di una posizione di endpoint server tramite SMB è lento in Windows Server 2012 R2. 
- Maggiore utilizzo della CPU dopo l'installazione dell'agente Sincronizzazione file di Azure V6.
- Miglioramenti della telemetria di suddivisione in livelli nel cloud.
- Vari miglioramenti dell'affidabilità per cloud a livelli e sincronizzazione.

## <a name="agent-version-7000"></a>Versione dell'agente 7.0.0.0
Le note sulla versione seguenti sono relative alla versione 7.0.0.0 dell'agente di Sincronizzazione file di Azure (rilasciato il 19 giugno 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti

- Supporto per dimensioni di condivisione file più grandi
    - Con l'anteprima delle condivisioni file di Azure di dimensioni maggiori, vengono aumentati anche i limiti di supporto per sincronizzazione file. In questo primo passaggio, Sincronizzazione file di Azure supporta ora fino a 25 TB e 50 milioni di file in un singolo spazio dei nomi di sincronizzazione. Per richiedere l'anteprima della condivisione file di grandi dimensioni, compilare il https://aka.ms/azurefilesatscalesurvey modulo. 
- Supporto per l'impostazione del firewall e della rete virtuale negli account di archiviazione
    - Sincronizzazione file di Azure supporta ora l'impostazione del firewall e della rete virtuale negli account di archiviazione. Per configurare la distribuzione in modo che funzioni con l'impostazione firewall e rete virtuale, vedere [configurare le impostazioni del firewall e della rete virtuale](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings).
- Cmdlet di PowerShell per sincronizzare immediatamente i file modificati nella condivisione file di Azure
    - Per sincronizzare immediatamente i file modificati nella condivisione file di Azure, è possibile usare il cmdlet di PowerShell Invoke-AzStorageSyncChangeDetection per avviare manualmente il rilevamento delle modifiche nella condivisione file di Azure. Questo cmdlet è destinato agli scenari in cui alcuni tipi di processi automatici apportano modifiche alla condivisione file di Azure o le modifiche vengono eseguite da un amministratore, ad esempio lo spostamento di file e directory nella condivisione. Per le modifiche degli utenti finali, è consigliabile installare l'agente di Sincronizzazione file di Azure in una macchina virtuale IaaS e fare in modo che gli utenti finali accedano alla condivisione file tramite la macchina virtuale IaaS. In questo modo tutte le modifiche si sincronizzano rapidamente con altri agenti senza la necessità di usare il cmdlet Invoke-AzStorageSyncChangeDetection. Per altre informazioni, vedere la documentazione di [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) .
- Miglioramento dell'esperienza del portale se si riscontrano file che non eseguono la sincronizzazione
    - Se sono presenti file che non riescono a eseguire la sincronizzazione, ora si differenziano gli errori temporanei e permanenti nel portale. Gli errori temporanei si risolvono in genere senza richiedere l'intervento dell'amministratore. Un file attualmente in uso, ad esempio, non verrà sincronizzato fino alla chiusura dell'handle di file. Per gli errori persistenti, viene ora visualizzato il numero di file interessati da ogni errore. Il numero di errori persistente viene inoltre visualizzato nella colonna file non sincronizzati di tutti gli endpoint server in un gruppo di sincronizzazione.
- Miglioramento del ripristino a livello di file di backup di Azure
    - I singoli file ripristinati con backup di Azure vengono ora rilevati e sincronizzati con l'endpoint server più velocemente.
- Miglioramento dell'affidabilità del cmdlet di richiamo a livelli cloud 
    - Il cmdlet per la suddivisione in livelli cloud (Invoke-StorageSyncFileRecall) supporta ora il numero di tentativi per file e il ritardo tra tentativi, simile a Robocopy.
- Supporto solo per TLS 1,2 (TLS 1,0 e 1,1 è disabilitato)
    - Sincronizzazione file di Azure supporta ora l'uso di TLS 1,2 solo su server con TLS 1,0 e 1,1 disabilitato. Prima di questo miglioramento, la registrazione del server avrebbe esito negativo se TLS 1,0 e 1,1 è stato disabilitato nel server.
- Miglioramenti delle prestazioni e dell'affidabilità per la sincronizzazione e la suddivisione in livelli nel cloud
    - In questa versione sono stati apportati diversi miglioramenti all'affidabilità e alle prestazioni. Alcune di esse sono destinate a rendere più efficiente la suddivisione in livelli nel cloud e Sincronizzazione file di Azure come un intero lavoro migliore in tali situazioni quando è impostata una pianificazione della limitazione della larghezza di banda.

### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nell'opzione di distribuzione di nano server.
- L'agente è supportato solo in Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- L'esecuzione di Sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può causare risultati imprevisti. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- File con caratteri non supportati. Vedere [Guida alla risoluzione dei problemi](storage-sync-files-troubleshoot.md#handling-unsupported-characters) per un elenco dei caratteri non supportati.
- File o directory che terminano con un punto.
- Percorsi che contengono più di 2.048 caratteri.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza se superiore a 2 kB. Il problema si verifica solo se sono presenti più di 40 voci di controllo di accesso in un singolo elemento.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza usato per il controllo.
- Attributi estesi.
- Flussi dei dati alternativi.
- Reparse point.
- Collegamenti reali.
- La compressione (se impostata in un file server) non viene mantenuta quando le modifiche vengono sincronizzate con tale file da altri endpoint.
- File crittografati con EFS (o un'altra crittografia in modalità utente) che impediscono al servizio di leggere i dati.

    > [!Note]  
    > Sincronizzazione file di Azure crittografa sempre i dati in transito e i dati vengono sempre crittografati quando sono inattivi in Azure.
 
### <a name="server-endpoint"></a>Endpoint server
- Un endpoint server può essere creato solo in un volume NTFS. ReFS, FAT, FAT32 e altri file system non sono attualmente supportati da Sincronizzazione file di Azure.
- I file a più livelli diventano inaccessibili se non vengono richiamati prima di eliminare l'endpoint server. Per ripristinare l'accesso ai file, ricreare l'endpoint server. Se sono trascorsi 30 giorni dall'eliminazione dell'endpoint server o se l'endpoint cloud è stato eliminato, i file a più livelli che non sono stati richiamati saranno inutilizzabili.
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare un file di paging del sistema operativo o dell'applicazione in un percorso dell'endpoint server.
- Se il server viene rinominato, il nome del server nel portale non viene aggiornato.

### <a name="cloud-endpoint"></a>Endpoint cloud
- Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una volta ogni 24 ore. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione.
- È possibile spostare il servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse o in una sottoscrizione diversa all'interno del tenant esistente di Azure AD. Se l'account di archiviazione viene spostato, è necessario concedere l'accesso al servizio Sincronizzazione file ibrida nell'account di archiviazione (vedere [Garantire che Sincronizzazione file di Azure possa accedere all'account di archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Sincronizzazione file di Azure non consente lo spostamento della sottoscrizione in un diverso tenant di Azure AD.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.

## <a name="agent-version-6300"></a>Versione dell'agente 6.3.0.0
Le note sulla versione seguenti sono relative alla versione 6.3.0.0 dell'agente Sincronizzazione file di Azure rilasciato il 27 giugno 2019. Queste note si aggiungono alle note sulla versione elencate per la versione 6.0.0.0.

Elenco dei problemi risolti in questa versione:  
- L'accesso o l'esplorazione di una posizione di endpoint server tramite SMB è lento in Windows Server 2012 R2 
- Maggiore utilizzo della CPU dopo l'installazione dell'agente Sincronizzazione file di Azure V6
- Miglioramenti della telemetria per la suddivisione in livelli nel cloud

## <a name="agent-version-6200"></a>Versione dell'agente 6.2.0.0
Le note sulla versione seguenti sono relative alla versione 6.2.0.0 dell'agente Sincronizzazione file di Azure rilasciato il 13 giugno 2019. Queste note si aggiungono alle note sulla versione elencate per la versione 6.0.0.0.

Elenco dei problemi risolti in questa versione:  
- Dopo aver creato un endpoint server, è possibile che si verifichi un utilizzo elevato della CPU quando il richiamo in background Scarica i file nel server
- Le operazioni di sincronizzazione e suddivisione in livelli nel cloud possono avere esito negativo con errore ECS_E_SERVER_CREDENTIAL_NEEDED a causa della scadenza
- Il richiamo di un file potrebbe non riuscire se l'URL per il download del file contiene caratteri riservati 

## <a name="agent-version-6100"></a>Versione dell'agente versione 6.1.0.0
Le note sulla versione seguenti sono relative alla versione versione 6.1.0.0 dell'agente Sincronizzazione file di Azure rilasciato il 6 maggio 2019. Queste note si aggiungono alle note sulla versione elencate per la versione 6.0.0.0.

Elenco dei problemi risolti in questa versione:  
- L'interfaccia di amministrazione di Windows non è in grado di visualizzare la versione dell'agente e la configurazione degli endpoint server nei server in cui è installato Sincronizzazione file di Azure agente versione 6,0.

## <a name="agent-version-6000"></a>Versione dell'agente 6.0.0.0
Le note sulla versione seguenti sono relative alla versione 6.0.0.0 dell'agente di Sincronizzazione file di Azure (rilasciato il 22 aprile 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti

- Supporto aggiornamento automatico agenti
  - Il feedback è stato aggiunto e è stata aggiunta una funzionalità di aggiornamento automatico nell'agente di Sincronizzazione file di Azure server. Per ulteriori informazioni, vedere [sincronizzazione file di Azure criteri di aggiornamento dell'agente](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy).
- Supporto per gli ACL di condivisione file di Azure
  - Sincronizzazione file di Azure ha sempre supportato la sincronizzazione degli ACL tra gli endpoint server, ma gli ACL non sono stati sincronizzati con l'endpoint cloud (condivisione file di Azure). In questa versione è stato aggiunto il supporto per sincronizzare gli ACL tra gli endpoint server e cloud.
- Sessioni di sincronizzazione di caricamento e download parallele per un endpoint server 
  - Gli endpoint server supportano ora il caricamento e il download di file contemporaneamente. Non è più in attesa del completamento di un download, quindi è possibile caricare i file nella condivisione file di Azure. 
- Nuovi cmdlet di suddivisione in livelli cloud per ottenere lo stato di volume e suddivisione in livelli
  - È ora possibile usare due nuovi cmdlet di PowerShell locali per il server per ottenere informazioni su suddivisione in livelli nel cloud e richiamo di file. Eseguono le informazioni di registrazione di due canali di eventi sul server disponibili:
    - Get-StorageSyncFileTieringResult elenca tutti i file e i relativi percorsi non suddivisi in livelli e segnala il motivo per cui.
    - Get-StorageSyncFileRecallResult segnala tutti gli eventi di richiamo di file. Elenca tutti i file richiamati e il relativo percorso, nonché l'esito positivo o negativo per il richiamo.
  - Per impostazione predefinita, entrambi i canali di eventi possono archiviare fino a 1 MB ciascuno: è possibile aumentare la quantità di file segnalati aumentando le dimensioni del canale di eventi.
- Supporto per la modalità FIPS
  - Sincronizzazione file di Azure supporta ora l'abilitazione della modalità FIPS nei server in cui è installato l'agente di Sincronizzazione file di Azure.
    - Prima di abilitare la modalità FIPS sul server, installare l'agente Sincronizzazione file di Azure e il [modulo PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) nel server. Se FIPS è già abilitato nel server, [scaricare manualmente](https://docs.microsoft.com/powershell/gallery/how-to/working-with-packages/manual-download) il [modulo PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) nel server.
- Vari miglioramenti dell'affidabilità per la suddivisione in livelli e la sincronizzazione nel cloud

### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nell'opzione di distribuzione di nano server.
- L'agente è supportato solo in Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- L'esecuzione di sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può portare a risultati imprevisti. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- File con caratteri non supportati. Vedere [Guida alla risoluzione dei problemi](storage-sync-files-troubleshoot.md#handling-unsupported-characters) per un elenco dei caratteri non supportati.
- File o directory che terminano con un punto.
- Percorsi che contengono più di 2.048 caratteri.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza se superiore a 2 kB. Il problema si verifica solo se sono presenti più di 40 voci di controllo di accesso in un singolo elemento.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza usato per il controllo.
- Attributi estesi.
- Flussi dei dati alternativi.
- Reparse point.
- Collegamenti reali.
- La compressione (se impostata in un file server) non viene mantenuta quando le modifiche vengono sincronizzate con tale file da altri endpoint.
- File crittografati con EFS (o un'altra crittografia in modalità utente) che impediscono al servizio di leggere i dati.

    > [!Note]  
    > Sincronizzazione file di Azure crittografa sempre i dati in transito e i dati vengono sempre crittografati quando sono inattivi in Azure.
 
### <a name="server-endpoint"></a>Endpoint server
- Un endpoint server può essere creato solo in un volume NTFS. ReFS, FAT, FAT32 e altri file system non sono attualmente supportati da Sincronizzazione file di Azure.
- I file a più livelli diventano inaccessibili se non vengono richiamati prima di eliminare l'endpoint server. Per ripristinare l'accesso ai file, ricreare l'endpoint server. Se sono trascorsi 30 giorni dall'eliminazione dell'endpoint server o se l'endpoint cloud è stato eliminato, i file a più livelli che non sono stati richiamati saranno inutilizzabili.
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare un file di paging del sistema operativo o dell'applicazione in un percorso dell'endpoint server.
- Se il server viene rinominato, il nome del server nel portale non viene aggiornato.

### <a name="cloud-endpoint"></a>Endpoint cloud
- Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una volta ogni 24 ore. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione.
- È possibile spostare il servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse o in una sottoscrizione diversa all'interno del tenant esistente di Azure AD. Se l'account di archiviazione viene spostato, è necessario concedere l'accesso al servizio Sincronizzazione file ibrida nell'account di archiviazione (vedere [Garantire che Sincronizzazione file di Azure possa accedere all'account di archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Sincronizzazione file di Azure non consente lo spostamento della sottoscrizione in un diverso tenant di Azure AD.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.
- Quando si visualizzano le proprietà del file da un client SMB, l'attributo offline può non essere correttamente impostato a causa della memorizzazione nella cache SMB dei metadati del file.

## <a name="agent-version-5200"></a>Versione dell'agente 5.2.0.0
Le note sulla versione seguenti sono relative alla versione 5.2.0.0 dell'agente Sincronizzazione file di Azure rilasciato il 4 aprile 2019. Queste note si aggiungono alle note sulla versione elencate per la versione 5.0.2.0.

Elenco dei problemi risolti in questa versione:  
- Miglioramenti dell'affidabilità per il trasferimento di dati offline e le funzionalità di ripresa del trasferimento dei dati
- Miglioramenti della telemetria di sincronizzazione

## <a name="agent-version-5100"></a>Versione dell'agente 5.1.0.0
Le note sulla versione seguenti sono relative alla versione 5.1.0.0 dell'agente Sincronizzazione file di Azure rilasciato il 7 marzo 2019. Queste note si aggiungono alle note sulla versione elencate per la versione 5.0.2.0.

Elenco dei problemi risolti in questa versione:  
- È possibile che i file non vengano sincronizzati con l'errore 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED) se l'enumerazione delle modifiche non riesce nel server
- Se una sessione o un file di sincronizzazione riceve un errore 0x80072F78 (WININET_E_INVALID_SERVER_RESPONSE), Sync riproverà a ripetere l'operazione
- I file potrebbero non essere sincronizzati con l'errore 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE)
- Quando si richiamano i file, può verificarsi un utilizzo elevato della memoria.
- Miglioramenti della telemetria per la suddivisione in livelli nel cloud 

## <a name="agent-version-5020"></a>Versione dell'agente 5.0.2.0
Le note seguenti si riferiscono alla versione 5.0.2.0 dell'agente Sincronizzazione file di Azure resa disponibile il 12 febbraio 2019.

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti

- Supporto per il cloud di Azure per enti pubblici
  - È stato aggiunto il supporto di anteprima per il cloud di Azure per enti pubblici. Richiede una sottoscrizione consentita e un download di agenti speciali da Microsoft. Per ottenere l'accesso all'anteprima, inviare un messaggio di posta elettronica direttamente a Microsoft all'indirizzo [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com).
- Supporto per la deduplicazione dei dati
    - La deduplicazione dei dati ha un supporto completo con cloud a livelli abilitato in Windows Server 2016 e Windows Server 2019. Abilitando la deduplicazione dei dati in un volume con cloud a livelli abilitato, è possibile memorizzare nella cache un numero maggiore di file in locale senza effettuare il provisioning di altro spazio di archiviazione.
- Supporto per il trasferimento dei dati offline (ad esempio tramite Data Box)
    - Migrare facilmente grandi quantità di dati in Sincronizzazione file di Azure secondo la modalità scelta. È possibile scegliere Azure Data Box, AzCopy e persino servizi di migrazione di terze parti. Non servono grandi quantità di larghezza di banda per trasferire i dati in Azure, nel caso di Data Box è sufficiente inviarli all'interno del servizio tramite posta elettronica. Per altre informazioni, vedere la [documentazione per il trasferimento dati offline](https://aka.ms/AFS/OfflineDataTransfer).
- Prestazioni di sincronizzazione migliorate
    - I clienti con più endpoint server nello stesso volume potrebbero aver riscontrato prestazioni di sincronizzazione più lente nelle versioni precedenti. Sincronizzazione file di Azure crea uno snapshot VSS temporaneo una volta al giorno nel server per sincronizzare i file con handle aperti. Sincronizzazione file ora supporta la sincronizzazione di più endpoint server in un volume quando è attiva una sessione di sincronizzazione VSS. Non occorrerà più attendere il completamento della sessione di sincronizzazione VSS perché la sincronizzazione possa riprendere in altri endpoint server nel volume.
- Funzionalità di monitoraggio nel portale migliorata
    - Sono stati aggiunti grafici nel portale del Servizio di sincronizzazione archiviazione per visualizzare:
        - Numero di file sincronizzati
        - Dimensione dei dati trasferiti
        - Numero di file che non eseguono la sincronizzazione
        - Dimensione dei dati richiamati
        - Stato di connettività del server
    - Per altre informazioni, vedere [Monitorare Sincronizzazione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring).
- Scalabilità e affidabilità migliorate
    - Numero massimo di oggetti file system (directory e file) in una directory aumentato a 1.000.000. Il limite precedente era 200.000.
    - Sincronizzazione file effettuerà un tentativo di riprendere il trasferimento dei dati anziché una nuova trasmissione, quando un trasferimento viene interrotto per file di grandi dimensioni 

### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nelle opzioni di distribuzione Windows Server Core o Nano Server.
- L'agente è supportato solo in Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.
- La modalità FIPS non è supportata e deve essere disabilitata. 

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- L'esecuzione di sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può portare a risultati imprevisti. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- File con caratteri non supportati. Vedere [Guida alla risoluzione dei problemi](storage-sync-files-troubleshoot.md#handling-unsupported-characters) per un elenco dei caratteri non supportati.
- File o directory che terminano con un punto.
- Percorsi che contengono più di 2.048 caratteri.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza se superiore a 2 kB. Il problema si verifica solo se sono presenti più di 40 voci di controllo di accesso in un singolo elemento.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza usato per il controllo.
- Attributi estesi.
- Flussi dei dati alternativi.
- Reparse point.
- Collegamenti reali.
- La compressione (se impostata in un file server) non viene mantenuta quando le modifiche vengono sincronizzate con tale file da altri endpoint.
- File crittografati con EFS (o un'altra crittografia in modalità utente) che impediscono al servizio di leggere i dati.

    > [!Note]  
    > Sincronizzazione file di Azure crittografa sempre i dati in transito e i dati vengono sempre crittografati quando sono inattivi in Azure.
 
### <a name="server-endpoint"></a>Endpoint server
- Un endpoint server può essere creato solo in un volume NTFS. ReFS, FAT, FAT32 e altri file system non sono attualmente supportati da Sincronizzazione file di Azure.
- I file a più livelli diventano inaccessibili se non vengono richiamati prima di eliminare l'endpoint server. Per ripristinare l'accesso ai file, ricreare l'endpoint server. Se sono trascorsi 30 giorni dall'eliminazione dell'endpoint server o se l'endpoint cloud è stato eliminato, i file a più livelli che non sono stati richiamati saranno inutilizzabili.
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare un file di paging del sistema operativo o dell'applicazione in un percorso dell'endpoint server.
- Se il server viene rinominato, il nome del server nel portale non viene aggiornato.

### <a name="cloud-endpoint"></a>Endpoint cloud
- Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una volta ogni 24 ore. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione.
- È possibile spostare il servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse o in una sottoscrizione diversa all'interno del tenant esistente di Azure AD. Se l'account di archiviazione viene spostato, è necessario concedere l'accesso al servizio Sincronizzazione file ibrida nell'account di archiviazione (vedere [Garantire che Sincronizzazione file di Azure possa accedere all'account di archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Sincronizzazione file di Azure non consente lo spostamento della sottoscrizione in un diverso tenant di Azure AD.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.
- Quando si visualizzano le proprietà del file da un client SMB, l'attributo offline può non essere correttamente impostato a causa della memorizzazione nella cache SMB dei metadati del file.

## <a name="agent-version-4300"></a>Agente versione 4.3.0.0
Le note seguenti sulla versione si riferiscono alla versione 4.3.0.0 dell'agente Sincronizzazione file di Azure con data di rilascio il 14 luglio 2019. Queste note si aggiungono a quelle elencate per la versione 4.0.1.0.

Elenco dei problemi risolti in questa versione:  
- I file non vengono suddivisi in livelli dopo l'aggiornamento dell'agente Sincronizzazione file di Azure alla versione 4.x.
- AfsUpdater.exe è ora supportato in Windows Server 2019.
- Vari miglioramenti dell'affidabilità per la sincronizzazione. 

## <a name="agent-version-4200"></a>Agente versione 4.2.0.0
Le note sulla versione seguenti si riferiscono alla versione 4.2.0.0 dell'agente Sincronizzazione file di Azure rilasciata il 10 dicembre 2018. Queste note si aggiungono a quelle elencate per la versione 4.0.1.0.

Elenco dei problemi risolti in questa versione:  
- Quando si crea uno snapshot VSS, può verificarsi un errore irreversibile 0x3B o 0x1E.  
- Potrebbe verificarsi una perdita di memoria cloud quando è abilitato il cloud a livelli  

## <a name="agent-version-4100"></a>Agente versione 4.1.0.0
Le note sulla versione seguenti si riferiscono alla versione 4.1.0.0 dell'agente Sincronizzazione file di Azure rilasciata il 4 dicembre 2018. Queste note si aggiungono a quelle elencate per la versione 4.0.1.0.

Elenco dei problemi risolti in questa versione:  
- Il server potrebbe smettere di rispondere a causa di una perdita di memoria del cloud a livelli.  
- L'installazione dell'agente ha esito negativo con l'errore seguente: Errore 1921. Impossibile arrestare il servizio 'Agente di sincronizzazione archiviazione' (FileSyncSvc).  Verificare di disporre di privilegi sufficienti per l'arresto dei servizi di sistema.  
- Potrebbe verificarsi un arresto anomalo del servizio Agente di sincronizzazione archiviazione (FileSyncSvc) quando l'utilizzo della memoria è elevato.  
- Vari miglioramenti dell'affidabilità per cloud a livelli e sincronizzazione.

## <a name="agent-version-4010"></a>Agente versione 4.0.1.0
Le note sulla versione seguenti si riferiscono alla versione 4.0.1.0 dell'agente Sincronizzazione file di Azure rilasciata il 13 novembre 2018.

### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nelle opzioni di distribuzione Windows Server Core o Nano Server.
- L'agente è supportato solo in Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.
- La modalità FIPS non è supportata e deve essere disabilitata. 
- Quando si crea uno snapshot VSS, può verificarsi un errore irreversibile 0x3B o 0x1E.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- L'esecuzione di sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può portare a risultati imprevisti. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.
- La deduplicazione dei dati e la suddivisione in livelli cloud non sono supportate nello stesso volume.

### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- File con caratteri non supportati. Vedere [Guida alla risoluzione dei problemi](storage-sync-files-troubleshoot.md#handling-unsupported-characters) per un elenco dei caratteri non supportati.
- File o directory che terminano con un punto.
- Percorsi che contengono più di 2.048 caratteri.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza se superiore a 2 kB. Il problema si verifica solo se sono presenti più di 40 voci di controllo di accesso in un singolo elemento.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza usato per il controllo.
- Attributi estesi.
- Flussi dei dati alternativi.
- Reparse point.
- Collegamenti reali.
- La compressione (se impostata in un file server) non viene mantenuta quando le modifiche vengono sincronizzate con tale file da altri endpoint.
- File crittografati con EFS (o un'altra crittografia in modalità utente) che impediscono al servizio di leggere i dati.

    > [!Note]  
    > Sincronizzazione file di Azure crittografa sempre i dati in transito e i dati vengono sempre crittografati quando sono inattivi in Azure.
 
### <a name="server-endpoint"></a>Endpoint server
- Un endpoint server può essere creato solo in un volume NTFS. ReFS, FAT, FAT32 e altri file system non sono attualmente supportati da Sincronizzazione file di Azure.
- I file a più livelli diventano inaccessibili se non vengono richiamati prima di eliminare l'endpoint server. Per ripristinare l'accesso ai file, ricreare l'endpoint server. Se sono trascorsi 30 giorni dall'eliminazione dell'endpoint server o se l'endpoint cloud è stato eliminato, i file a più livelli che non sono stati richiamati saranno inutilizzabili.
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare un file di paging del sistema operativo o dell'applicazione in un percorso dell'endpoint server.
- Se il server viene rinominato, il nome del server nel portale non viene aggiornato.

### <a name="cloud-endpoint"></a>Endpoint cloud
- Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una volta ogni 24 ore. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione.
- È possibile spostare il servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse o in una sottoscrizione diversa all'interno del tenant esistente di Azure AD. Se l'account di archiviazione viene spostato, è necessario concedere l'accesso al servizio Sincronizzazione file ibrida nell'account di archiviazione (vedere [Garantire che Sincronizzazione file di Azure possa accedere all'account di archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Sincronizzazione file di Azure non consente lo spostamento della sottoscrizione in un diverso tenant di Azure AD.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- L'impostazione dei i criteri di suddivisione in livelli nel cloud in base alla data viene usata per specificare i file che devono essere memorizzati nella cache se aperti in un numero specificato di giorni. Per altre informazioni, vedere [Suddivisione in livelli cloud](https://docs.microsoft.com/azure/storage/files/storage-sync-cloud-tiering#afs-force-tiering).
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.
- Quando si visualizzano le proprietà del file da un client SMB, l'attributo offline può non essere correttamente impostato a causa della memorizzazione nella cache SMB dei metadati del file.
