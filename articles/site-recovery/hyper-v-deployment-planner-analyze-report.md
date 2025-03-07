---
title: Analizzare il report di Azure Site Recovery Deployment Planner per il ripristino di emergenza di macchine virtuali Hyper-V in Azure | Microsoft Docs
description: Questo articolo descrive come analizzare un report generato da Azure Site Recovery Deployment Planner per il ripristino di emergenza di macchine virtuali Hyper-V in Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/20/2019
ms.author: mayg
ms.openlocfilehash: 7bfe382ac1a175aafb4944dffa8d12a372f4fb70
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60772901"
---
# <a name="analyze-the-azure-site-recovery-deployment-planner-report"></a>Analizzare il report di Azure Site Recovery Deployment Planner
Questo articolo descrive i fogli contenuti nel report Excel generato da Azure Site Recovery Deployment Planner per uno scenario di distribuzione da Hyper-V ad Azure.

## <a name="on-premises-summary"></a>Riepilogo ambiente locale
Il foglio di lavoro On-premises summary (Riepilogo ambiente locale) offre una panoramica dell'ambiente Hyper-V profilato.

![Riepilogo ambiente locale](media/hyper-v-deployment-planner-analyze-report/on-premises-summary-h2a.png)

**Data di inizio** e **Data di fine**: date di inizio e fine dei dati di profilatura considerate per la generazione di report. Per impostazione predefinita, la data di inizio è la data in cui inizia la profilatura e la data di fine è la data in cui termina. Può trattarsi dei valori di 'StartDate' ed 'EndDate' se il report viene generato con questi parametri.

**Numero totale di giorni di profilatura**: il numero totale di giorni di profilatura tra le date di inizio e fine per i quali viene generato il report.

**Numero di macchine virtuali compatibili**: il numero totale di macchine virtuali compatibili per le quali vengono calcolati la larghezza di banda di rete necessaria, il numero necessario di account di archiviazione e il numero di core di Azure.

**Numero totale di dischi tra tutte le macchine virtuali compatibili**: il numero totale di dischi tra tutte le macchine virtuali compatibili.

**Numero medio di dischi per macchina virtuale compatibile**: il numero medio di dischi calcolato tra tutte le macchine virtuali compatibili.

**Dimensioni medie disco (GB)** : le dimensioni medie dei dischi calcolate tra tutte le macchine virtuali compatibili.

**RPO desiderato (minuti)** : l'obiettivo del punto di recupero predefinito o il valore passato per il parametro "DesiredRPO" al momento della generazione del report per stimare la larghezza di banda necessaria.

**Larghezza di banda desiderata (Mbps)** : Il valore passato per il parametro "Larghezza di banda" al momento della generazione del report per stimare l'obiettivo del punto di recupero (RPO) ottenibile.

**Varianza tipica dei dati osservata al giorno (GB)** : La varianza media dei dati osservata in tutti i giorni di profilatura.

## <a name="recommendations"></a>Consigli 
Il foglio Recommendations (Raccomandazioni) del report per lo scenario da Hyper-V ad Azure contiene i dettagli seguenti, in base al valore RPO desiderato selezionato:

![Raccomandazioni del report per lo scenario da Hyper-V ad Azure](media/hyper-v-deployment-planner-analyze-report/Recommendations-h2a.png)

### <a name="profile-data"></a>Dati del profilo
![Dati del profilo](media/hyper-v-deployment-planner-analyze-report/profile-data-h2a.png)

**Periodo dati profilati**: il periodo in cui la profilatura è stata in esecuzione. Per impostazione predefinita, lo strumento include nel calcolo tutti i dati profilati. Se nella generazione del report sono state usate le opzioni StartDate ed EndDate, viene generato il report per il periodo specifico. 

**Numero di server Hyper-V profilati**: Numero di server Hyper-V per cui viene generato il report delle macchine virtuali. Selezionare il numero per visualizzare il nome dei server Hyper-V. Verrà visualizzato il foglio On-premises Storage Requirement (Requisito di archiviazione locale) in cui sono elencati tutti i server con il relativo requisito di archiviazione. 

**RPO desiderato**: l'obiettivo del punto di recupero per la distribuzione. Per impostazione predefinita, la larghezza di banda di rete viene calcolata per i valori RPO di 15, 30 e 60 minuti. A seconda della selezione, i valori interessati verranno aggiornati nel foglio. Se durante la generazione del report è stato usato il parametro DesiredRPOinMin, tale valore verrà visualizzato nel risultato di Desired RPO (RPO desiderato).

### <a name="profiling-overview"></a>Panoramica della profilatura
![Panoramica della profilatura](media/hyper-v-deployment-planner-analyze-report/profiling-overview-h2a.png)

**Totale macchine virtuali profilate**: il numero totale di macchine virtuali delle quali sono disponibili dati profilati. Se il parametro VMListFile presenta nomi di macchine virtuali che non sono state profilate, tali macchine virtuali non verranno considerate nella generazione del report e verranno escluse dal conteggio totale delle macchine virtuali profilate.

**Macchine virtuali compatibili**: il numero di macchine virtuali che possono essere protette in Azure con Site Recovery. Si tratta del numero totale di macchine virtuali compatibili per le quali vengono calcolati la larghezza di banda di rete necessaria, il numero di account di archiviazione e il numero di core di Azure. I dettagli di ogni VM compatibile sono disponibili nella sezione "VM compatibili".

