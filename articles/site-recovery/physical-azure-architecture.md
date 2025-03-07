---
title: Architettura del ripristino di emergenza di server fisici in Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo fornisce una panoramica dei componenti e dell'architettura usati durante il ripristino di emergenza di server fisici locali in Azure con il servizio Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: a5d3dfe6457c4b70f0b23c2d8aa7ac5e58e68dc7
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814461"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Architettura del ripristino di emergenza da server fisici ad Azure

Questo articolo descrive l'architettura e i processi usati per la replica, il failover e il ripristino di server fisici Windows e Linux tra un sito locale e Azure, usando il servizio [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Componenti dell'architettura

La tabella e il grafico seguenti offrono una visualizzazione generale dei componenti usati per la replica di server fisici in Azure.  

**Componente** | **Requisito** | **Dettagli**
--- | --- | ---
**Azure** | Una sottoscrizione di Azure e una rete di Azure. | I dati replicati da computer fisici locali vengono archiviati in Azure Managed Disks. Le macchine virtuali di Azure vengono create con i dati replicati durante l'esecuzione di un failover dal sito locale ad Azure. Le VM di Azure si connettono alla rete virtuale di Azure quando vengono create.
**Server di configurazione** | Viene distribuito un solo computer fisico locale o una macchina virtuale VMware per eseguire tutti i componenti di Site Recovery. La macchina virtuale esegue il server di configurazione, il server di elaborazione e il server di destinazione master. | Il server di configurazione coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.
 **Server di elaborazione**  | Installato per impostazione predefinita insieme al server di configurazione. | Agisce come un gateway di replica. Riceve i dati di replica, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia all'archiviazione di Azure.<br/><br/> Il server di elaborazione installa anche il servizio Mobility nei server di cui si vuole eseguire la replica.<br/><br/> Con l'aumentare delle dimensioni della distribuzione, è possibile aggiungere altri server di elaborazione separati per gestire volumi più elevati di traffico di replica.
 **Server master di destinazione** | Installato per impostazione predefinita insieme al server di configurazione. | Gestisce i dati di replica durante il failback da Azure.<br/><br/> Per distribuzioni di grandi dimensioni, è possibile aggiungere un altro server di destinazione master separato per il failback.
**Server replicati** | Il servizio Mobility viene installato in ogni server di cui si esegue la replica. | È consigliabile consentire l'installazione automatica dal server di elaborazione. In alternativa, è possibile installare manualmente il servizio o usare un metodo di distribuzione automatico, ad esempio System Center Configuration Manager.

**Architettura della replica dall'ambiente fisico ad Azure**

![Componenti](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Processo di replica

1. Si configura la distribuzione, inclusi i componenti locali e di Azure. Nell'insieme di credenziali di Servizi di ripristino è necessario specificare l'origine e la destinazione della replica, impostare il server di configurazione, creare criteri di replica e abilitare la replica.
2. La replica dei computer avviene in base ai criteri di replica e viene eseguita la replica di una copia iniziale dei dati del server nella risorsa di archiviazione di Azure.
3. Al termine della replica iniziale, viene avviata la replica differenziale in Azure. Le modifiche rilevate vengono salvate in un file HRL.
    - I computer comunicano con il server di configurazione sulla la porta HTTPS 443 in ingresso, per la gestione delle repliche.
    - I computer inviano i dati di replica al server di elaborazione sulla porta HTTPS 9443 in ingresso (che può essere modificata).
    - Il server di configurazione orchestra la gestione delle repliche con Azure tramite la porta HTTPS 443 in uscita.
    - Il server di elaborazione riceve i dati dai computer di origine, li ottimizza e li crittografa e li invia ad Archiviazione di Azure tramite la porta 443 in uscita.
    - Se si abilita la coerenza tra più macchine virtuali, i computer inclusi nel gruppo di replica comunicano tra loro sulla porta 20004. La coerenza tra più VM viene usata se si raggruppano più computer in gruppi di replica che condividono punti di ripristino coerenti con l'arresto anomalo del sistema e coerenti con l'app quando si esegue il failover. È utile se i computer eseguono lo stesso carico di lavoro e devono essere coerenti.
4. Il traffico viene replicato negli endpoint pubblici di archiviazione di Azure, tramite Internet. In alternativa, è possibile usare il [peering pubblico](../expressroute/expressroute-circuit-peerings.md#publicpeering) di Azure ExpressRoute. La replica del traffico tramite una VPN da sito a sito da un sito locale ad Azure non è supportata.


**Processo di replica dall'ambiente fisico ad Azure**

![Processo di replica](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processo di failover e failback

Dopo aver configurato la replica e aver eseguito un'analisi di ripristino di emergenza (failover di test) per verificare che funzioni tutto come previsto, è possibile eseguire il failover e il failback in base alle esigenze. Si noti che:

- Il failover pianificato non è supportato.
- È necessario eseguire il failback in una macchina virtuale VMware locale. Questo significa che è necessaria un'infrastruttura VMware locale, anche quando si esegue la replica di server fisici locali in Azure.
- È possibile eseguire il failover di un solo computer o creare piani di ripristino per il failover di più computer insieme.
- Quando si esegue un failover, le macchine virtuali di Azure vengono create dai dati replicati nella risorsa di archiviazione di Azure.
- Dopo aver attivato il failover iniziale, è necessario eseguirne il commit per iniziare ad accedere al carico di lavoro dalla macchina virtuale di Azure.
- Quando il sito locale primario è di nuovo disponibile, è possibile effettuare il failback.
- È necessario configurare un'infrastruttura di failback, che includa:
    - **Server di elaborazione temporaneo in Azure**: per eseguire il failback da Azure, configurare una macchina virtuale di Azure perché funga da server di elaborazione per gestire la replica da Azure. Questa VM può essere eliminata al termine del failback.
    - **Connessione VPN**: per eseguire il failback è necessaria una connessione VPN, oppure Azure ExpressRoute, dalla rete di Azure al sito locale.
    - **Server di destinazione master separato**: per impostazione predefinita, il server di destinazione master installato con il server di configurazione nella macchina virtuale VMware locale gestisce il failback. Tuttavia, se è necessario eseguire il failback di grandi volumi di traffico, è consigliabile configurare un server di destinazione master locale separato a questo scopo.
    - **Criteri di failback**: per eseguire la replica nel sito locale, è necessario un criterio di failback, che viene creato automaticamente quando si creano i criteri di replica dal sito locale ad Azure.
    - **Infrastruttura VMware**: è necessaria un'infrastruttura VMware per il failback. Non è possibile eseguire il failback a un server fisico.
- Dopo aver predisposto i componenti, il failback avviene in tre fasi:
    - Fase 1: riproteggere le macchine virtuali di Azure in modo da eseguirne di nuovo la replica da Azure alle macchine virtuali VMware locali.
    - Fase 2: eseguire un failover nel sito locale.
    - Fase 3: al termine del failback dei carichi di lavoro, riabilitare la replica.

**Failback di VMware da Azure**

![Failback](./media/physical-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Passaggi successivi

Seguire [questa esercitazione](physical-azure-disaster-recovery.md) per abilitare la replica da server fisico ad Azure.
