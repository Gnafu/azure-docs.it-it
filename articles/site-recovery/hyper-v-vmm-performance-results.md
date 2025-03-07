---
title: Eseguire test dei risultati della replica delle macchine virtuali Hyper-V in cloud VMM in un sito secondario con Azure Site Recovery | Microsoft Docs
description: Questo articolo fornisce informazioni sui test delle prestazioni per la replica di macchine virtuali Hyper-V in cloud VMM in un sito secondario tramite Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi
ms.openlocfilehash: a7413b2dcb24a42092eb2af9816b1d29a8306e19
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68377219"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>Eseguire test dei risultati della replica Hyper-V in un sito secondario


Questo articolo fornisce i risultati dei test delle prestazioni durante la replica di macchine virtuali Hyper-V in cloud System Center Virtual Machine Manager (VMM) in un data center secondario.

## <a name="test-goals"></a>Obiettivi di test

L'obiettivo del test è analizzare le prestazioni di Site Recovery durante la replica in uno stato stazionario.

- La replica dello stato stazionario si verifica quando le macchine virtuali hanno completato la replica iniziale e sincronizzano le modifiche differenziali.
- È importante misurare le prestazioni usando lo stato stazionario perché è lo stato in cui resta la maggior parte delle macchine virtuali a meno che non si verifichino interruzioni impreviste.
- La distribuzione di test è costituita da due siti locali con un server VMM in ciascuno di essi. Questa distribuzione di test è tipica di una distribuzione sede centrale/filiale, con la sede centrale che funge da sito primario e la filiale che funge da sito secondario o di ripristino.

## <a name="what-we-did"></a>Passaggi eseguiti

Passaggi necessari per il superamento del test:

1. Creare macchine virtuali tramite i modelli VMM.
2. Avviare le macchine virtuali e acquisire le metriche delle prestazioni di acquisizione dei dati di base per 12 ore.
3. Creare i cloud nei server VMM primario e di ripristino.
4. Configurare la replica in Site Recovery, incluso il mapping tra cloud di origine e di ripristino.
5. Abilitare la protezione per le macchine virtuali e consentire il completamento della replica iniziale.
6. Attendere un paio d'ore per la stabilizzazione del sistema.
7. Acquisire le metriche delle prestazioni per 12 ore. Le macchine virtuali sono rimaste in uno stato di replica previsto per queste 12 ore.
8. Misurare il differenziale tra le metriche delle prestazioni di base e le metriche delle prestazioni di replica.


## <a name="primary-server-performance"></a>Prestazioni del server primario

* Replica Hyper-V, usata da Site Recovery, tiene traccia in modo asincrono delle modifiche apportate a un file di log con sovraccarico di archiviazione minimo nel server primario.
* Replica Hyper-V usa la cache in memoria autonoma per ridurre il sovraccarico di IOPS per la traccia. Archivia scritture nel disco VHDX in memoria e le scarica nel file di log prima dell'invio del log al sito di ripristino del log. Lo scaricamento del disco avviene anche se le scritture raggiungono un limite predeterminato.
* Il grafico seguente mostra il sovraccarico di IOPS nello stato stazionario per la replica. Si può notare che il sovraccarico di operazioni di I/O al secondo dovuto alla replica è pari al 5% circa, quindi abbastanza basso.

  ![Risultati sito primario](./media/hyper-v-vmm-performance-results/IC744913.png)

Replica Hyper-V usa la memoria nel server primario per ottimizzare le prestazioni del disco. Come illustrato nel grafico seguente, il sovraccarico della memoria in tutti i server del cluster primario è marginale. Il sovraccarico della memoria mostrato è la percentuale di memoria usata dalla replica rispetto alla memoria totale installata sul server Hyper-V.

![Risultati sito primario](./media/hyper-v-vmm-performance-results/IC744914.png)

Replica Hyper-V ha un sovraccarico di CPU minimo. Come illustrato nel grafico, il sovraccarico della replica è tra il 2 e il 3%.