**Macchine virtuali non compatibili**: il numero di macchine virtuali profilate incompatibili con la protezione con Site Recovery. I motivi dell'incompatibilità sono indicati nella sezione "Macchine virtuali incompatibili". Se VMListFile contiene nomi di VM non profilate, tali VM vengono escluse dal conteggio delle VM incompatibili. Queste VM sono elencate con la dicitura "Data not found" (Dati non trovati) alla fine della sezione Incompatible VMs (VM incompatibili).

**RPO desiderato**: l'obiettivo del punto di recupero (RPO) desiderato, in minuti. Il report viene generato per tre valori RPO: 15 (impostazione predefinita), 30 e 60 minuti. La raccomandazione relativa alla larghezza di banda nel report varia in funzione della selezione nell'elenco a discesa **Desired RPO** (RPO desiderato) nella parte superiore destra del foglio. Se il report è stato generato usando il parametro -DesiredRPO con un valore personalizzato, questo valore personalizzato viene visualizzato come predefinito nell'elenco a discesa **Desired RPO** (RPO desiderato).

### <a name="required-network-bandwidth-mbps"></a>Larghezza di banda di rete necessaria (Mbps)
![Larghezza di banda di rete necessaria](media/hyper-v-deployment-planner-analyze-report/required-network-bandwidth-h2a.png)

**Per soddisfare il valore RPO il 100% del tempo**: la larghezza di banda consigliata in Mbps da allocare per soddisfare il valore RPO desiderato il 100% del tempo. Questa larghezza di banda deve essere dedicata alla replica differenziale stazionaria di tutte le VM compatibili per evitare violazioni di RPO.

**Per soddisfare il valore RPO il 90% del tempo**: se a causa dei prezzi della banda larga o per altri motivi non è possibile impostare la larghezza di banda necessaria per soddisfare il valore RPO desiderato il 100% del tempo. In questo caso è possibile usare un'impostazione di larghezza di banda inferiore che possa soddisfare il valore RPO desiderato il 90% del tempo. Per comprendere le implicazioni dell'impostazione di questa larghezza di banda inferiore, il report offre un'analisi ipotetica del numero e della durata delle violazioni del valore RPO prevedibili.

**Velocità effettiva ottenuta**: la velocità effettiva dal server in cui viene eseguito il comando GetThroughput all'area di Azure in cui si trova l'account di archiviazione. Questo valore di velocità effettiva indica il livello stimato che è possibile ottenere quando si proteggono le macchine virtuali compatibili usando Site Recovery. Le caratteristiche di archiviazione e di rete del server Hyper-V devono rimanere uguali a quelle del server da cui viene eseguito lo strumento.

