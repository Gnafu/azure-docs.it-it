---
title: Modelli Blueprint per sicurezza e conformità di Azure - Data warehouse per PCI DSS
description: Modelli Blueprint per sicurezza e conformità di Azure - Data warehouse per PCI DSS
services: security
author: meladie
ms.assetid: 6d29e5bf-6cd0-4cda-bbd8-59f283b0c86c
ms.service: security
ms.topic: article
ms.date: 07/03/2018
ms.author: meladie
ms.openlocfilehash: 0e7eadcb511bc55bbafcf20f44a28ffcf2f9be90
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946651"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-pci-dss"></a>Progetto di sicurezza e conformità di Azure: data warehouse per PCI DSS

## <a name="overview"></a>Panoramica

Questo modello Blueprint per sicurezza e conformità di Azure fornisce indicazioni per la distribuzione di un'architettura di data warehouse in Azure utile per supportare i requisiti di Payment Card Industry Data Security Standards (PCI DSS 3.2). Illustra un'architettura di riferimento comune e dimostra la gestione corretta dei dati delle carte di credito (numero, scadenza e dati di verifica) in un ambiente multilivello protetto e conforme. Questo modello Blueprint illustra i modi in cui i clienti possono soddisfare requisiti specifici di sicurezza e conformità e costituisce un punto di partenza per i clienti che vogliono creare e configurare soluzioni di data warehouse personalizzate in Azure.

L'architettura di riferimento, la guida all'implementazione e il modello di minaccia forniscono una base a cui i clienti possono fare riferimento per la conformità ai requisiti di PCI DSS 3.2. Questa soluzione offre una base iniziale per aiutare i clienti a distribuire i carichi di lavoro in Azure in modo conforme a PCI DSS 3.2; tuttavia la soluzione non deve essere usata così com'è in un ambiente di produzione perché sono necessarie configurazioni aggiuntive.

Per ottenere la conformità allo standard PCI DSS è necessaria la certificazione di una soluzione di produzione del cliente da parte di un Qualified Security Assessor (QSA) accreditato. I clienti hanno la responsabilità di svolgere valutazioni appropriate della sicurezza e della conformità di qualsiasi soluzione creata con questa architettura, in quanto i requisiti possono variare a seconda delle specifiche dell'implementazione di ogni cliente.

## <a name="architecture-diagram-and-components"></a>Diagramma dell'architettura e componenti

Questa soluzione fornisce un'architettura di riferimento che implementa un data warehouse cloud a prestazioni e sicurezza elevate. L'architettura include due livelli di dati separati: un livello in cui i dati vengono importati, archiviati e sottoposti a staging in un ambiente SQL con cluster e un altro per Azure SQL Data Warehouse, in cui i dati vengono caricati tramite uno strumento di estrazione, trasformazione e caricamento (ETL), come query T-SQL [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase), per l'elaborazione. Dopo che i dati sono stati archiviati in Azure SQL Data Warehouse, è possibile eseguire l'analisi su larga scala.

Azure offre svariati servizi di creazione di report e di analisi per i clienti. Questa soluzione include SQL Server Reporting Services (SSRS) per la creazione rapida di report da Azure SQL Data Warehouse. Tutto il traffico SQL viene crittografato con il protocollo SSL tramite l'inclusione di certificati autofirmati. Come procedura consigliata, Azure invita all'uso di un'autorità di certificazione attendibile per una sicurezza avanzata.

I dati in Azure SQL Data Warehouse vengono archiviati in tabelle relazionali con uno spazio di archiviazione a colonne, vale a dire un formato che riduce in modo significativo i costi di archiviazione dei dati migliorando al tempo stesso le prestazioni delle query. In base ai requisiti di utilizzo, le risorse di calcolo di Azure SQL Data Warehouse possono essere aumentate o ridotte o arrestate completamente, se non sono presenti processi attivi che richiedono risorse di calcolo.

