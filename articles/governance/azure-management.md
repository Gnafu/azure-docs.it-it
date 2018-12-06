---
title: Panoramica della gestione di Azure
description: Panoramica delle aree di gestione di applicazioni e risorse di Azure con collegamenti a contenuti sugli strumenti di gestione di Azure.
author: DCtheGeek
manager: carmonm
ms.service: governance
ms.topic: article
ms.date: 09/18/2018
ms.author: dacoulte
ms.openlocfilehash: 180b0cb9f52858d9b0f079ea711fd5ccab738ecf
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582307"
---
# <a name="management-in-azure"></a>Gestione in Azure

La governance in Azure è un aspetto della gestione di Azure. Questo articolo illustra brevemente le diverse aree di gestione necessarie per distribuire e gestire le applicazioni e le risorse in Azure e include collegamenti alla documentazione utile per iniziare.

Per gestione si intendono le attività e i processi necessari per gestire le applicazioni aziendali e le risorse che le supportano. Azure include molti strumenti e servizi che possono essere usati in combinazione per offrire la gestione completa per le applicazioni in esecuzione non solo in Azure ma anche in altri cloud e in locale. La comprensione dei diversi strumenti disponibili e del modo in cui possono essere usati in combinazione per diversi scenari di gestione costituisce il primo passaggio della progettazione di un ambiente di gestione completo.

Il diagramma seguente illustra le diverse aree della gestione necessarie per gestire eventuali applicazioni o risorse. Le diverse aree possono essere considerate in termini di ciclo di vita, in cui ogni area è necessaria in successione continua per l'intera durata di una risorsa, a partire dalla distribuzione iniziale, fino al funzionamento continuo e infine al ritiro della risorsa.

![Funzionalità di gestione](../monitoring/media/management-overview/management-capabilities.png)

Nessun servizio di Azure soddisfa da solo i requisiti di un'area di gestione specifica, ma tali requisiti possono essere soddisfatti usando più servizi in combinazione. Alcuni servizi forniscono funzionalità mirate, come Application Insights che offre il monitoraggio per le applicazioni Web. Altri servizi forniscono funzioni comuni, come Log Analytics che archivia i dati di gestione per altri servizi, consentendo di analizzare dati di diversi tipi raccolti da diversi servizi.

Le sezioni seguenti descrivono brevemente le diverse aree di gestione e includono collegamenti a contenuti dettagliati sui servizi di Azure principali specifici per ogni area.

## <a name="monitor"></a>Monitorare

Il monitoraggio comporta la raccolta e l'analisi dei dati per determinare le prestazioni, l'integrità e la disponibilità dell'applicazione aziendale e delle risorse da cui dipende. Una strategia efficace di monitoraggio consente di conoscere il funzionamento dettagliato dei diversi componenti dell'applicazione e di aumentare il tempo di attività grazie all'invio di notifiche proattive sulle problematiche critiche, per poterle risolvere prima che diventino problemi. È possibile leggere una panoramica del monitoraggio in Azure che identifica i diversi servizi usati per una strategia di monitoraggio in [Monitoraggio di applicazioni e risorse di Azure](../monitoring/monitoring-overview.md).

## <a name="configure"></a>Configurare

Per configurazione si intendono la distribuzione e la configurazione iniziali di applicazioni e risorse e la rispettiva manutenzione continuativa con patch e aggiornamenti. L'automazione di queste attività tramite script e criteri consente di eliminare la ridondanza, riducendo al minimo il tempo e il lavoro richiesti e aumentando la precisione e l'efficienza. [Automazione di Azure](..\automation\automation-intro.md) fornisce la maggior parte dei servizi per l'automazione delle attività di configurazione. Oltre ai runbook per l'automazione dei processi, fornisce la gestione della configurazione e degli aggiornamenti, che consente di gestire la configurazione tramite criteri e di identificare e distribuire gli aggiornamenti.

## <a name="govern"></a>Governance

La governance offre meccanismi e processi per mantenere il controllo delle applicazioni e delle risorse in Azure. Include la pianificazione delle iniziative e la definizione delle priorità strategiche.
La governance in Azure viene implementata principalmente tramite due servizi. [Criteri di Azure](../azure-policy/azure-policy-introduction.md) consente di creare, assegnare e gestire definizioni di criteri che applicano diverse regole e azioni in relazione alle risorse, in modo che le risorse rimangano conformi ai contratti di servizio e agli standard dell'azienda. [Gestione costi di Azure di Cloudyn](../cost-management/overview.md) consente di tenere traccia dell'uso del cloud e delle spese per le risorse di Azure e altri provider di servizi cloud, tra cui AWS e Google.

## <a name="secure"></a>Proteggere

La gestione della sicurezza delle applicazioni, delle risorse e dei dati richiede una combinazione di valutazione delle minacce, di raccolta e analisi dei dati sulla sicurezza e la verifica che le applicazioni e le risorse siano progettate e configurate in modo sicuro. Il monitoraggio della sicurezza e l'analisi delle minacce vengono forniti dal Centro sicurezza di Azure, che include la gestione unificata della sicurezza e la protezione avanzata dalle minacce in carichi di lavoro cloud ibridi. Per informazioni complete sulla sicurezza in Azure e indicazioni sulla configurazione sicura delle risorse di Azure, vedere anche [Introduzione alla sicurezza in Azure](../security/azure-security.md).

## <a name="protect"></a>Proteggere

Per protezione si intende assicurare che le applicazioni e i dati siano sempre disponibili, anche in caso di interruzioni al di fuori del proprio controllo. La protezione in Azure viene fornita da due servizi. Il [Backup di Azure](../backup/backup-introduction-to-azure-backup.md) fornisce il backup e il ripristino dei dati, nel cloud o in locale. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) assicura la disponibilità elevata dell'applicazione offrendo la continuità aziendale e il ripristino immediato in caso di emergenza.

## <a name="migrate"></a>Migrazione

Per migrazione si intende la transizione nel cloud di Azure dei carichi di lavoro attualmente in esecuzione in locale.
[Azure Migrate](../migrate/migrate-overview.md) è un servizio che consente di valutare l'idoneità della migrazione in Azure delle macchine virtuali locali, incluse la determinazione delle dimensioni in base alle prestazioni e le stime dei costi. Azure Site Recovery può contribuire all'esecuzione della migrazione effettiva delle macchine virtuali [dall'ambiente locale](../site-recovery/migrate-tutorial-on-premises-azure.md) o [da Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md). [Migrazione del database di Azure](../dms/dms-overview.md) fornisce supporto per la migrazione di più origini di database nelle piattaforme dati di Azure.
