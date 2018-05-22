---
title: Proteggere le macchine virtuali distribuite nello Stack di Azure | Documenti Microsoft
description: Linee guida su come proteggere le macchine virtuali distribuite nello Stack di Azure.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/14/2018
ms.author: jeffgilb
ms.reviewer: hector.linares
ms.openlocfilehash: 734ee0e6ffb0dab660a2b63b431780208e0e0484
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2018
---
# <a name="protect-virtual-machines-deployed-on-azure-stack"></a>Proteggere le macchine virtuali distribuite nello Stack di Azure

Usare questo articolo come guida per lo sviluppo di un piano per la protezione di macchine virtuali (VM) che gli utenti distribuiscono nello Stack di Azure.

Per evitare la perdita di dati e i tempi di inattività non pianificato, è necessario implementare un piano di ripristino di backup o ripristino di emergenza per le applicazioni utente e i relativi dati. Questo piano potrebbe essere univoco per ogni applicazione ma segue una struttura definita continuità completa dell'organizzazione e di strategia di ripristino (BC/ripristino di emergenza) di emergenza. È un buon punto di partenza [progettazione resiliente delle applicazioni per Azure](https://docs.microsoft.com/azure/architecture/resiliency), che fornisce i modelli generali e procedure consigliate per la disponibilità dell'applicazione e resilienza.

>[!IMPORTANT]
> Testare i piani di ripristino di backup e ripristino di emergenza su base continuativa. È necessario per questa opzione per assicurarsi che:
> * I piani di lavoro
> * I piani comunque soddisfano le esigenze per che e progettati.

## <a name="azure-stack-infrastructure-recovery"></a>Ripristino dell'infrastruttura di Azure Stack

Gli utenti sono responsabili per proteggere le proprie macchine virtuali separatamente da servizi di infrastruttura di Azure dello Stack.

Il piano di ripristino per i servizi di infrastruttura di Azure Stack **non** includono ripristino di macchine virtuali degli utenti, account di archiviazione o database. Come proprietario dell'applicazione, è responsabile per l'implementazione di un piano di ripristino per le applicazioni e dati.

Se il cloud di Azure Stack è offline per un periodo di tempo prolungato o perde, è necessario disporre di un ripristino piano inserire che:

* Garantisce tempi di inattività minimi
* Consente di mantenere le macchine virtuali critiche, ad esempio i server di database, in esecuzione
* Consente alle applicazioni di mantenere le richieste utente

L'operatore del cloud Azure Stack è responsabile per la creazione di un piano di ripristino per i servizi e l'infrastruttura di Azure Stack sottostante. Per ulteriori informazioni, leggere l'articolo [recupero dalla perdita di dati irreversibile](https://docs.microsoft.com/azure/azure-stack/azure-stack-backup-recover-data).

## <a name="sourcetarget-combinations"></a>Combinazioni di origine/destinazione

Ogni cloud Stack Azure viene distribuito in un Data Center. È possibile ripristinare le applicazioni è necessario un ambiente separato. L'ambiente di ripristino può essere un altro cloud di Azure Stack in un altro Data Center o cloud pubblico di Azure. I sovranità dati e i requisiti sulla privacy dei dati determina l'ambiente di ripristino per l'applicazione. Come abilitare la protezione per ogni applicazione, è la flessibilità di scegliere un'opzione di ripristino specifico per ognuno di essi. È possibile avere applicazioni in una sottoscrizione, il backup dei dati in un altro Data Center. In un'altra sottoscrizione, è possibile replicare i dati al cloud pubblico di Azure.

Pianificare la strategia di backup e ripristino e ripristino di emergenza per ciascuna applicazione determinare la destinazione per ogni applicazione. Un piano di ripristino consentirà l'organizzazione correttamente le dimensioni di archiviazione capacità necessaria in locale e nel cloud pubblico di consumo del progetto.

