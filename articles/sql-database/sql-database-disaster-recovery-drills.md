---
title: Esercitazioni per il ripristino di emergenza del database SQL | Documentazione Microsoft
description: Istruzioni e procedure consigliate relative all'uso del database SQL di Azure per eseguire esercitazioni per il ripristino di emergenza.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 12/18/2018
ms.openlocfilehash: 2923ae8b9b25932ae214cfa45780dffb8780dd39
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568852"
---
# <a name="performing-disaster-recovery-drill"></a>Esercitazione per il ripristino di emergenza

È consigliabile verificare periodicamente la conformità delle applicazioni per il flusso di lavoro di ripristino. La verifica del comportamento di un'applicazione e delle implicazioni legate alla perdita di dati e/o all'interruzione del servizio che comporta il failover è una buona norma di progettazione. Inoltre, questa verifica è richiesta dalla maggior parte degli standard del settore come parte della certificazione di continuità aziendale.

L'esercitazione per il ripristino di emergenza prevede l'esecuzione delle attività seguenti:

* Simulazione dell'interruzione del livello dati
* Ripristino
* Convalida dell'integrità dell'applicazione dopo il ripristino

A seconda della modalità di [progettazione dell’applicazione per la continuità aziendale](sql-database-business-continuity.md), il flusso di lavoro dell'esercitazione può variare. Questo articolo descrive le procedure consigliate per eseguire un test per il ripristino di emergenza nel contesto del database SQL di Azure.

## <a name="geo-restore"></a>Ripristino geografico

Per evitare il rischio di perdita di dati durante l'esecuzione di un test per il ripristino di emergenza, è consigliabile usare un ambiente di test creando una copia dell'ambiente di produzione e usandola per verificare il flusso di lavoro di failover dell'applicazione.

### <a name="outage-simulation"></a>Simulazione dell'interruzione del servizio

Per simulare l'interruzione è possibile rinominare il database di origine. Questa modifica del nome causa errori di connettività dell'applicazione.

### <a name="recovery"></a>Recupero

* Eseguire il ripristino geografico del database in un server diverso, come descritto [qui](sql-database-disaster-recovery.md).
* Modificare la configurazione dell'applicazione per connettersi ai database ripristinati e seguire la guida [Configurare un database dopo il ripristino](sql-database-disaster-recovery.md) per completare il ripristino.

### <a name="validation"></a>Convalida

Completare il test verificando l'integrità dell'applicazione dopo il ripristino, includendo stringhe di connessione, accessi, test di funzionalità di base o altre verifiche correlate alle procedure standard di signoff delle applicazioni.

## <a name="failover-groups"></a>Gruppi di failover

Per un database protetto mediante i gruppi di failover l'esercitazione comporta un failover pianificato per il server secondario. Il failover pianificato assicura che i database primario e secondario nel gruppo di failover restino sincronizzati quando si invertono i ruoli. A differenza del failover non pianificato, questa operazione non comporta la perdita di dati, quindi l'esercitazione può essere realizzata nell'ambiente di produzione.

### <a name="outage-simulation"></a>Simulazione dell'interruzione del servizio

Per simulare l'interruzione è possibile disabilitare l'applicazione web o la macchina virtuale connessa al database. Questa simulazione di interruzione determina errori di connettività per i client Web.

### <a name="recovery"></a>Recupero

* Assicurarsi che la configurazione dell'applicazione nell'area DR punti al database secondario precedente, che diventa il nuovo database primario completamente accessibile.
* Attivare il [failover pianificato](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) del gruppo di failover dal server secondario.
* Seguire la guida [Configurare un database dopo il ripristino](sql-database-disaster-recovery.md) per completare il ripristino.

### <a name="validation"></a>Convalida

Completare il test verificando l'integrità dell'applicazione dopo il ripristino, includendo connettività, test di funzionalità di base o altre verifiche necessarie per i signoff del test.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sugli scenari di continuità aziendale, vedere l'articolo relativo agli [scenari di continuità](sql-database-business-continuity.md).
* Per informazioni sui backup automatici del database SQL di Azure, vedere [Panoramica: Backup automatici del database SQL](sql-database-automated-backups.md)
* Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md).
* Per altre informazioni su opzioni di ripristino più veloci, vedere [Replica geografica attiva](sql-database-active-geo-replication.md) e [Gruppi di failover automatico](sql-database-auto-failover-group.md).