Un servizio di bilanciamento del carico SQL gestisce il traffico SQL, assicurando prestazioni elevate. Tutte le macchine virtuali in questa architettura di riferimento vengono distribuite in un set di disponibilità con istanze di SQL Server configurate in un gruppo di disponibilità AlwaysOn per assicurare la disponibilità elevata e offrire funzionalità di ripristino di emergenza.

Questa architettura di riferimento per data warehouse include anche un livello Active Directory per la gestione delle risorse nell'architettura. La subnet di Active Directory consente l'adozione facilitata in una struttura di foresta di Active Directory più ampia, permettendo il funzionamento continuo dell'ambiente anche in caso di mancata disponibilità dell'accesso alla foresta più grande. Tutte le macchine virtuali sono aggiunte al dominio nel livello Active Directory e usano i Criteri di gruppo di Active Directory per applicare configurazioni di sicurezza e conformità a livello di sistema operativo.

La soluzione usa gli account di Archiviazione di Azure che i clienti possono configurare per usare la crittografia del servizio di archiviazione e mantenere la riservatezza dei dati inattivi. Azure archivia tre copie di dati all'interno del data center scelto del cliente per garantire la resilienza. L'archiviazione con ridondanza geografica assicura che i dati vengano replicati in un data center secondario a centinaia di chilometri di distanza e archiviati di nuovo come tre copie all'interno di tale data center, impedendo a un evento avverso nel data center principale del cliente di generare una perdita di dati.

Per una sicurezza ottimale, tutte le risorse in questa soluzione vengono gestite come gruppo di risorse tramite Azure Resource Manager. Il controllo degli accessi in base al ruolo di Azure Active Directory viene usato per controllare l'accesso alle chiavi, incluse le risorse distribuite in Azure Key Vault. L'integrità del sistema è monitorata tramite il Centro sicurezza di Azure e Monitoraggio di Azure. I clienti configurano entrambi i servizi di monitoraggio per acquisire i log e visualizzare l'integrità del sistema in un singolo dashboard, facilmente navigabile.

Una macchina virtuale viene usata come bastion host di gestione, fornendo una connessione sicura per l'accesso alle risorse distribuite da parte degli amministratori. I dati vengono caricati nell'area di staging tramite questo bastion host di gestione. **Microsoft consiglia di configurare una connessione VPN o Azure ExpressRoute per la gestione e l'importazione dei dati nella subnet dell'architettura di riferimento.**