|  | Azure globale | Stack di Azure distribuiti in Data Center CSP e utilizzato dal CSP | Stack di Azure distribuiti in Data Center di clienti e gestita dal cliente |
|------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| **Stack di Azure distribuiti in Data Center CSP e utilizzato dal CSP** | Macchine virtuali degli utenti vengono distribuite allo Stack di Azure gestita CSP. Macchine virtuali degli utenti vengono ripristinate dal backup o il failover direttamente in Azure. | CSP opera le istanze primarie e secondarie dello Stack di Azure nel proprio Data Center. Macchine virtuali degli utenti vengono ripristinate o il failover tra le due istanze di Stack di Azure. | CSP opera Azure Stack nel sito primario. Data Center del cliente è la destinazione di ripristino o il failover. |
| **Stack di Azure distribuiti in Data Center di clienti e gestita dal cliente** | Macchine virtuali degli utenti vengono distribuite per il cliente gestita dello Stack di Azure. Macchine virtuali degli utenti vengono ripristinate dal backup o il failover direttamente in Azure. | Cliente opera le istanze primarie e secondarie dello Stack di Azure nel proprio Data Center. Macchine virtuali degli utenti vengono ripristinate o il failover tra le due istanze di Stack di Azure. | Cliente opera Azure Stack nel sito primario. Data Center CSP è la destinazione di ripristino o il failover. |

![Combinazioni di origine-destinazione](media\azure-stack-manage-vm-backup\vm_backupdataflow_01.png)

## <a name="application-recovery-objectives"></a>Obiettivi di ripristino applicazione

È necessario determinare la quantità di tempo di inattività e perdita di dati che per ogni applicazione può tollerare l'organizzazione. Identificando i tempi di inattività e perdita di dati è possibile creare un piano di ripristino che riduce al minimo l'impatto di una situazione di emergenza per l'organizzazione. Per ogni applicazione, prendere in considerazione:

 - **Obiettivo tempo di ripristino (RTO)**  
RTO è il tempo massimo accettabile che un'applicazione può essere disponibile dopo un evento imprevisto. Ad esempio, un RTO di 90 minuti significa che è necessario essere in grado di ripristinare l'applicazione a uno stato di esecuzione all'interno di 90 minuti dall'inizio di un'emergenza. Se si dispone di un RTO basso, è possibile mantenere una seconda distribuzione continuamente in esecuzione in modalità standby per la protezione da un'interruzione dell'alimentazione locale.
 - **Obiettivo del punto di ripristino (RPO)**  
RPO è la durata massima di perdita di dati è accettabile durante un'emergenza. Per esempio, se si archiviano i dati in un singolo database, senza replica in altri database, e si eseguono backup orari, si potrebbero perdere fino a un'ora di dati.

Gli obiettivi RTO e RPO sono requisiti aziendali. Condurre una valutazione dei rischi per definire obiettivi RTO e RPO dell'applicazione.

È un'altra metrica **tempo medio per il ripristino** (MTTR), ovvero il tempo medio impiegato per ripristinare l'applicazione dopo un errore. MTTR è un valore empirico per un sistema. Se questo tempo supera l'obiettivo RTO, di conseguenza un errore di sistema causerà un'interruzione delle attività inaccettabile, poiché non sarà possibile ripristinare il sistema entro l'obiettivo RTO definito.

### <a name="backup-restore"></a>Ripristino dei backup

Lo schema più comune di protezione per le applicazioni basate su VM consiste nell'utilizzare software di backup. Backup di una macchina virtuale in genere include il sistema operativo, configurazione del sistema operativo, i file binari dell'applicazione e dati dell'applicazione. I backup vengono creati tramite l'aggiunta di uno snapshot dei volumi, dischi o dell'intera macchina virtuale. Con lo Stack di Azure, ha la flessibilità di backup all'interno del contesto del sistema operativo guest o dalla risorsa di archiviazione Azure Stack e le API di calcolo. Stack di Azure non consente di effettuare backup a livello di hypervisor.
 