Per tutte le distribuzioni aziendali di Site Recovery è consigliabile usare [ExpressRoute](https://aka.ms/expressroute).

### <a name="required-storage-accounts"></a>Account di archiviazione necessari
Il grafico seguente indica il numero totale di account di archiviazione (Standard e Premium) necessari per proteggere tutte le VM compatibili. Per informazioni sull'account di archiviazione da usare per ogni VM, vedere la sezione "Selezione host di archiviazione delle VM".

![Required Azure Storage Accounts (Account di archiviazione di Azure necessari)](media/hyper-v-deployment-planner-analyze-report/required-storage-accounts-h2a.png)

### <a name="required-number-of-azure-cores"></a>Numero di core Azure necessari
Questo risultato è il numero totale di core da configurare prima del failover o del failover di test di tutte le VM compatibili. Se i core disponibili nella sottoscrizione sono troppo pochi, Site Recovery non creerà le VM al momento del failover di test o del failover.

![Numero di core Azure necessari](media/hyper-v-deployment-planner-analyze-report/required-number-of-azure-cores-h2a.png)


### <a name="additional-on-premises-storage-requirement"></a>Requisito di archiviazione locale aggiuntivo

Spazio di archiviazione totale disponibile necessario nei server Hyper-V per eseguire correttamente la replica iniziale e la replica differenziale, in modo da garantire che la replica delle macchine virtuali non provochi tempi di inattività indesiderati per le applicazioni di produzione. Altre informazioni sui requisiti di ogni volume sono disponibili in [On-premises Storage Requirement](#on-premises-storage-requirement) (Requisito di archiviazione locale). 

Per comprendere il motivo per cui per la replica è necessario spazio disponibile, vedere la sezione [On-premises Storage Requirement](#why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication) (Requisito di archiviazione locale).

![Requisito di archiviazione locale e frequenza di copia](media/hyper-v-deployment-planner-analyze-report/on-premises-storage-and-copy-frequency-h2a.png)

### <a name="maximum-copy-frequency"></a>Frequenza di copia massima
È necessario impostare la frequenza di copia massima consigliata per la replica per ottenere l'obiettivo del punto di ripristino desiderato. Il valore predefinito è cinque minuti. Per ottenere un obiettivo del punto di ripristino migliore, è possibile impostare una frequenza di copia di 30 secondi.

### <a name="what-if-analysis"></a>Analisi di simulazione
![Analisi di simulazione](media/hyper-v-deployment-planner-analyze-report/what-if-analysis-h2a.png) Questa analisi indica quante violazioni possono verificarsi durante il periodo di profilatura quando si imposta una larghezza di banda inferiore per soddisfare il valore RPO desiderato solo il 90% del tempo. Possono verificarsi una o più violazioni del valore RPO in un giorno. Il grafico illustra il valore RPO di picco del giorno. In base a questa analisi è possibile stabilire se il numero di violazioni del valore RPO per il totale dei giorni e il valore RPO di picco raggiunto ogni giorno siano accettabili con la larghezza di banda inferiore specificata. Se sono accettabili è possibile allocare la larghezza di banda inferiore per la replica, altrimenti allocare una larghezza di banda superiore come suggerito per soddisfare il valore RPO desiderato il 100% del tempo. 

### <a name="recommendation-for-successful-initial-replication"></a>Raccomandazioni per il corretto completamento della replica iniziale
Questa sezione indica il numero dei batch nei quali proteggere le macchine virtuali e la larghezza di banda minima necessaria per completare correttamente la replica iniziale. 

![Suddivisione in batch per la replica iniziale](media/hyper-v-deployment-planner-analyze-report/ir-batching-h2a.png)

Le macchine virtuali devono essere protette in base all'ordine dei batch specificato. Ogni batch prevede un elenco specifico di macchine virtuali. Le macchine virtuali del batch 1 devono essere protette prima di quelle del batch 2. Le macchine virtuali del batch 2 devono essere protette prima di quelle del batch 3 e così via. Dopo aver completato la replica iniziale delle macchine virtuali del batch 1, è possibile abilitare la replica per le macchine virtuali del batch 2. Analogamente, dopo aver completato la replica iniziale delle macchine virtuali del batch 2 è possibile abilitare la replica per le macchine virtuali del batch 3 e così via. 

Se l'ordine dei batch non viene rispettato, potrebbe non essere disponibile larghezza di banda sufficiente per la replica iniziale delle macchine virtuali che verranno protette in un secondo momento. Di conseguenza, le macchine virtuali non completano mai la replica iniziale oppure alcune macchine virtuali protette potrebbero passare alla modalità di risincronizzazione. Il foglio relativo alla suddivisione in batch per la replica iniziale per il valore RPO selezionato contiene informazioni dettagliate sulle macchine virtuali da includere in ogni batch.

Il grafico mostra la distribuzione della larghezza di banda per la replica iniziale e la replica differenziale tra i batch nell'ordine specificato. Quando si protegge il primo batch di macchine virtuali, è disponibile tutta la larghezza di banda per la replica iniziale. Dopo aver completato la replica iniziale per il primo batch, una parte della larghezza di banda sarà necessaria per la replica differenziale. La larghezza di banda rimanente sarà disponibile per la replica iniziale del secondo batch di macchine virtuali. 

La barra relativa al batch 2 mostra la larghezza di banda necessaria per la replica differenziale delle macchine virtuali del batch 1 e la larghezza di banda disponibile per la replica iniziale delle macchine virtuali del batch 2. Analogamente, la barra relativa al batch 3 mostra la larghezza di banda necessaria per la replica differenziale per i batch precedenti (macchine virtuali del batch 1 e del batch 2) e la larghezza di banda disponibile per la replica iniziale per il batch 3 e così via. Dopo aver completato la replica iniziale di tutti i batch, l'ultima barra mostra la larghezza di banda necessaria per la replica differenziale di tutte le macchine virtuali protette.

**Perché è necessaria la suddivisione in batch per la replica iniziale?**
Il tempo di completamento della replica iniziale varia in base alle dimensioni del disco delle macchine virtuali, allo spazio su disco usato e alla velocità effettiva di rete disponibile. Nel foglio relativo alla suddivisione in batch per la replica iniziale per il valore RPO selezionato sono disponibili informazioni dettagliate.

### <a name="cost-estimation"></a>Stima dei costi
Il grafico offre la visualizzazione di riepilogo del costo stimato totale del ripristino di emergenza in Azure per l'area di destinazione scelta e la valuta specificata per la generazione del report.

![Riepilogo della stima dei costi](media/hyper-v-deployment-planner-analyze-report/cost-estimation-summary-h2a.png)

Il riepilogo consente di conoscere il costo che è necessario sostenere per archiviazione, calcolo, rete e licenza in caso di protezione di tutte le VM compatibili in Azure con Site Recovery. Il costo viene calcolato per le macchine virtuali compatibili e non per tutte le macchine virtuali profilate. 
 
È possibile visualizzare i costi su base mensile o annuale. Vedere altre informazioni sulle [aree di destinazione supportate](./hyper-v-deployment-planner-cost-estimation.md#supported-target-regions) e sulle [valute supportate](./hyper-v-deployment-planner-cost-estimation.md#supported-currencies).

**Costo per componenti**: il costo totale per il ripristino di emergenza viene diviso tra quattro componenti, ovvero calcolo, risorsa di archiviazione, rete e costo della licenza di Site Recovery. Il costo viene calcolato in base all'utilizzo che si verifica durante la replica e l'esercitazione sul ripristino di emergenza. Vengono presi in considerazione il calcolo, l'archiviazione (Premium e Standard), la connessione ExpressRoute/VPN configurata tra il sito locale e Azure e la licenza di Site Recovery.

**Costo per stati**: il costo totale del ripristino di emergenza viene classificato in base a due diversi stati: replica e drill del ripristino di emergenza. 

**Costo della replica**: il costo che viene addebitato durante la replica. Copre il costo dell'archiviazione, della rete e della licenza di Site Recovery. 

**Costo del drill del ripristino di emergenza**: il costo addebitato durante i failover di test. Durante il failover di test, Site Recovery attiva macchine virtuali. Il costo dell'esercitazione sul ripristino di emergenza copre il costo di calcolo e di archiviazione delle VM in esecuzione. 

**Costo dell'Archiviazione di Azure al mese/all’anno**: il grafico a barre mostra il costo di archiviazione totale addebitato per l'archiviazione premium e standard per la replica e il drill del ripristino di emergenza. È possibile visualizzare un'analisi dettagliata dei costi per VM nel foglio [Cost Estimation](hyper-v-deployment-planner-cost-estimation.md) (Stima costi).

### <a name="growth-factor-and-percentile-values-used"></a>Fattore di crescita e valori percentili usati
Questa sezione nella parte inferiore del foglio indica il valore percentile usato per tutti i contatori delle prestazioni delle VM profilate (il valore predefinito è 95° percentile). Indica anche il fattore di crescita (il valore predefinito è il 30%) usato in tutti i calcoli.

![Fattore di crescita e valori percentili usati](media/hyper-v-deployment-planner-run/growth-factor-max-percentile-value.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Raccomandazioni con la larghezza di banda disponibile come input
![Panoramica della profilatura con input della larghezza di banda](media/hyper-v-deployment-planner-analyze-report/profiling-overview-bandwidth-input-h2a.png)

Si possono verificare situazioni in cui non è possibile impostare una larghezza di banda oltre un dato valore di Mbps per la replica di Site Recovery. Lo strumento consente di immettere la larghezza di banda disponibile (con il parametro -Bandwidth durante la generazione di report) e determinare il valore RPO ottenibile in minuti. Con questo valore RPO ottenibile, è possibile stabilire se effettuare il provisioning di larghezza di banda aggiuntiva o se è sufficiente una soluzione di ripristino di emergenza con questo valore RPO.

![RPO ottenibile](media/hyper-v-deployment-planner-analyze-report/achivable-rpo-h2a.PNG)

## <a name="vm-storage-placement-recommendation"></a>Raccomandazione per la selezione host di archiviazione delle VM 
![Selezione host di archiviazione delle VM](media/hyper-v-deployment-planner-analyze-report/vm-storage-placement-h2a.png)

**Tipo di archiviazione su disco**: account di archiviazione standard o premium usato per replicare tutte le macchine virtuali corrispondenti indicate nella colonna **macchine virtuali da posizionare**.

**Prefisso suggerito**: il prefisso di tre caratteri che può essere usato per la denominazione dell'account di archiviazione. È possibile usare un prefisso personalizzato, ma il suggerimento dello strumento segue la [convenzione di denominazione delle partizioni per gli account di archiviazione](https://aka.ms/storage-performance-checklist).

**Nome account suggerito**: il nome dell'account di archiviazione dopo avere incluso il prefisso suggerito. Sostituire il nome nelle parentesi acute (< e >) con l'input personalizzato.

**Account di archiviazione log**: tutti i log di replica vengono archiviati in un account di archiviazione standard. Per le VM che eseguono la replica in un account di archiviazione Premium, configurare un account di archiviazione Standard aggiuntivo per l'archiviazione log. Un singolo account di archiviazione log Standard può essere usato da più account di archiviazione di replica Premium. Le VM replicate negli account di archiviazione Standard usano lo stesso account di archiviazione per i log.

**Nome account log suggerito**: il nome dell'account log di archiviazione dopo avere incluso il prefisso suggerito. Sostituire il nome nelle parentesi acute (< e >) con l'input personalizzato.

**Riepilogo di posizionamento**: un riepilogo del carico totale delle macchine virtuali nell'account di archiviazione al momento della replica e del failover di test o del failover. Il riepilogo include gli elementi seguenti:

* Numero totale di VM mappate all'account di archiviazione. 
* Numero totale di operazioni di I/O al secondo in lettura/scrittura per tutte le VM inserite in questo account di archiviazione.
* Numero totale di operazioni di I/O al secondo in scrittura (replica).
* Dimensioni di installazione totali per tutti i dischi.
* Numero totale di dischi.

**Macchine virtuali da posizionare**: un elenco di tutte le macchine virtuali da inserire nell'account di archiviazione specificato per ottenere uso e prestazioni ottimali.

## <a name="compatible-vms"></a>VM compatibili
Il report di Excel generato da Site Recovery Deployment Planner fornisce tutti i dettagli relativi alle macchine virtuali compatibili nel foglio "Compatible VMs" (Macchine virtuali compatibili).

![VM compatibili](media/hyper-v-deployment-planner-analyze-report/compatible-vms-h2a.png)

**Nome macchina virtuale**: il nome della macchina virtuale usato in VMListFile quando viene generato un report. Questa colonna elenca anche i dischi (dischi rigidi virtuali) collegati alle macchine virtuali. I nomi includono i nomi host Hyper-V in cui si trovavano le macchine virtuali quando lo strumento le ha rilevate durante il periodo di profilatura.

**Compatibilità macchina virtuale**: I valori sono **Sì** e **Sì**\*. **Yes**\* è per i casi in cui la macchina virtuale è idonea per i [dischi SSD Premium](../virtual-machines/windows/disks-types.md). In questo caso, il disco profilato per valori elevati di operazioni di I/O al secondo o varianza rientra in una categoria di dimensioni del disco Premium più elevata rispetto a quella mappata al disco. In base alle dimensioni, l'account di archiviazione decide a quale tipo di disco di archiviazione Premium mappare un disco: 
* <128 GB rientrano nella categoria P10.
* Da 128 GB a 256 GB rientrano nella categoria P15.
* Da 256 GB a 512 GB rientrano nella categoria P20.
* Da 512 GB a 1.024 GB rientrano nella categoria P30.
* Da 1.025 GB a 2.048 GB rientrano nella categoria P40.
* Da 2.049 GB a 4.095 GB rientrano nella categoria P50.

Se in virtù delle caratteristiche del carico di lavoro un disco appartiene alla categoria P20 o P30, ma le dimensioni ne determinano l'associazione a un tipo di disco di archiviazione Premium inferiore, ad esempio, lo strumento contrassegna tale VM con **Yes**\* (Sì). Lo strumento consiglia anche di modificare le dimensioni del disco di origine per renderlo idoneo al tipo di disco di archiviazione Premium raccomandato oppure di modificare il tipo di disco di destinazione dopo il failover.

**Tipo di archiviazione**: standard o premium.

**Prefisso suggerito**: il prefisso di tre caratteri dell'account di archiviazione.

**Account di archiviazione**: il nome che usa il prefisso dell'account di archiviazione suggerito.

**Picco di operazioni di I/O al secondo in lettura/scrittura (con fattore di crescita)** : il picco di operazioni di I/O al secondo in lettura/scrittura del carico di lavoro nel disco (il valore predefinito è 95° percentile), insieme al fattore di crescita futuro (il valore predefinito è 30%). Il numero totale di operazioni di I/O al secondo in lettura/scrittura di una VM non è sempre la somma delle operazioni di I/O al secondo in lettura/scrittura dei singoli dischi della VM. Il picco di operazioni di I/O al secondo in lettura/scrittura della macchina virtuale è il picco della somma delle operazioni di I/O al secondo in lettura/scrittura dei singoli dischi per ogni minuto del periodo di profilatura.

**Picco di varianza dati in MB/s (con fattore di crescita)** : il picco di varianza nel disco (il valore predefinito è 95° percentile), insieme al fattore di crescita futuro (il valore predefinito è 30%). La varianza totale dei dati della VM non è sempre la somma delle varianze dei dati dei singoli dischi della VM. Il picco della varianza dei dati della VM corrisponde al picco della somma delle varianze dei singoli dischi per ogni minuto del periodo di profilatura.

**Dimensioni macchina virtuale di Azure**: le dimensioni ideali della macchina virtuale mappata dei Servizi cloud di Azure per questa macchina virtuale locale. Il mapping si basa sulla memoria, sul numero di dischi/core/schede di interfaccia di rete e operazioni di I/O al secondo in lettura/scrittura della VM locale. La raccomandazione si riferisce sempre alle dimensioni minime della macchina virtuale di Azure corrispondenti a tutte le caratteristiche della macchina virtuale locale.

**Numero di dischi**: il numero totale dei dischi rigidi virtuali (VHD) nella macchina virtuale.

**Dimensioni disco (GB)** : le dimensioni totali di tutti i dischi della macchina virtuale. Lo strumento indica anche le dimensioni dei singoli dischi della VM.

**Core**: il numero di core CPU nella macchina virtuale.

**Memoria (MB)** : la RAM della macchina virtuale.

**Schede di interfaccia di rete**: il numero di schede di interfaccia di rete della macchina virtuale.

**Tipo di avvio**: il tipo di avvio della macchina virtuale. Può essere BIOS o EFI.

## <a name="incompatible-vms"></a>VM incompatibili
Il report di Excel generato da Site Recovery Deployment Planner fornisce tutti i dettagli relativi alle macchine virtuali incompatibili nel foglio "Incompatible VMs" (Macchine virtuali incompatibili).

![VM incompatibili](media/hyper-v-deployment-planner-analyze-report/incompatible-vms-h2a.png)

**Nome macchina virtuale**: il nome della macchina virtuale usato in VMListFile quando viene generato un report. Questa colonna elenca anche i dischi (dischi rigidi virtuali) collegati alle macchine virtuali. I nomi includono i nomi host Hyper-V in cui si trovavano le macchine virtuali quando lo strumento le ha rilevate durante il periodo di profilatura.

**Compatibilità macchina virtuale**: indica perché la macchina virtuale specificata è incompatibile per l'uso con Site Recovery. I motivi vengono descritti per ogni disco incompatibile della VM e, in base ai [limiti di archiviazione](https://aka.ms/azure-storage-scalbility-performance) pubblicati, possono essere uno dei seguenti:

* Le dimensioni del disco sono maggiori di 4.095 GB. Archiviazione di Azure attualmente non supporta dischi dati di dimensioni superiori a 4.095 GB.

* Il disco del sistema operativo ha dimensioni superiori a 2.047 GB per una macchina virtuale di generazione 1 (tipo di avvio BIOS). Site Recovery non supporta dischi del sistema operativo di dimensioni superiori a 2.047 GB per le macchine virtuali di generazione 1.

* Il disco del sistema operativo ha dimensioni superiori a 300 GB per una macchina virtuale di generazione 2 (tipo di avvio EFI). Site Recovery non supporta dischi del sistema operativo di dimensioni superiori a 300 GB per le macchine virtuali di generazione 2.

* Non sono supportati nomi di macchina virtuale contenenti i caratteri “” [] `. Lo strumento non può ottenere i dati profilati di macchine virtuali che contengono questi caratteri nel nome. 

* Un disco rigido virtuale è condiviso da due o più macchine virtuali. Azure non supporta le macchine virtuali con un disco rigido virtuale condiviso.

* Le macchine virtuali con Virtual Fiber Channel non sono supportate. Site Recovery non supporta le macchine virtuali con Virtual Fiber Channel.

* Un cluster Hyper-V non contiene un gestore di replica. Site Recovery non supporta una macchina virtuale in un cluster Hyper-V se il gestore di replica Hyper-V non è configurato per il cluster.

* La macchina virtuale non offre disponibilità elevata. Site Recovery non supporta una macchina virtuale di un nodo del cluster Hyper-V con dischi rigidi virtuali archiviati nel disco locale invece che nel disco del cluster. 

* Le dimensioni totali della macchina virtuale (replica + failover di test) superano il limite supportato dall'account di archiviazione Premium (35 TB). Questa incompatibilità si verifica in genere quando un singolo disco della VM ha una caratteristica di prestazioni che supera i limiti massimi supportati da Azure o da Site Recovery per l'archiviazione Standard. In questo caso la VM rientra nell'area dell'archiviazione Premium. Le dimensioni massime supportate da un account di archiviazione Premium sono tuttavia pari a 35 TB. Non è possibile proteggere una singola VM su più account di archiviazione. 

    Quando un failover di test viene eseguito in una macchina virtuale protetta e per tale failover di test è configurato un disco non gestito, il failover viene eseguito nello stesso account di archiviazione in cui è in corso la replica. In questo caso è necessaria una quantità aggiuntiva di spazio di archiviazione equivalente a quella necessaria per la replica. La replica può così proseguire e il failover di test può venire completato in parallelo. Quando per il failover di test è configurato un disco gestito, non è necessario allocare spazio aggiuntivo per la macchina virtuale usata per il failover di test.

* Le operazioni di I/O al secondo di origine superano il limite supportato di archiviazione di 7.500 operazioni per disco.

* Le operazioni di I/O al secondo di origine superano il limite supportato di archiviazione di 80.000 operazioni per VM.

* La varianza media dei dati della VM di origine supera il limite supportato da Site Recovery di 10 MB/s per dimensioni I/O medie.

* Il numero medio di operazioni di I/O al secondo in scrittura effettive della VM di origine supera il limite supportato da Site Recovery di 840 operazioni.

* L'archiviazione snapshot calcolata supera il limite supportato di 10 TB.

**Picco di operazioni di I/O al secondo in lettura/scrittura (con fattore di crescita)** : il picco di operazioni di I/O al secondo del carico di lavoro nel disco (il valore predefinito è 95° percentile) insieme al fattore di crescita futuro (il valore predefinito è 30%). Il numero totale di operazioni di I/O al secondo in lettura/scrittura della VM non è sempre la somma delle operazioni di I/O al secondo in lettura/scrittura dei singoli dischi della VM. Il picco di operazioni di I/O al secondo in lettura/scrittura della macchina virtuale è il picco della somma delle operazioni di I/O al secondo in lettura/scrittura dei singoli dischi per ogni minuto del periodo di profilatura.

**Picco di varianza dati in MB/s (con fattore di crescita)** : il picco di varianza nel disco (il valore predefinito è 95° percentile), insieme al fattore di crescita futuro (il valore predefinito è 30%). Si noti che la varianza totale dei dati della VM non è sempre la somma delle varianze dei dati dei singoli dischi della VM. Il picco della varianza dei dati della VM corrisponde al picco della somma delle varianze dei singoli dischi per ogni minuto del periodo di profilatura.

**Numero di dischi**: il numero totale dei VHD nella macchina virtuale.

**Dimensioni disco (GB)** : le dimensioni di installazione totali di tutti i dischi della macchina virtuale. Lo strumento indica anche le dimensioni dei singoli dischi della VM.

**Core**: il numero di core CPU nella macchina virtuale.

**Memoria (MB)** : la quantità di RAM della macchina virtuale.

**Schede di interfaccia di rete**: il numero di schede di interfaccia di rete della macchina virtuale.

**Tipo di avvio**: il tipo di avvio della macchina virtuale. Può essere BIOS o EFI.

## <a name="azure-site-recovery-limits"></a>Limiti di Azure Site Recovery
La tabella seguente indica i limiti di Site Recovery. Questi limiti si basano su test, ma non possono coprire tutte le possibili combinazioni di I/O delle applicazioni. I risultati effettivi possono variare in base alla combinazione di I/O delle applicazioni. Per risultati ottimali, anche dopo la pianificazione della distribuzione eseguire test approfonditi delle applicazioni con un failover di test per ottenere il quadro reale delle prestazioni dell'applicazione.
 
**Destinazione archiviazione di replica** | **Dimensioni medie I/O VM di origine** |**Varianza dati media VM di origine** | **Varianza dati totale giornaliera VM di origine**
---|---|---|---
Archiviazione standard | 8 KB | 2 MB/s per VM | 168 GB per VM
Archiviazione Premium | 8 KB  | 5 MB/s per VM | 421 GB per VM
Archiviazione Premium | 16 KB o superiore| 20 MB al secondo per macchina virtuale | 1684 GB per ogni macchina virtuale

Questi limiti rappresentano valori medi presupponendo una sovrapposizione di I/O del 30%. Site Recovery può gestire una velocità effettiva maggiore in base alla percentuale di sovrapposizione, alle dimensioni di scrittura maggiori e all'effettivo I/O del carico di lavoro. I numeri precedenti presuppongono un backlog tipico di circa cinque minuti, ovvero i dati, dopo essere stati caricati, verranno elaborati e verrà creato un punto di ripristino entro cinque minuti.

## <a name="on-premises-storage-requirement"></a>Requisito di archiviazione locale

Il foglio di lavoro indica i requisiti di spazio di archiviazione totale disponibile per ogni volume dei server Hyper-V (dove si trovano i dischi rigidi virtuali) per la corretta esecuzione della replica iniziale e della replica differenziale. Prima di abilitare la replica, aggiungere ai volumi lo spazio di archiviazione necessario, in modo da garantire che la replica non provochi tempi di inattività indesiderati per le applicazioni di produzione. 

  Site Recovery Deployment Planner identifica i requisiti ottimali per lo spazio di archiviazione in base alle dimensioni dei dischi rigidi virtuali e alla larghezza di banda di rete usata per la replica.

![Requisito di archiviazione locale](media/hyper-v-deployment-planner-analyze-report/on-premises-storage-requirement-h2a.png)

### <a name="why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication"></a>Perché è necessario spazio disponibile nel server Hyper-V per la replica?
* Quando si abilita la replica di una macchina virtuale, Site Recovery crea uno snapshot di ogni disco rigido virtuale della macchina virtuale per la replica iniziale. Mentre è in corso la replica iniziale, le nuove modifiche vengono scritte nei dischi dall'applicazione. Site Recovery tiene traccia delle modifiche differenziali nei file di log e ciò richiede spazio di archiviazione aggiuntivo. Fino al completamento della replica iniziale, i file di log vengono archiviati in locale. 

    Se non è disponibile spazio sufficiente per i file di log e lo snapshot (AVHDX), la replica passa in modalità di risincronizzazione e non viene mai completata. Nel peggiore dei casi, è necessario spazio disponibile aggiuntivo pari alle dimensioni del disco rigido virtuale per la replica iniziale.
* Dopo aver completato la replica iniziale, viene avviata la replica differenziale. Site Recovery tiene traccia delle modifiche differenziali nei file di log che vengono archiviati nel volume in cui si trovano i dischi rigidi virtuali della macchina virtuale. Questi file di log vengono replicati in Azure in base alla frequenza di copia configurata. A seconda della larghezza di banda di rete disponibile, può essere necessaria una determinata quantità di tempo per la replica dei file di log in Azure. 

    Se non è disponibile spazio sufficiente per archiviare i file di log, la replica viene sospesa. Lo stato della replica della macchina virtuale passa a "Risincronizzazione necessaria".
* Se la larghezza di banda di rete non è sufficiente per eseguire il push dei file di log in Azure, i file di log si accumulano nel volume. Nello scenario peggiore, quando le dimensioni dei file di log aumentano fino a raggiungere il 50% delle dimensioni del disco rigido virtuale, la replica della macchina virtuale passa a "Risincronizzazione necessaria". Nel peggiore dei casi, è necessario uno spazio disponibile aggiuntivo pari al 50% delle dimensioni del disco rigido virtuale per la replica differenziale.

**Host Hyper-V**: l'elenco dei server Hyper-V profilati. Se un server fa parte di un cluster Hyper-V, tutti i nodi del cluster vengono raggruppati insieme.

**Volume (percorso VHD)** : ogni volume di un host Hyper-V in cui sono presenti VHD o VHDX. 

**Spazio disponibile (GB)** : lo spazio disponibile nel volume.

**Spazio di archiviazione totale necessario nel volume (GB)** : lo spazio di archiviazione totale disponibile necessario nel volume per la corretta esecuzione della replica iniziale e della replica differenziale. 

**Spazio di archiviazione aggiuntivo totale di cui eseguire il provisioning nel volume per la corretta esecuzione della replica (GB)** : indica lo spazio aggiuntivo totale di cui è necessario eseguire il provisioning nel volume per la corretta esecuzione della replica iniziale e della replica differenziale.

## <a name="initial-replication-batching"></a>Suddivisione in batch per la replica iniziale 

### <a name="why-do-i-need-initial-replication-batching"></a>Perché è necessaria la suddivisione in batch per la replica iniziale?
Se tutte le macchine virtuali vengono protette contemporaneamente, lo spazio di archiviazione disponibile richiesto è molto maggiore. In assenza di spazio sufficiente, la replica delle macchine virtuali passa alla modalità di risincronizzazione. Anche il requisito di larghezza di banda di rete per completare correttamente la replica iniziale di tutte le macchine virtuali contemporaneamente è molto maggiore. 

### <a name="initial-replication-batching-for-a-selected-rpo"></a>Suddivisione in batch per la replica iniziale per l'obiettivo del punto di ripristino selezionato
Questo foglio di lavoro fornisce una vista dettagliata di ogni batch per la replica iniziale. Per ogni obiettivo del punto di ripristino viene creato un foglio distinto relativo alla suddivisione in batch per la replica iniziale. 

Dopo aver seguito le raccomandazioni relative ai requisiti per lo spazio di archiviazione locale per ogni volume, le informazioni principali necessarie per la replica sono costituite dall'elenco delle macchine virtuali che è possibile proteggere in parallelo. Queste macchine virtuali vengono raggruppate in un batch e possono essere presenti più batch. Proteggere le macchine virtuali in base all'ordine dei batch specificato. Proteggere prima le VM del batch 1. Dopo aver completato la replica iniziale, proteggere le VM del batch 2 e così via. È possibile ottenere l'elenco dei batch e delle macchine virtuali corrispondenti da questo foglio. 

![Dettagli della suddivisione in batch per la replica iniziale](media/hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo-h2a.png)

![Altri dettagli della suddivisione in batch per la replica iniziale](media/hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo2-h2a.png)

### <a name="each-batch-provides-the-following-information"></a>Ogni batch fornisce le informazioni seguenti 
**Host Hyper-V**: l'host Hyper-V della macchina virtuale da proteggere.

**Macchina virtuale**: La macchina virtuale da proteggere. 

**Commenti**: se sono necessarie azioni per un volume specifico di una macchina virtuale, i commenti vengono indicati qui. Se, ad esempio, non è disponibile spazio sufficiente in un volume, nel commento viene indicato di aggiungere spazio per proteggere la macchina virtuale.

**Volume (percorso VHD)** : il nome del volume in cui si trovano i VHD della macchina virtuale. 

**Spazio libero disponibile nel volume (GB)** : lo spazio su disco disponibile nel volume per la macchina virtuale. Per il calcolo dello spazio disponibile nei volumi, viene considerato lo spazio su disco usato per la replica differenziale dalle macchine virtuali dei batch precedenti i cui dischi rigidi virtuali si trovano nello stesso volume. 

Si presupponga ad esempio che le macchine virtuali VM1, VM2 e VM3 si trovino nel volume E:\VHDpath. Prima della replica, lo spazio disponibile nel volume è di 500 GB. VM1 fa parte del batch 1, VM2 fa parte del batch 2 e VM3 fa parte del batch 3. Per VM1, lo spazio disponibile è 500 GB. Per VM2, lo spazio disponibile è 500 meno lo spazio su disco necessario per la replica differenziale per VM1. Se VM1 richiede 300 GB di spazio per la replica differenziale, lo spazio disponibile per VM2 è 500 GB - 300 GB = 200 GB. Analogamente, VM2 richiede 300 GB per la replica differenziale. Lo spazio disponibile per VM3 è 200 GB - 300 GB = -100 GB.

**Spazio di archiviazione necessario nel volume per la replica iniziale (GB)** : lo spazio di archiviazione disponibile necessario nel volume per la macchina virtuale per la replica iniziale.

**Spazio di archiviazione necessario nel volume per la replica differenziale (GB)** : lo spazio di archiviazione disponibile necessario nel volume per la macchina virtuale per la replica differenziale.

**Spazio di archiviazione aggiuntivo necessario in base al disavanzo per evitare errori di replica (GB)** : lo spazio di archiviazione aggiuntivo necessario nel volume per la macchina virtuale. Corrisponde al requisito massimo di spazio di archiviazione disponibile per la replica iniziale e la replica differenziale meno lo spazio disponibile nel volume.

**Larghezza di banda minima necessaria per la replica iniziale (Mbps)** : La larghezza di banda minima necessaria per la replica iniziale per la macchina virtuale.

**Larghezza di banda minima necessaria per la replica differenziale (Mbps)** : La larghezza di banda minima necessaria per la replica differenziale per la macchina virtuale.

### <a name="network-utilization-details-for-each-batch"></a>Dettagli relativi all'utilizzo di rete per ogni batch 
La tabella di ogni batch fornisce un riepilogo dell'utilizzo di rete del batch.

**Larghezza di banda disponibile per il batch**: la larghezza di banda disponibile per il batch dopo aver considerato la larghezza di banda per la replica differenziale del batch precedente.

**Larghezza di banda approssimativa disponibile per la replica iniziale del batch**: la larghezza di banda disponibile per la replica iniziale delle macchine virtuali del batch. 

**Larghezza di banda approssimativa utilizzata per la replica differenziale del batch**: la larghezza di banda necessaria per la replica differenziale delle macchine virtuali del batch. 

**Tempo stimato per la replica iniziale per batch (hh:mm)** : il tempo stimato per la replica iniziale in ore:minuti.



## <a name="next-steps"></a>Passaggi successivi
Vedere altre informazioni sulla [stima dei costi](hyper-v-deployment-planner-cost-estimation.md).