![Diagramma dell'architettura di riferimento di data warehouse per PCI DSS](images/pcidss-dw-architecture.png "Diagramma dell'architettura di riferimento di data warehouse per PCI DSS")

Questa soluzione usa i servizi di Azure seguenti. Informazioni dettagliate sull'architettura di distribuzione sono disponibili nella sezione [Architettura di distribuzione](#deployment-architecture).

- SET DI DISPONIBILITÀ
    - Controller di dominio di Active Directory
    - Controllo e nodi del cluster SQL
- Azure Active Directory
- Azure Data Catalog
- Insieme di credenziali delle chiavi di Azure
- Monitoraggio di Azure
- Centro sicurezza di Azure
- Azure Load Balancer
- Archiviazione di Azure
- Macchine virtuali di Azure
    - (1) Bastion host
    - (2) Controller di dominio di Active Directory
    - (2) Nodo del cluster di SQL Server
    - (1) Controllo SQL Server
- Rete virtuale di Azure
    - (1) Rete /16
    - (4) Reti /24
    - (4) Gruppi di sicurezza di rete
- Insieme di credenziali dei servizi di ripristino
- SQL Data Warehouse
- SQL Server Reporting Services

## <a name="deployment-architecture"></a>Architettura di distribuzione

La sezione seguente descrive in modo dettagliato gli elementi di sviluppo e implementazione.

**SQL data warehouse**: [SQL data warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) è un data warehouse aziendale (EDW) che utilizza l'elaborazione parallela massiva (MPP) per eseguire rapidamente query complesse su petabyte di dati, consentendo agli utenti di identificare in modo efficiente i dati finanziari. Gli utenti possono usare semplici query T-SQL PolyBase per importare Big Data in SQL Data Warehouse e quindi sfruttare la potenza dell'elaborazione MPP per eseguire analisi ad alte prestazioni.

**SQL Server Reporting Services (SSRS)** : [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) consente di creare rapidamente report con tabelle, grafici, mappe, misuratori, matrici e altro ancora per Azure SQL data warehouse.

**Data Catalog**: [Data Catalog](../../data-catalog/overview.md) rende le origini dati facilmente individuabili e comprensibili per gli utenti che gestiscono i dati. Le origini dati comuni possono essere registrate, contrassegnate con tag e sottoposte a ricerche di dati finanziari. I dati rimangono nella posizione esistente, ma una copia dei relativi metadati viene aggiunta a Data Catalog, insieme a un riferimento alla posizione dell'origine dati. I metadati vengono anche indicizzati per semplificare l'individuazione di ogni origine dati tramite una ricerca e per rendere l'origine dati comprensibile per gli utenti che la individuano.

**Bastion host**: il bastion host è il singolo punto di ingresso che consente agli utenti di accedere alle risorse distribuite in questo ambiente. Il bastion host fornisce una connessione sicura alle risorse distribuite consentendo solo il traffico remoto dagli indirizzi IP pubblici inclusi in un elenco di indirizzi attendibili. Per consentire il traffico di desktop remoto (RDP), l'origine del traffico deve essere definita nel gruppo di sicurezza di rete.

Questa soluzione crea una macchina virtuale come bastion host aggiunto al dominio con le configurazioni seguenti:
-   [Estensione antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Estensione di Diagnostica di Azure](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Crittografia dischi di Azure](../azure-security-disk-encryption-overview.md) tramite Azure Key Vault
-   [Criteri di arresto automatico](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) per ridurre il consumo di risorse della macchina virtuale quando non è in uso
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) abilitato, in modo che le credenziali e altri segreti vengano eseguiti in un ambiente protetto, isolato dal sistema operativo in esecuzione.

### <a name="virtual-network"></a>Rete virtuale

Questa architettura di riferimento definisce una rete privata virtuale con uno spazio degli indirizzi 10.0.0.0/16.

**Gruppi di sicurezza di rete**: i [gruppi di sicurezza di rete](../../virtual-network/virtual-network-vnet-plan-design-arm.md) contengono elenchi di controllo di accesso che autorizzano o bloccano il traffico in una rete virtuale. I gruppi di sicurezza di rete possono essere usati per proteggere il traffico a livello di subnet o di singola macchina virtuale. Esistono i seguenti gruppi di sicurezza di rete:

  - Un gruppo di sicurezza di rete per il livello dati (cluster SQL Server, controllo SQL Server e SQL Load Balancer)
  - Un gruppo di sicurezza di rete per il bastion host di gestione
  - Un gruppo di sicurezza di rete per Active Directory
  - Un gruppo di sicurezza di rete per SQL Server Reporting Services

Per ognuno dei gruppi di sicurezza di rete sono aperti porte e protocolli specifici per garantire il funzionamento protetto e corretto della soluzione. Per ogni gruppo di sicurezza di rete sono abilitate anche le configurazioni seguenti:

- [Log ed eventi di diagnostica](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) abilitati e archiviati in un account di archiviazione
- I log di monitoraggio di Azure sono connessi ai [log&#39;di diagnostica del gruppo di sicurezza di rete](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Subnet**: ogni subnet è associata al gruppo di sicurezza di rete corrispondente.

### <a name="data-at-rest"></a>Dati inattivi

L'architettura protegge i dati inattivi in diversi modi, tra cui la crittografia e il controllo del database.

**Archiviazione di Azure**: per soddisfare i requisiti dei dati inattivi crittografati, [Archiviazione di Azure](https://azure.microsoft.com/services/storage/) usa sempre la [crittografia del servizio di archiviazione](../../storage/common/storage-service-encryption.md). Ciò consente di proteggere i dati dei possessori di carte, supportando l'impegno a livello di sicurezza dell'organizzazione e i requisiti di conformità definiti da PCI DSS 3.2.

**Crittografia dischi di Azure**: [Crittografia dischi di Azure](../azure-security-disk-encryption-overview.md) sfrutta la funzionalità BitLocker di Windows per fornire la crittografia del volume per i dischi dati e del sistema operativo. La soluzione si integra con Azure Key Vault per semplificare il controllo e la gestione delle chiavi di crittografia dei dischi.

**Database SQL di Azure**: L'istanza di database SQL di Azure usa le misure di sicurezza del database seguenti:

- L'[autenticazione e l'autorizzazione di Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) consentono la gestione delle identità degli utenti del database e di altri servizi Microsoft in una posizione centrale.
- Il [controllo del database SQL](../../sql-database/sql-database-auditing.md) tiene traccia degli eventi che si verificano nel database e li registra in un log di controllo in un account di Archiviazione di Azure.
- Il database SQL di Azure è configurato per l'uso di [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), che esegue la crittografia e la decrittografia in tempo reale del database, dei backup associati e dei file di log delle transazioni per proteggere le informazioni inattive. Transparent Data Encryption garantisce che i dati archiviati non siano soggetti ad accesso non autorizzato.
- Le [regole del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impediscono completamente l'accesso ai server di database fino alla concessione delle autorizzazioni appropriate. Il firewall concede l'accesso ai database in base all'indirizzo IP di origine di ogni richiesta.
- Il [servizio di rilevamento delle minacce di SQL](../../sql-database/sql-database-threat-detection.md) consente di rilevare e di rispondere a minacce potenziali non appena si verificano, visualizzando avvisi relativi alla sicurezza per attività sospette del database, vulnerabilità potenziali, attacchi SQL injection e modelli di accesso al database anomali.
- Le [colonne crittografate](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) assicurano che i dati sensibili non vengano mai visualizzati come testo non crittografato all'interno del sistema di database. Dopo l'abilitazione della crittografia dei dati, solo le applicazioni client o i server applicazioni con accesso alle chiavi possono accedere ai dati di testo non crittografato.
- Le [proprietà estese](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) possono essere usate per interrompere l'elaborazione degli interessati perché consentono agli utenti di aggiungere proprietà personalizzate agli oggetti di database e di contrassegnare i dati come "Sospesi" per supportare la logica dell'applicazione, in modo da impedire l'elaborazione dei dati finanziari associati.
- La [sicurezza a livello di riga](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) consente agli utenti di definire criteri per limitare l'accesso ai dati per interrompere l'elaborazione.
- La [maschera dati dinamica del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita l'esposizione dei dati sensibili nascondendoli agli utenti o alle applicazioni senza privilegi. La maschera dati dinamica può individuare automaticamente dati potenzialmente sensibili e suggerire le maschere appropriate da applicare. In questo modo è più semplice identificare e ridurre l'accesso ai dati in modo da evitare l'uscita dal database tramite accesso non autorizzato. I clienti sono tenuti a modificare le impostazioni della maschera dati dinamica in modo da adattarle al proprio schema del database.

### <a name="identity-management"></a>Gestione delle identità

Le tecnologie seguenti offrono le funzionalità necessarie per gestire l'accesso ai dati nell'ambiente di Azure:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) è il servizio Microsoft di gestione di identità e directory multi-tenant basato sul cloud. Tutti gli utenti della soluzione sono stati creati in Azure Active Directory, inclusi gli utenti che accedono al database SQL di Azure.
- L'autenticazione per l'applicazione viene eseguita tramite Azure Active Directory. Per altre informazioni, vedere [Integrazione di applicazioni con Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Inoltre, la crittografia delle colonne del database usa Azure Active Directory per l'autenticazione dell'applicazione nel database SQL di Azure. Per altre informazioni, vedere l'articolo su come [proteggere i dati sensibili nel database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- Il [controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/role-assignments-portal.md) consente agli amministratori di definire autorizzazioni di accesso con granularità fine per concedere solo il livello di accesso necessario agli utenti per il proprio lavoro. Invece di concedere a ogni utente autorizzazioni senza limiti per le risorse di Azure, gli amministratori possono consentire solo determinate azioni per l'accesso ai dati. L'accesso alla sottoscrizione è limitato all'amministratore della sottoscrizione.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) consente ai clienti di ridurre al minimo il numero di utenti autorizzati ad accedere a determinate informazioni. Gli amministratori possono usare Azure Active Directory Privileged Identity Management per individuare, limitare e monitorare le identità con privilegi e il rispettivo accesso alle risorse. Questa funzionalità può essere usata anche per applicare l'accesso amministrativo on demand e Just-In-Time quando necessario.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) rileva le potenziali vulnerabilità che interessano le identità dell'organizzazione, consente di configurare risposte automatiche per le azioni sospette rilevate in relazione alle identità dell'organizzazione, ricerca la causa degli eventi sospetti per intraprendere le azioni appropriate per risolverli.

### <a name="security"></a>Security

**Gestione dei segreti**: la soluzione usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) per la gestione delle chiavi e dei segreti. L'insieme di credenziali delle chiavi di Azure consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. Le funzionalità seguenti di Azure Key Vault aiutano gli utenti a proteggere e accedere ai dati:

- I criteri di accesso avanzati vengono configurati in base alle necessità.
- I criteri di accesso di Key Vault sono definiti con le autorizzazioni minime necessarie per le chiavi e i segreti.
- Tutti i segreti e le chiavi in Key Vault hanno date di scadenza.
- Tutte le chiavi in Key Vault sono protette da moduli di protezione hardware specializzati. Le chiavi sono di tipo RSA a 2048 bit protette dal modulo di protezione hardware.
- A tutti gli utenti e le identità vengono concesse le autorizzazioni minime richieste tramite il controllo degli accessi in base al ruolo.
- I log di diagnostica per Key Vault sono abilitati con un periodo di conservazione di almeno 365 giorni
- Le operazioni di crittografia consentite per le chiavi sono limitate a quelle necessarie.

**Gestione delle patch**: Le macchine virtuali Windows distribuite come parte di questa architettura di riferimento sono configurate per impostazione predefinita in modo da ricevere aggiornamenti automatici dal servizio Windows Update. Questa soluzione include anche il servizio [Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-intro) che consente di creare distribuzioni aggiornate per applicare patch alle macchine virtuali quando è necessario.

**Protezione antimalware**: [Microsoft antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) per macchine virtuali offre funzionalità di protezione in tempo reale che consentono di identificare e rimuovere virus, spyware e altro software dannoso, con avvisi configurabili in caso di tentativi di software dannoso o indesiderato. installare o eseguire in macchine virtuali protette.

**Centro sicurezza di Azure**: con il [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) i clienti possono applicare e gestire centralmente i criteri di sicurezza nei carichi di lavoro, limitare l'esposizione alle minacce e rilevare e rispondere agli attacchi. Il Centro sicurezza di Azure accede inoltre alle configurazioni esistenti dei servizi di Azure in modo da fornire elementi consigliati su configurazione e servizi utili per migliorare le condizioni di sicurezza e proteggere i dati.

Il Centro sicurezza di Azure usa una serie di funzionalità di rilevamento per avvisare i clienti riguardo a potenziali attacchi contro gli ambienti in cui operano. Questi avvisi contengono informazioni importanti relative a cosa ha attivato l'avviso, alle risorse interessate e all'origine dell'attacco. Il Centro sicurezza di Azure include un set di [avvisi di sicurezza predefiniti](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) che vengono attivati in caso di minaccia o di attività sospetta. Le [regole di avviso personalizzate](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) nel Centro sicurezza di Azure consentono ai clienti di definire nuovi avvisi di sicurezza in base ai dati già raccolti dall'ambiente.

Il Centro sicurezza di Azure offre avvisi di sicurezza e imprevisti classificati in ordine di priorità semplificando l'individuazione e gestione di potenziali problemi di sicurezza per i clienti. Viene generato un [report di intelligence per le minacce](https://docs.microsoft.com/azure/security-center/security-center-threat-report) per ogni minaccia rilevata per supportare i team di risposta agli eventi imprevisti a livello di indagine e reazione alle minacce.

### <a name="business-continuity"></a>Continuità aziendale

**Disponibilità elevata**: I carichi di lavoro del server vengono raggruppati in un [set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) per garantire la disponibilità elevata delle macchine virtuali in Azure. Almeno una macchina virtuale è disponibile durante un evento di manutenzione pianificato o non pianificato, in conformità al Contratto di servizio di Azure con disponibilità garantita del 99,95%.

**Insieme di credenziali di Servizi di ripristino**: L'insieme di credenziali di [servizi di ripristino](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) ospita i dati di backup e protegge tutte le configurazioni di macchine virtuali di Azure in questa architettura. Con un insieme di credenziali di Servizi di ripristino i clienti possono ripristinare file e cartelle da una macchina virtuale IaaS senza ripristinare l'intera macchina virtuale, riducendo i tempi di ripristino.

### <a name="logging-and-auditing"></a>Registrazione e controllo

I servizi di Azure registrano in modo completo le attività di sistema e degli utenti e l'integrità del sistema:
- **Log attività**: i [log attività](../../azure-monitor/platform/activity-logs-overview.md) offrono informazioni dettagliate sulle operazioni eseguite sulle risorse di una sottoscrizione. I log attività possono essere utili per determinare l'iniziatore di un'operazione, l'ora in cui si è verificata e lo stato.
- **Log di diagnostica**: i [log di diagnostica](../../azure-monitor/platform/diagnostic-logs-overview.md) includono tutti i log generati da ogni risorsa. ovvero i log eventi del sistema Windows, i log di Archiviazione di Azure, i log di controllo di Key Vault e i log degli accessi e del firewall del gateway applicazione. Tutti i log di diagnostica eseguono operazioni di scrittura in un account di archiviazione di Azure centralizzato e crittografato per finalità di archiviazione. La conservazione può essere configurata dall'utente per un massimo di 730 giorni per soddisfare i requisiti di conservazione specifici dell'organizzazione.

**Log di Monitoraggio di Azure**: Questi log vengono consolidati nei [log di monitoraggio di Azure](https://azure.microsoft.com/services/log-analytics/) per l'elaborazione, l'archiviazione e la creazione di report del dashboard. Dopo la raccolta, i dati vengono organizzati in tabelle separate per tipo nelle aree di lavoro di Log Analytics, in modo che sia possibile analizzare tutti i dati insieme, indipendentemente dalla rispettiva origine. Il Centro sicurezza di Azure si integra inoltre con i log di monitoraggio di Azure, consentendo ai clienti di usare query kusto per accedere ai dati degli eventi di sicurezza e combinarli con i dati di altri servizi.

Come parte di questa architettura sono incluse le [soluzioni di monitoraggio](../../monitoring/monitoring-solutions.md) di Azure seguenti:
-   [Valutazione Active Directory](../../azure-monitor/insights/ad-assessment.md): la soluzione Controllo integrità Active Directory valuta il rischio e l'integrità degli ambienti server a intervalli regolari e fornisce un elenco di elementi consigliati specifici per l'infrastruttura di server distribuita, classificati in ordine di priorità.
- [Valutazione SQL](../../azure-monitor/insights/sql-assessment.md): la soluzione Controllo integrità SQL valuta il rischio e l'integrità degli ambienti server a intervalli regolari e offre ai clienti un elenco di elementi consigliati specifici per l'infrastruttura di server distribuita, classificati in ordine di priorità.
- [Integrità agente](../../monitoring/monitoring-solution-agenthealth.md): la soluzione Integrità agente segnala il numero di agenti distribuiti e la rispettiva distribuzione geografica, oltre al numero di agenti non reattivi e a quello degli agenti che inviano dati operativi.
-   [Analisi log attività](../../azure-monitor/platform/collect-activity-logs.md): la soluzione Analisi log attività offre assistenza per l'analisi dei log attività di Azure in tutte le sottoscrizioni di Azure per un cliente.

**Automazione di Azure**: [Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) archivia, esegue e gestisce i runbook. In questa soluzione i runbook consentono di raccogliere log dal database SQL di Azure. La soluzione [Rilevamento modifiche](../../automation/change-tracking.md) di Automazione consente ai clienti di identificare con facilità le modifiche apportate all'ambiente.

**Monitoraggio di Azure**: [Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) consente agli utenti di tenere traccia delle prestazioni, implementare la sicurezza e identificare le tendenze, permettendo alle organizzazioni di eseguire verifiche, creare avvisi e archiviare i dati, incluso il rilevamento delle chiamate API nelle relative risorse di Azure.

## <a name="threat-model"></a>Modello di minaccia

Il diagramma di flusso di dati per questa architettura di riferimento è disponibile per il [download](https://aka.ms/pcidss-dw-tm) o è riportato più avanti. Il modello può aiutare i clienti comprendere i punti di rischio potenziale nell'infrastruttura del sistema quando vengono apportate modifiche.

![Data warehouse per il diagramma dell'architettura di riferimento PCI DSS](images/pcidss-dw-threat-model.png "Applicazione Web PaaS per il modello di minaccia di PCI DSS")

## <a name="compliance-documentation"></a>Documentazione sulla conformità

In [Modelli Blueprint per sicurezza e conformità di Azure - PCI DSS - Matrice delle responsabilità dell'utente](https://aka.ms/pcidss-crm) sono elencate le responsabilità per tutti i requisiti di PCI DSS 3.2.

In [Modelli Blueprint per sicurezza e conformità di Azure - PCI DSS - Matrice di implementazione di data wharehouse](https://aka.ms/pcidss-dw-cim) sono fornite informazioni sui requisiti di PCI DSS 3.2 a cui l'architettura del data warehouse fa riferimento, incluse descrizioni dettagliate del modo in cui l'implementazione rispetta i requisiti di ogni controllo specifico.

## <a name="guidance-and-recommendations"></a>Indicazioni e consigli

### <a name="vpn-and-expressroute"></a>VPN ed ExpressRoute

È necessario configurare un tunnel VPN sicuro o un'istanza di [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) per stabilire in modo sicuro la connessione alle risorse distribuite come parte di questa architettura di riferimento del data warehouse. La configurazione corretta di una VPN o di una connessione ExpressRoute consente ai clienti di aggiungere un livello di protezione per i dati in transito.

L'implementazione di un tunnel VPN sicuro con Azure consente di creare una connessione virtuale privata tra una rete locale e una rete virtuale di Azure. Questa connessione viene eseguita via Internet e consente ai clienti di inviare in modo sicuro le informazioni attraverso un "tunnel" all'interno di un collegamento crittografato tra la rete del cliente e Azure. La rete VPN da sito a sito è una tecnologia sicura e collaudata, che viene distribuita da aziende di ogni dimensione ormai da decenni. La [modalità tunnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) viene usata in questa opzione come meccanismo di crittografia.

Poiché il traffico entro il tunnel VPN attraversa Internet con una connessione VPN da sito a sito, Microsoft offre un'altra opzione di connessione ancora più sicura. Azure ExpressRoute è un collegamento WAN dedicato tra Azure e una posizione locale o un provider di hosting di Exchange. Poiché le connessioni ExpressRoute non sfruttano la rete Internet, queste connessioni offrono un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali. Poiché inoltre si tratta di una connessione diretta del provider di telecomunicazioni del cliente, i dati non vengono trasmessi su Internet e quindi non vengono esposti a Internet.

Sono [disponibili](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) procedure consigliate per l'implementazione di una rete ibrida protetta che estende una rete locale in Azure.

### <a name="extract-transform-load-process"></a>Processo di estrazione, trasformazione e caricamento

[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) può caricare dati in Azure SQL Data Warehouse senza che sia necessario uno strumento di estrazione, trasformazione, caricamento o importazione separato. PolyBase consente l'accesso ai dati tramite query T-SQL. È possibile usare con PolyBase lo stack di business intelligence e analisi Microsoft, oltre a strumenti di terze parti compatibili con SQL Server.

### <a name="azure-active-directory-setup"></a>Configurazione di Azure Active Directory

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) è essenziale per la gestione della distribuzione e per il provisioning dell'accesso al personale che interagisce con l'ambiente. Un'istanza esistente di Windows Server Active Directory può essere integrata con Azure Active Directory con [quattro clic](../../active-directory/hybrid/how-to-connect-install-express.md). I clienti possono anche associare l'infrastruttura di Active Directory distribuita (controller di dominio) a un'istanza esistente di Azure Active Directory configurando l'infrastruttura di Active Directory distribuita come sottodominio di una foresta di Azure Active Directory.

### <a name="optional-services"></a>Servizi facoltativi

Azure offre diversi servizi che semplificano l'archiviazione e lo staging di dati formattati e non formattati. I servizi seguenti possono essere aggiunti a questa architettura di riferimento in base ai requisiti dei clienti:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) è un servizio cloud gestito ideato per complessi progetti ibridi di estrazione, trasformazione e caricamento e di integrazione dei dati. Azure Data Factory include funzionalità utili per la verifica e l'individuazione dei dati dei possessori di carte, tra cui strumenti di visualizzazione e monitoraggio per identificare quando sono stati ricevuti i dati e la rispettiva provenienza. Con Azure Data Factory i clienti possono creare e pianificare flussi di lavoro basati sui dati, detti pipeline, che ricevono dati provenienti da archivi diversi. Queste pipeline consentono ai clienti di inserire dati da origini interne ed esterne. I clienti possono quindi elaborare e trasformare i dati per l'output in archivi dati come Azure SQL Data Warehouse.
- I clienti possono eseguire lo staging di dati non strutturati in [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview), che consente l'acquisizione di dati di qualsiasi dimensione, tipo e velocità di inserimento in un'unica posizione per analisi operative ed esplorative.  Azure Data Lake include funzionalità che consentono l'estrazione e la conversione dei dati. Azure Data Lake Store è compatibile con la maggior parte dei componenti open source dell'ecosistema Hadoop e si integra senza problemi con altri servizi di Azure, ad esempio Azure SQL Data Warehouse.

## <a name="disclaimer"></a>Dichiarazione di non responsabilità

 - Questo documento è esclusivamente a scopo informativo. MICROSOFT NON RILASCIA ALCUNA GARANZIA, ESPRESSA, IMPLICITA O DI LEGGE, RIGUARDO ALLE INFORMAZIONI CONTENUTE IN QUESTO DOCUMENTO. Questo documento viene fornito "così com'è". Le informazioni e le indicazioni riportate nel presente documento, inclusi URL e altri riferimenti a siti Internet, sono soggette a modifica senza preavviso. I clienti che fanno riferimento a questo documento lo usano a proprio rischio.
 - Questo documento non concede ai clienti diritti legali di proprietà intellettuale per alcun prodotto o soluzione Microsoft.
 - I clienti possono copiare e usare questo documento per scopi di riferimento interni.
 - Alcune indicazioni contenute in questo documento possono comportare un maggiore utilizzo di risorse di dati, rete o calcolo in Azure, aumentando di conseguenza i costi di licenza o di sottoscrizione di Azure per il cliente.
 - Questa architettura è una soluzione di base che i clienti possono adattare ai propri requisiti specifici e che non deve essere usata così com'è in un ambiente di produzione.
 - Questo documento è stato sviluppato come riferimento e non deve essere usato per definire tutti i mezzi attraverso cui un cliente può soddisfare requisiti e normative di conformità specifici. I clienti dovranno richiedere supporto legale all'organizzazione riguardo alle implementazioni approvate.