![Backup restor](media\azure-stack-manage-vm-backup\vm_backupdataflow_03.png)

Per ripristinare l'applicazione, è necessario il ripristino di uno o più macchine virtuali nel cloud stesso o a un nuovo cloud. È possibile scegliere un cloud nel Data Center o cloud pubblico. Il cloud che scelto è completamente all'interno del controllo e si basa sui requisiti di privacy e sovranità dati.
 
 - RTO: Tempo di inattività misurato in ore
 - RPO: Perdita di dati della variabile (a seconda della frequenza di backup)
 - Topologia di distribuzione: attiva/passiva

#### <a name="planning-your-backup-strategy"></a>Pianificazione della strategia di backup

Pianificazione della strategia di backup e definizione dei requisiti di scala inizia con quantificare il numero di istanze di macchine Virtuali che devono essere protette. Backup di tutte le macchine virtuali in tutti i server in un ambiente è una strategia comune. Tuttavia, con lo Stack di Azure, esistono alcune macchine virtuali che si devono eseguire il backup. Ad esempio, le macchine virtuali in un set di scalabilità sono considerate temporanee risorse che possono provenire e passare a volte senza preavviso. Eventuali dati durevoli che necessitano di protezione vengono archiviati in un archivio diverso, ad esempio un archivio di database o dell'oggetto.

Considerazioni importanti per il backup di macchine virtuali nello Stack di Azure:

 - **Categorizzazione**
    - Si consideri un modello in cui gli utenti optare per backup di VM.
    - Definire un ripristino contratto di servizio (SLA) in base alla priorità di applicazioni o l'impatto sull'azienda.
 - **Ridimensionare**
    - Considerare i backup sfalsati caricamento un numero elevato di nuove macchine virtuali (se è necessario eseguire backup).
    - Valutare i prodotti di backup che è possano acquisire e trasmettere i dati di backup per ridurre al minimo il contenuto delle risorse della soluzione in modo efficiente.
    - Valutare i prodotti per il backup sono memorizzate in modo efficiente i dati di backup tramite backup incrementale o differenziali per ridurre al minimo la necessità di backup completi tra tutte le macchine virtuali nell'ambiente.
 - **Restore**
    - Prodotti per il backup è possono ripristinare i dati dell'applicazione all'interno di una macchina virtuale esistente, o l'intera risorsa di macchina virtuale i dischi virtuali e dischi virtuali associati. Lo schema di ripristino che è necessario dipende dal modo in cui si prevede di ripristinare l'applicazione e può rallentare l'applicazione il tempo di ripristino. Ad esempio, può risultare più semplice ridistribuire SQL server da un modello e quindi ripristinare i database anziché ripristinare l'intera macchina virtuale o un set di macchine virtuali.

### <a name="replicationmanual-failover"></a>Failover della replica/manuale

Un approccio alternativo alla che supportano la disponibilità elevata consiste nel replicare le macchine virtuali dell'applicazione a un altro cloud e si basano su un failover manuale. Il livello di macchina virtuale o del sistema operativo guest può essere eseguita la replica dei dati dell'applicazione, sistema operativo e file binari dell'applicazione. Il failover viene gestito mediante software aggiuntivi che non fa parte dell'applicazione.

Con questo approccio, l'applicazione viene distribuita in un cloud e la macchina virtuale viene replicato in altri cloud. Se viene attivato un failover, il database secondario macchine virtuali dovranno attivato nel cloud di secondo. In alcuni scenari, il failover crea i dischi di macchine virtuali e collega ad essi. Questo processo può richiedere molto tempo per completare, soprattutto con un'applicazione multilivello che richiede una sequenza di avvio specifico. È anche possibile passaggi che è necessario eseguire prima l'applicazione è pronto per iniziare a soddisfare le richieste.