![Risultati sito primario](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Prestazioni del server secondario

Replica Hyper-V usa una piccola quantità di memoria nel server di ripristino per ottimizzare il numero di operazioni di archiviazione. Il grafico riepiloga l'utilizzo della memoria nel server di ripristino. Il sovraccarico della memoria mostrato è la percentuale di memoria usata dalla replica rispetto alla memoria totale installata sul server Hyper-V.

![Risultati sito secondario](./media/hyper-v-vmm-performance-results/IC744916.png)

La quantità di operazioni di I/O nel sito di ripristino è una funzione del numero di operazioni di scrittura nel sito primario. Ora verranno esaminate le operazioni di I/O totali sul sito di ripristino rispetto alle operazioni di I/O totali e le operazioni di scrittura nel sito primario. I grafici mostrano che il totale di IOPS nel sito di ripristino è

* Circa 1,5 volte la scrittura di IOPS sul database primario.
* Circa il 37% del totale di IOPS nel sito primario.

![Risultati sito secondario](./media/hyper-v-vmm-performance-results/IC744917.png)

![Risultati sito secondario](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>Risultati dell'uso di rete

Una media di 275 MB al secondo di larghezza di banda è stata usata tra i nodi primario e di ripristino (con compressione abilitata) su una larghezza di banda esistente di 5 GB al secondo.

![Risultati utilizzo rete](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>Effetto sulle prestazioni della macchina virtuale

Una considerazione importante è l'impatto della replica sui carichi di lavoro di produzione in esecuzione nelle macchine virtuali. Se viene eseguito il provisioning adeguato del sito primario per la replica, l’impatto sui carichi di lavoro dovrebbe essere nullo. Il meccanismo di traccia a basso utilizzo di risorse di Replica Hyper-V assicura che i carichi di lavoro in esecuzione nelle macchine virtuali non subiscano ripercussioni durante la replica nello stato stazionario. Tale condizione è illustrata nei grafici seguenti.

Questo grafico mostra le operazioni di I/O al secondo eseguite dalle macchine virtuali con carichi di lavoro diversi prima e dopo l'abilitazione della replica. È possibile osservare che non esiste alcuna differenza tra i due.

![Risultati effetto replica](./media/hyper-v-vmm-performance-results/IC744920.png)

Il grafico seguente mostra la velocità effettiva della macchine virtuali con carichi di lavoro diversi prima e dopo l'abilitazione della replica. È possibile osservare che la replica non ha alcun impatto significativo.

![Risultati effetti replica](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Conclusione

I risultati mostrano chiaramente che Site Recovery, grazie a Replica Hyper-V, si adatta bene con un sovraccarico minimo per un cluster di grandi dimensioni. Site Recovery consente di eseguire in modo semplice distribuzione, replica, gestione e monitoraggio. Replica Hyper-V fornisce l'infrastruttura necessaria per la corretta scalabilità della replica. 

## <a name="test-environment-details"></a>Ambiente di test nel dettaglio

### <a name="primary-site"></a>Sito primario

* Il sito primario include un cluster contenente cinque server Hyper-V con 470 macchine virtuali.
* Le macchine virtuali eseguono diversi carichi di lavoro e per tutte la protezione di Site Recovery è abilitata.
* L’archiviazione per il nodo del cluster è fornita da una rete SAN iSCSI. Modello – Hitachi HUS130.
* Ogni server del cluster ha quattro schede di rete (NIC) di 1 Gbps ciascuna.
* Due delle schede di rete sono connesse a una rete privata iSCSI e due sono connesse a una rete aziendale esterna. Una delle reti esterne è riservata alle comunicazioni del cluster.

![Requisiti hardware principali](./media/hyper-v-vmm-performance-results/IC744922.png)

| Server | RAM | Modello | Processore | Numero di processori | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Server Hyper-V nel cluster: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25 ha 256 |Dell ™ PowerEdge ™ R820 |CPU Intel(R) Xeon(R) E5-4620 0 \@ 2,20 GHz |4 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + ruolo Hyper-V |
| Server VMM |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### <a name="secondary-site"></a>Sito secondario

* Il sito secondario ha un cluster di failover a sei nodi
* L’archiviazione per il nodo del cluster è fornita da una rete SAN iSCSI. Modello – Hitachi HUS130.

![Specifiche hardware principali](./media/hyper-v-vmm-performance-results/IC744923.png)

| Server | RAM | Modello | Processore | Numero di processori | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Server Hyper-V nel cluster: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell ™ PowerEdge ™ R720 |CPU Intel(R) Xeon(R) E5-2630 0 \@ 2,30 GHz |2 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + ruolo Hyper-V |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |CPU Intel(R) Xeon(R) E5-4620 0 \@ 2,20 GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + ruolo Hyper-V |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |CPU Intel(R) Xeon(R) E5-4620 0 \@ 2,20 GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + ruolo Hyper-V |
| Server VMM |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### <a name="server-workloads"></a>Carichi di lavoro server

* A scopo di test sono stati scelti i carichi di lavoro comunemente utilizzati negli scenari aziendali dei clienti.
* Si utilizza [IOMeter](http://www.iometer.org) con le caratteristiche del carico di lavoro riepilogate in tabella per la simulazione.
* Tutti i profili IOMeter sono impostati per scrivere byte casuali per simulare modelli  di scrittura dei casi peggiori per i carichi di lavoro.

| Carico di lavoro | Dimensioni I/O (KB) | % accesso | % lettura | I/O in sospeso | Modello I/O |
| --- | --- | --- | --- | --- | --- |
| File server |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |Tutti 100% casuale |
| SQL Server (volume 1)<br />SQL Server (volume 2) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% casuale<br />100% sequenziale |
| Exchange |32 |100% |67% |8 |100% casuale |
| Workstation/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |Entrambi 100% casuale |
| File Server Web |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |Tutti 75% casuale |

### <a name="vm-configuration"></a>Configurazione della macchina virtuale

* 470 macchine virtuali nel cluster primario.
* Tutte le macchine virtuali con disco VHDX.
* Macchine virtuali con carichi di lavoro in esecuzione riepilogati nella tabella. Tutti sono stati creati con i modelli VMM.

| Carico di lavoro | N. di macchine virtuali | RAM minima (GB) | RAM massima (GB) | Dimensioni disco logico (GB) per macchina virtuale | Numero massimo di IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| File Server |50 |1 |2 |552 |22 |
| VDI |149 |0,5 |1 |80 |6 |
| Server Web |149 |0,5 |1 |80 |6 |
| TOTALE |470 | | |96,83 TB |4108 |

### <a name="site-recovery-settings"></a>Impostazioni di Site Recovery

* Site Recovery è stato configurato per la protezione da locale a locale
* Il server VMM ha quattro cloud configurati contenenti i server del cluster Hyper-V e le macchine virtuali.

| Cloud VMM primario | Macchine virtuali protette | Frequenza di replica | Punti di ripristino aggiuntivi |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 min |Nessuna |
| PrimaryCloudRpo30s |47 |30 secondi |Nessuna |
| PrimaryCloudRpo30sArp1 |47 |30 secondi |1 |
| PrimaryCloudRpo5m |235 |5 min |Nessuna |

### <a name="performance-metrics"></a>Metriche delle prestazioni

Nella tabella vengono riepilogate le metriche delle prestazioni e i contatori misurati nella distribuzione.

| Metrica | Contatore |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| Memoria disponibile |\Memoria\MByte disponibili |
| Input/output al secondo |\DiscoFisico(_Totale)\Trasferimenti disco/secondo |
| Operazioni di lettura VM (IOPS) al secondo |\Dispositivo dispositivo di archiviazione virtuale\<(VHD >) \Byte letti/sec |
| Operazioni di scrittura VM (IOPS) al secondo |Dispositivo di archiviazione virtuale \dispositivo\<(VHD >) \Byte scritti operazioni/sec |
| Velocità effettiva lettura VM |Dispositivo di archiviazione virtuale \dispositivo\<(> VHD) \Byte letti/sec |
| Velocità effettiva di scrittura VM |Dispositivo di archiviazione virtuale \dispositivo\<(> VHD) \Byte scritti/sec |

## <a name="next-steps"></a>Passaggi successivi

[Configurare la replica](hyper-v-vmm-disaster-recovery.md)
