---
title: 'Azure Site Recovery: Domande frequenti | Microsoft Docs'
description: Questo articolo illustra le domande frequenti su Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: raynew
ms.openlocfilehash: a9c7aa2be945e4fbaa65bdd2a145d576422c5539
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491761"
---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Azure Site Recovery: domande frequenti
Questo articolo riepiloga le domande frequenti su Azure Site Recovery.</br>
Per query specifiche in Azure Site Recovery diversi scenari, vedere uno scenario specifiche domande frequenti.<br>

- [Ripristino di emergenza di macchine Virtuali di Azure in Azure](azure-to-azure-common-questions.md)
- [Ripristino di emergenza di macchine Virtuali VMware in Azure](vmware-azure-common-questions.md)
- [Ripristino di emergenza di macchine Virtuali Hyper-V in Azure](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>Generale

### <a name="what-does-site-recovery-do"></a>Quali sono le funzioni di Site Recovery?
Site Recovery favorisce l'attuazione della strategia di continuità aziendale e ripristino di emergenza mediante la gestione e l'automatizzazione delle operazioni di replica delle macchine virtuali di Azure tra le aree, delle macchine virtuali e dei server fisici locali in Azure o in un data center secondario. [Altre informazioni](site-recovery-overview.md)

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>È possibile proteggere una macchina virtuale con un disco di Docker?

No, questo scenario non è supportato.

## <a name="service-providers"></a>Provider di servizi

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Per i provider di servizi, Site Recovery funziona per modelli di infrastruttura dedicati e condivisi?
Sì, Site Recovery supporta i modelli di infrastruttura dedicati e condivisi.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Per i provider di servizi l'identità del tenant è condivisa con il servizio Site Recovery?
No. L'identità del tenant rimane anonima. Per i tenant non è necessario l'accesso al portale di Site Recovery. Solo l'amministratore del provider di servizi interagisce con il portale.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>I dati delle applicazioni tenant potranno mai essere ricevuti da Azure?
Quando si esegue la replica tra i siti di proprietà del provider di servizi, i dati delle applicazioni non vengono mai inviati ad Azure. I dati vengono crittografati in transito e replicati direttamente tra i siti del provider di servizi.

Se si esegue la replica in Azure, i dati dell'applicazione vengono inviati all'archiviazione di Azure, ma non al servizio Site Recovery. I dati vengono crittografati in transito e rimangono crittografati in Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>I tenant riceveranno una fattura per ogni servizio Azure?
No. La fatturazione di Azure avviene direttamente con il provider di servizi. I provider di servizi sono responsabili della generazione di fatture specifiche per i tenant.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Se si esegue la replica in Azure, è sempre necessario eseguire le macchine virtuali in Azure?
No, i dati vengono replicati in archiviazione di Azure nella sottoscrizione. Quando si esegue un failover di test (un'analisi del ripristino di emergenza) o un failover effettivo, Site Recovery crea automaticamente macchine virtuali nella sottoscrizione.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>È possibile garantire isolamento a livello di tenant durante la replica in Azure?
Sì.

### <a name="what-platforms-do-you-currently-support"></a>Quali piattaforme sono attualmente supportate?
Sono supportate distribuzioni basate su Azure Pack, Cloud Platform System e System Center (2012 e versioni successive). [Alter informazioni](https://technet.microsoft.com/library/dn850370.aspx) sull'integrazione di Azure Pack e Site Recovery.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Sono supportate singole distribuzioni di Azure Pack e del server VMM?
Sì, è possibile eseguire la replica da macchine virtuali Hyper-V ad Azure o tra siti del provider di servizi.  Si noti che se si esegue la replica tra i siti del provider di servizi, l'integrazione del runbook di Azure non è disponibile.

## <a name="pricing"></a>Prezzi

### <a name="where-can-i-find-pricing-information"></a>Dove posso trovare informazioni sui prezzi?
Revisione [prezzi di Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) dettagli.


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Come è possibile fare una stima dei costi durante l'uso di Site Recovery?

È possibile usare la [calcolatore dei prezzi](https://aka.ms/asr_pricing_calculator) per stimare i costi quando si usa Site Recovery.

Per la stima dettagliata sui costi, eseguire lo strumento deployment planner per [VMware](https://aka.ms/siterecovery_deployment_planner) oppure [Hyper-V](https://aka.ms/asr-deployment-planner)e usare il [report di stima dei costi](https://aka.ms/asr_DP_costreport).


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>I dischi gestiti sono ora usati per replicare le macchine virtuali VMware e server fisici. Sono previsti addebiti aggiuntivi per l'account di archiviazione della cache con dischi gestiti?

No, non sono previsti addebiti aggiuntivi per la cache. Quando esegue la replica in account di archiviazione standard, questa risorsa di archiviazione della cache fa parte dello stesso account di archiviazione di destinazione.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Sono un utente di Azure Site Recovery da più di un mese. Posso ottenere ancora i primi 31 giorni gratuiti per ogni istanza protetta?

Sì. Per ogni istanza protetta non vengono addebitati costi per Azure Site Recovery per i primi 31 giorni. Ad esempio, se hai protetto 10 istanze negli ultimi 6 mesi e Connetti un'istanza dall'11 ad Azure Site Recovery, non esistono applicato alcun addebito per l'istanza 11 per i primi 31 giorni. Le prime 10 istanze continuano a generare costi di Azure Site Recovery, dal momento che hai protette per più di 31 giorni.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Durante i primi 31 giorni, sono previsti altri addebiti per Azure?

Sì, anche se Site Recovery è gratuito durante i primi 31 giorni di un'istanza protetta, si potrebbero subire addebiti per archiviazione di Azure, le transazioni di archiviazione e trasferimento dei dati. Per una macchina virtuale ripristinata possono venire addebitati anche i costi di calcolo di Azure.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>È previsto un costo per eseguire failover di drill/test di ripristino di emergenza?

Non sono previsti costi separati per le esercitazioni sul ripristino di emergenza. Saranno presenti i costi di calcolo dopo aver creata la macchina virtuale dopo il failover di test.



## <a name="security"></a>Security

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>I dati di replica vengono inviati al servizio Site Recovery?
No, Site Recovery non intercetta i dati replicati né raccoglie informazioni su ciò che è in esecuzione sulle macchine virtuali o sui server fisici.
I dati di replica vengono scambiati tra host Hyper-V, hypervisor VMware o server fisici e Archiviazione di Azure o il sito secondario. Site Recovery non è in grado di intercettare i dati. Al servizio Site Recovery vengono inviati solo i metadati necessari per gestire la replica e il failover.  

Site Recovery è certificato ISO 27001:2013, 27018, HIPAA e DPA e le valutazioni SOC2 e FedRAMP JAB sono in fase di completamento.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Per motivi di conformità, è necessario che anche i metadati locali restino all'interno della stessa area geografica. Site Recovery può offrire vantaggi?
Sì. Quando si crea un insieme di credenziali di Site Recovery in un'area, tutti i metadati necessari per abilitare e gestire la replica e il failover restano all'interno dei confini di tale area geografica.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery consente di crittografare la replica?
Per la replica di macchine virtuali e server fisici tra siti locali, è supportata la crittografia in transito. Per la replica di macchine virtuali e server fisici in Azure, sono supportate sia la crittografia in transito che la [crittografia inattiva (in Azure)](https://docs.microsoft.com/azure/storage/storage-service-encryption).




## <a name="disaster-recovery"></a>Ripristino di emergenza

### <a name="what-can-site-recovery-protect"></a>Quali elementi può proteggere Site Recovery?
* **Macchine virtuali di Azure**: Site Recovery può replicare qualsiasi carico di lavoro in esecuzione in una macchina virtuale di Azure supportata
* **Macchine virtuali Hyper-V**: Site Recovery può proteggere qualsiasi carico di lavoro in esecuzione in una macchina virtuale Hyper-V.
* **Server fisici**: Site Recovery può proteggere i server fisici che eseguono Windows o Linux.
* **Macchine virtuali VMware**: Site Recovery può proteggere qualsiasi carico di lavoro in esecuzione in una macchina virtuale VMware.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Quali carichi di lavoro è possibile proteggere con Site Recovery?
È possibile usare Site Recovery per proteggere la maggior parte dei carichi di lavoro in esecuzione in una VM o in un server fisico supportati. Site Recovery fornisce il supporto per la replica compatibile con l'applicazione per consentire il ripristino delle app a uno stato intelligente. Si integra con le applicazioni Microsoft, ad esempio SharePoint, Exchange, Dynamics, SQL Server e Active Directory, e opera a stretto contatto con importanti fornitori, tra cui Oracle, SAP, IBM e Red Hat. [Altre informazioni](site-recovery-workload.md) sulla protezione del carico di lavoro.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>È possibile gestire il ripristino di emergenza per le succursali con Site Recovery?
Sì. Quando si usa Site Recovery per gestire la replica e il failover nelle succursali, si ottiene una gestione unificata e vengono visualizzati tutti i carichi di lavoro delle succursali in una posizione centrale. È possibile eseguire facilmente i failover e amministrare il ripristino di emergenza di tutte le succursali senza abbandonare la sede centrale.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Ripristino di emergenza è supportato per macchine virtuali di Azure?

Sì, Site Recovery supporta emergenza per macchine virtuali di Azure tra aree di Azure. [Esaminare le domande più comuni](azure-to-azure-common-questions.md) sul ripristino di emergenza di macchine Virtuali di Azure.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>Ripristino di emergenza è supportato per le macchine virtuali VMware?

Sì, Site Recovery supporta il ripristino di emergenza di macchine virtuali VMware in locale. [Esaminare le domande più comuni](vmware-azure-common-questions.md) per il ripristino di emergenza di macchine virtuali VMware.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Ripristino di emergenza è supportato per le macchine virtuali Hyper-V?
Sì, Site Recovery supporta il ripristino di emergenza di macchine virtuali Hyper-V in locale. [Esaminare le domande più comuni](hyper-v-azure-common-questions.md) per il ripristino di emergenza di macchine virtuali Hyper-V.

## <a name="is-disaster-recovery-supported-for-physical-servers"></a>Ripristino di emergenza è supportato per server fisici?
Sì, Site Recovery supporta il ripristino di emergenza di server fisici locali che eseguono Windows e Linux in Azure o in un sito secondario. Altre informazioni sui requisiti di ripristino di emergenza [Azure](vmware-physical-azure-support-matrix.md#replicated-machines)e di ottenere[un sito secondario](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
Si noti che i server fisici verranno eseguiti come macchine virtuali in Azure dopo il failover. Il failback da Azure a un server fisico locale non è attualmente supportato. È possibile eseguire solo in una macchina virtuale VMware.





## <a name="replication"></a>Replica

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>È possibile replicare tramite una VPN da sito a sito in Azure?
Azure Site Recovery replica i dati a un account di archiviazione di Azure o i dischi gestiti, su un endpoint pubblico. La replica non avviene tramite una rete VPN da sito a sito. 

### <a name="why-cant-i-replicate-over-vpn"></a>Perché non è possibile eseguire la replica su VPN?

Quando esegue la replica in Azure, il traffico di replica raggiunge gli endpoint pubblici di una risorsa di archiviazione di Azure. In questo modo è possibile solo eseguire la replica sulla rete internet pubblica con ExpressRoute (peering Microsoft o un peering pubblico esistente) e VPN non è supportata.

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>È possibile usare Riverbed SteelHeads per la replica?

Il nostro partner, Riverbed, include indicazioni dettagliate sull'uso di Azure Site Recovery. Revisione del loro [Guida alla soluzione](https://community.riverbed.com/s/article/DOC-4627).

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>È possibile usare ExpressRoute per replicare macchine virtuali in Azure?
Sì, [è possibile](concepts-expressroute-with-site-recovery.md).

- Azure Site Recovery replica i dati a una risorsa di archiviazione di Azure su un endpoint pubblico. È necessario configurare [peering Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) oppure usare un oggetto esistente [peering pubblico di](../expressroute/expressroute-circuit-peerings.md#publicpeering) (deprecato per i circuiti nuovo) per usare ExpressRoute per la replica di Site Recovery.
- Il peering Microsoft è il dominio di routing consigliato per la replica.
- La replica non è supportata su peering privato.
- Se si proteggono macchine VMware o computer fisici, assicurarsi che il [requisiti di rete](vmware-azure-configuration-server-requirements.md#network-requirements) per Server di configurazione vengono soddisfatte. Dal Server di configurazione è necessaria una connessione a URL specifici per l'orchestrazione della replica di Site Recovery. Non è possibile usare ExpressRoute per la connettività.
- Dopo che è stato eseguito il failover delle macchine virtuali su una rete virtuale di Azure, è possibile accedere alle macchine virtuali usando la configurazione di [peering privato](../expressroute/expressroute-circuit-peerings.md#privatepeering) con la rete virtuale di Azure.


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Se si esegue la replica in Azure, il tipo di account di archiviazione o un disco gestito è necessario?

È necessario una risorsa di archiviazione con ridondanza locale o archiviazione con ridondanza geografica. È consigliabile usare l'archiviazione con ridondanza geografica per una maggiore resilienza dei dati in caso di interruzione del servizio a livello di area o se non è possibile recuperare l'area primaria. L'account deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino. Archiviazione Premium è supportato per la replica di macchine virtuali VMware e Hyper-V e di server fisici, quando si distribuisce Site Recovery nel portale di Azure. Managed disks supporta solo l'archiviazione con ridondanza locale.

### <a name="how-often-can-i-replicate-data"></a>Con quale frequenza è possibile eseguire la replica dei dati?
* **Hyper-V:** È possibile replicare VM Hyper-V ogni cinque minuti, o 30 secondi (eccetto per archiviazione premium)
* **Server fisici di macchine virtuali, macchine virtuali VMware, Azure:** in questo caso la frequenza di replica non è rilevante. La replica è continua.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>È possibile estendere la replica dal sito di ripristino esistente a un sito terziario?
No, la replica concatenata o estesa non è supportata. Richiedere questa funzionalità nel [forum dei commenti](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>È possibile eseguire una replica offline la prima volta che si esegue la replica in Azure?
Questa funzionalità non è supportata. Richiedere questa funzionalità nel [forum dei commenti](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>È possibile escludere dischi specifici dalla replica?
Questa opzione è supportata quando si esegue la replica di macchine virtuali VMware e Hyper-V in Azure tramite il portale di Azure.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>È possibile eseguire la replica delle macchine virtuali con i dischi dinamici?
I dischi dinamici sono supportati durante la replica di macchine virtuali Hyper-V e la replica di macchine virtuali VMware e computer fisici in Azure. Il disco del sistema operativo deve essere un disco di base.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>È possibile limitare la larghezza di banda assegnata per il traffico di replica?
Sì. Altre informazioni sulla limitazione della larghezza di banda in questi articoli:

* [Pianificazione della capacità per la replica da VM VMware e server fisici](site-recovery-plan-capacity-vmware.md)
* [Pianificazione della capacità per la replica da VM Hyper-V in Azure](site-recovery-capacity-planning-for-hyper-v-replication.md)



## <a name="failover"></a>Failover
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Se sta eseguendo il failover in Azure, come posso accedere macchine virtuali di Azure dopo il failover?

È possibile accedere alle VM di Azure tramite una connessione Internet sicura, una connessione VPN da sito a sito o ExpressRoute di Azure. È necessario completare una serie di aspetti per la connessione. [Altre informazioni](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Se si esegue il failover in Azure, in che modo Azure si assicura che i dati siano resilienti?
Azure è progettato nell'ottica della resilienza. Site Recovery è già progettato per il failover in un Data Center Azure secondario, in conformità con il contratto di servizio di Azure. In questo caso, Microsoft si assicura che i metadati e gli insiemi di credenziali rimangano nella stessa area geografica selezionata per l'insieme di credenziali.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Se si esegue la replica tra due data center, che cosa accade se nel data center principale si verifica un'interruzione imprevista?
È possibile attivare un failover non pianificato dal sito secondario. Per eseguire il failover, in Site Recovery non è necessaria la connettività dal sito primario.

### <a name="is-failover-automatic"></a>Il failover è automatico?
Il failover non è automatico. Le operazioni di failover si avviano con un singolo clic nel portale oppure mediante [PowerShell per Site Recovery](/powershell/module/az.recoveryservices) . Il failback è una semplice operazione nel portale di Site Recovery.

Per avviarlo in automatico, è possibile usare Orchestrator o Operations Manager locale per rilevare un errore della macchina virtuale e quindi attivare il failover tramite l'SDK.

* [Ulteriori informazioni](site-recovery-create-recovery-plans.md) sui piani di ripristino.
* [Altre informazioni](site-recovery-failover.md) sul failover.
* [Altre informazioni](site-recovery-failback-azure-to-vmware.md) sul failback di VM VMware e server fisici

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Se l'host locale non risponde o è bloccato, è possibile eseguire nuovamente in un host diverso?
Sì, è possibile usare il ripristino nel percorso alternativo per eseguire il failback in un host diverso da Azure.

* [Per macchine virtuali VMware](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Per macchine virtuali Hyper-V](hyper-v-azure-failback.md#perform-failback)

## <a name="automation"></a>Automazione

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>È possibile automatizzare gli scenari di Site Recovery con un SDK?
Sì. È possibile automatizzare i flussi di lavoro di Site Recovery usando l'API Rest, PowerShell o Azure SDK. Scenari attualmente supportati per la distribuzione di Site Recovery tramite PowerShell:

* [Replica da VM Hyper-V nei cloud VMM ad Azure PowerShell Resource Manager](hyper-v-vmm-powershell-resource-manager.md)
* [Replica da VM Hyper-V senza VMM ad Azure PowerShell Resource Manager](hyper-v-azure-powershell-resource-manager.md)
* [Replicare VMware in Azure con PowerShell Resource Manager](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Aggiornamento provider/componente

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Dove trovare release notes/aggiornamenti cumulativi degli aggiornamenti di Site Recovery

[Scopri](site-recovery-whats-new.md) sui nuovi aggiornamenti, e [ottenere informazioni rollup](service-updates-how-to.md).

## <a name="next-steps"></a>Passaggi successivi
* Leggere la [panoramica di Site Recovery](site-recovery-overview.md)