![Failover manuale di replica](media\azure-stack-manage-vm-backup\vm_backupdataflow_02.png)

 - RTO: Tempo di inattività espresso in minuti
 - RPO: Perdita di dati della variabile (a seconda della frequenza di replica)
 - Topologia di distribuzione: attiva/passiva stand-by
 
### <a name="high-availabilityautomatic-failover"></a>Failover a disponibilità elevata/automatico

Per le applicazioni in cui l'azienda può tollerare pochi secondi o minuti di inattività e perdita di dati minimo, è necessario prendere in considerazione una configurazione a disponibilità elevata. Applicazioni a disponibilità elevata sono progettate in modo rapido e automatico recupero da errori. Per gli errori hardware locale, infrastruttura di Azure Stack implementa la disponibilità elevata nella rete fisica usando due cima commutatori rack. Per gli errori di livello di calcolo, Azure Stack utilizza più nodi in un'unità di scala. A livello di macchina virtuale, è possibile utilizzare i set di scalabilità in combinazione con domini di errore per verificare gli errori di nodo non accettano verso il basso l'applicazione.

In combinazione con set di scalabilità, l'applicazione sarà necessario supportare la disponibilità elevata in modo nativo o supporta l'utilizzo del software di clustering. Ad esempio, Microsoft SQL Server supporta in modo nativo la disponibilità elevata per i database utilizzando la modalità commit sincrono. Tuttavia, se è possibile supportare la replica asincrona, quindi verrà una perdita di dati. Anche le applicazioni possono essere distribuite in un cluster di failover in cui il software di clustering gestisce il failover automatico dell'applicazione.

Utilizzando questo approccio, l'applicazione è attivo solo in un cloud, ma il software viene distribuito in più aree. Altri cloud sono in modalità standby pronte avviare l'applicazione quando viene attivato il failover.

 - RTO: Tempo di inattività espresso in secondi
 - RPO: Perdita di dati minima
 - Topologia di distribuzione: standby attivo/attivo

### <a name="fault-tolerance"></a>Tolleranza di errore

Tale disco, alimentazione, porta di rete o nodo di Azure Stack fisica disponibilità ridondanza e l'infrastruttura del servizio di proteggere solo per l'hardware livello errori/errori. Tuttavia, se l'applicazione deve essere sempre disponibile e non comportano la perdita di tutti i dati, è necessario implementare la tolleranza di errore in modo nativo in un'applicazione o utilizzare software aggiuntivo per abilitare la tolleranza di errore.

In primo luogo, è necessario verificare che l'applicazione che sono distribuite macchine virtuali tramite scala imposta per la protezione da errori a livello di nodo. Per proteggere il cloud di passare alla modalità offline, la stessa applicazione già deve essere distribuita a un cloud diverso, in modo che può continuare a gestire le richieste senza interruzioni. Questo modello viene in genere definito a una distribuzione attivo-attivo.

Tenere presente che ogni cloud Azure Stack sono indipendenti tra loro, i cloud vengono sempre considerati attivi dal punto di vista dell'infrastruttura. In questo caso, più le istanze attive dell'applicazione vengono distribuite a uno o più cloud attivo.

 - RTO: Senza tempi di inattività
 - RPO: Senza perdita di dati
 - Topologia di distribuzione: attiva/attiva

### <a name="no-recovery"></a>Senza recupero

Alcune applicazioni nell'ambiente in uso potrebbero non essere necessario protezione contro la perdita di dati o di tempi di inattività imprevisti. Ad esempio, le macchine virtuali utilizzate per lo sviluppo e test in genere non è necessario ripristinare. È la decisione di eseguire senza la protezione per un'applicazione o di una macchina virtuale specifica. Stack di Azure non offre backup o la replica delle macchine virtuali dall'infrastruttura sottostante. Analogamente a Azure, è necessario registrarsi per la protezione per ogni macchina virtuale in ognuna delle sottoscrizioni.

 - RTO: errore irreversibile
 - RPO: Perdita dei dati completa

## <a name="recommended-topologies"></a>Topologie consigliate

Considerazioni importanti per la distribuzione di Azure Stack:

|     | Raccomandazione | Commenti |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Backup e ripristino di macchine virtuali a una destinazione di backup esterno già distribuiti nel Data Center | Consigliato | Sfruttare competenze operative e infrastruttura di backup esistente. Assicurarsi di ridimensionare l'infrastruttura di backup, è quindi pronto per proteggere le istanze VM aggiuntive. Verificare che l'infrastruttura di backup non è presente in prossimità dell'origine. È possibile ripristinare le macchine virtuali per l'origine dello Stack di Azure, a un'istanza secondaria di Stack di Azure o Azure. |
| Backup e ripristino di macchine virtuali a una destinazione di backup esterno dedicato allo Stack di Azure | Consigliato | È possibile acquistare una nuova infrastruttura di backup o di un'infrastruttura dedicata backup effettuare il provisioning per lo Stack di Azure. Verificare che l'infrastruttura di backup non è presente in prossimità dell'origine. È possibile ripristinare le macchine virtuali per l'origine dello Stack di Azure, a un'istanza secondaria di Stack di Azure o Azure. |
| Backup e ripristino di macchine virtuali direttamente in Azure globale o in un provider del servizio attendibile | Consigliato | Come è possibile soddisfare i requisiti normativi e privacy dei dati, è possibile archiviare i backup in Azure globale o un provider del servizio attendibile. In teoria il provider del servizio è in esecuzione anche Stack di Azure per ottenere la coerenza nell'esperienza operativa quando si ripristina. |
| Macchine virtuali di replica e failover a un'istanza separata di Azure Stack | Consigliato | In caso di failover, è necessario disporre di un cloud di Azure Stack secondo completamente operativo per evitare tempo di inattività dell'applicazione estese. |
| Macchine virtuali di replica e failover direttamente in Azure o in un provider del servizio attendibile | Consigliato | Come è possibile soddisfare i requisiti normativi e privacy dei dati, è possibile replicare i dati in Azure globale o in un provider del servizio attendibile. In teoria il provider del servizio è in esecuzione anche Stack di Azure per ottenere la coerenza nell'esperienza operativa dopo il failover. |
| Distribuzione di destinazione del backup nel cloud stesso Stack di Azure con i dati delle applicazioni | Non è consigliabile | Evitare di archiviare i backup nell'ambito del cloud di Azure Stack stesso. Tempo di inattività non pianificato del cloud è possibile evitare i dati primario e i dati di backup. Se si sceglie di distribuire una destinazione di backup come appliance virtuale (per gli scopi di ottimizzazione per il backup e ripristino), è necessario che tutti i dati in modo continuo viene copiato in un percorso di backup esterno. |
| Distribuire i dispositivi di backup fisici rack in cui è installata la soluzione Azure Stack | Non supportate | In questo momento, è possibile connettersi tutti gli altri dispositivi nella parte superiore di commutatori rack che non fanno parte della soluzione originale. |

## <a name="next-steps"></a>Passaggi successivi

Questo articolo sono state fornite le linee guida generali per la protezione utente macchine virtuali distribuite nello Stack di Azure. Per informazioni sull'utilizzo dei servizi Azure per proteggere macchine virtuali degli utenti, vedere:

 - [Utilizzare Backup di Azure per eseguire il backup di file e le applicazioni nello Stack di Azure](https://docs.microsoft.com/azure/backup/backup-mabs-files-applications-azure-stack)
 - [Supporto tecnico di Azure Backup Server per lo Stack di Azure](https://docs.microsoft.com/azure/backup/ ) 
 - [Supporto tecnico di Azure Site Recovery per lo Stack di Azure](https://docs.microsoft.com/azure/site-recovery/)  

Per ulteriori informazioni sui prodotti partner che offrono protezione delle macchine Virtuali nello Stack di Azure, fare riferimento a "[proteggere applicazioni e dati Azure stack](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/)."
