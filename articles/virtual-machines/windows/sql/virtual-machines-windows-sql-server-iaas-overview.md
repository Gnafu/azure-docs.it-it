---
title: Panoramica di SQL Server nelle macchine virtuali Windows di Azure | Microsoft Docs
description: Informazioni su come eseguire le edizioni complete di SQL Server in Macchine virtuali di Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 99c4f0f99af61196cf1a12f2f68a7d10d8b2e6c7
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58481281"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>Cos'è SQL Server in Macchine virtuali di Azure? (Windows)

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)

[SQL Server in Macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) consente di usare le versioni complete di SQL Server nel cloud senza dover gestire hardware locale. Le VM di SQL Server semplificano anche i costi di licenza quando si sceglie il modello con pagamento in base al consumo.

Le macchine virtuali di Azure vengono eseguite in diverse [aree geografiche](https://azure.microsoft.com/regions/) in tutto il mondo. Offrono anche un'ampia gamma di [dimensioni](../sizes.md). La raccolta di immagini di macchine virtuali consente di creare una VM di SQL Server con la versione, l'edizione e il sistema operativo appropriati. Le macchine virtuali possono così rappresentare una valida opzione per numerosi carichi di lavoro di SQL Server diversi.

## <a name="automated-updates"></a>Aggiornamenti automatici

Le VM di SQL Server Azure possono usare l'[applicazione automatica delle patch](virtual-machines-windows-sql-automated-patching.md) per pianificare una finestra di manutenzione per l'installazione automatica di importanti aggiornamenti di Windows e SQL Server.

## <a name="automated-backups"></a>Backup automatizzati

Le VM di SQL Server Azure possono sfruttare il [backup automatico](virtual-machines-windows-sql-automated-backup-v2.md), che crea periodicamente backup del database nell'archiviazione BLOB. È anche possibile usare questa tecnica manualmente. Per altre informazioni, vedere [Usare Archiviazione di Azure per il backup e il ripristino di SQL Server](virtual-machines-windows-use-storage-sql-server-backup-restore.md).

## <a name="high-availability"></a>Disponibilità elevata

Se è necessaria la disponibilità elevata, è consigliabile configurare i gruppi di disponibilità di SQL Server. Ciò richiede la presenza di più VM di SQL Server Azure in una rete virtuale. È possibile configurare la soluzione a disponibilità elevata manualmente oppure è possibile usare i modelli nel portale di Azure per la configurazione automatica. Per una panoramica di tutte le opzioni di disponibilità elevata, vedere [Disponibilità elevata e ripristino di emergenza per SQL Server nelle macchine virtuali di Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="performance"></a>Prestazioni

Le macchine virtuali di Azure sono disponibili in più dimensioni per soddisfare le diverse esigenze di carico di lavoro. Le VM SQL forniscono anche la configurazione automatica dell'archiviazione, ottimizzata in base ai requisiti di prestazioni. Per altre informazioni sulla configurazione dell'archiviazione per le VM SQL, vedere [Configurazione dell'archiviazione per le VM di SQL Server](virtual-machines-windows-sql-server-storage-configuration.md). Per ottimizzare le prestazioni, vedere [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-performance.md).

## <a name="get-started-with-sql-vms"></a>Introduzione alle VM di SQL

Per iniziare, scegliere un'immagine di macchina virtuale di SQL Server con la versione, l'edizione e il sistema operativo appropriati. Le sezioni seguenti forniscono collegamenti diretti al portale di Azure per le immagini della raccolta di macchine virtuali di SQL Server.

> [!TIP]
> Per altre informazioni sui prezzi delle immagini SQL, vedere [Guida ai prezzi per le VM di SQL Server in Azure](virtual-machines-windows-sql-server-pricing-guidance.md). 

### <a id="payasyougo"></a> Pagamento in base al consumo
La tabella seguente fornisce una matrice delle immagini di SQL Server con pagamento in base al consumo.

| Versione | Sistema operativo | Edizione |
| --- | --- | --- |
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

Per le immagini di macchine virtuali SQL Server disponibili per Linux, vedere [Panoramica di SQL Server in macchine virtuali di Azure (Linux)](../../linux/sql/sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Per usare la licenza personale, è ora possibile modificare il modello di licenza di una macchina virtuale di SQL Server con pagamento in base all'utilizzo. Per altre informazioni, vedere [Come cambiare il modello di licenza per una macchina virtuale SQL](virtual-machines-windows-sql-ahb.md). 

### <a id="BYOL"></a> Bring Your Own License
È anche possibile scegliere l'opzione Bring Your Own License (BYOL). In questo scenario si paga solo per la VM senza altre spese per le licenze di SQL Server.  L'opzione Bring Your Own License consente di risparmiare denaro nel tempo per i carichi di lavoro di produzione continui. Per i requisiti per questa opzione, vedere [Guida ai prezzi per le VM di SQL Server in Azure](virtual-machines-windows-sql-server-pricing-guidance.md#byol).

Per usare l'opzione Bring Your Own License, è possibile convertire una macchina virtuale SQL con pagamento in base all'utilizzo esistente oppure distribuire un'immagine con il prefisso **{BYOL}**. Per altre informazioni sul passaggio tra il modello di licenza con pagamento in base all'utilizzo e BYOL, vedere [Come cambiare il livello di licenza per una macchina virtuale SQL](virtual-machines-windows-sql-ahb.md). 

| Versione | Sistema operativo | Edizione |
| --- | --- | --- |
| **SQL Server 2017** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |

È possibile distribuire un'immagine meno recente di SQL Server che non è disponibile nel portale di Azure usando PowerShell. Per visualizzare tutte le immagini disponibili con PowerShell, usare il comando seguente:

  ```powershell
  Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
  ```

Per altre informazioni sulla distribuzione di macchine virtuali di SQL Server usando PowerShell, vedere [Come eseguire il provisioning di macchine virtuali di SQL Server con Azure PowerShell](virtual-machines-windows-ps-sql-create.md).


### <a name="connect-to-the-vm"></a>Connettersi alla VM
Una volta creata la VM di SQL Server, stabilire la connessione con applicazioni o strumenti come SQL Server Management Studio (SSMS). Per istruzioni, vedere [Connettersi a una macchina virtuale di SQL Server in Azure ](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Migrare i dati
Se esiste già un database, è possibile spostarlo nella nuova VM di SQL di cui viene effettuato il provisioning. Per le linee guida e un elenco di opzioni per la migrazione, vedere [Eseguire la migrazione di un database di SQL Server a SQL Server in una macchina virtuale di Azure](virtual-machines-windows-migrate-sql.md).

## <a id="lifecycle"></a> Criteri di aggiornamento delle immagini SQL delle VM
Azure gestisce una sola immagine di macchina virtuale per ogni combinazione supportata di sistema operativo, versione ed edizione. Ciò significa che le immagini vengono aggiornate nel tempo e quelle meno recenti vengono rimosse. Per altre informazioni, vedere la sezione **Immagini** delle [domande frequenti sulle macchine virtuali SQL Server](virtual-machines-windows-sql-server-iaas-faq.md#images).

## <a name="customer-experience-improvement-program-ceip"></a>Analisi utilizzo software
Il programma Analisi utilizzo software è abilitato per impostazione predefinita. Invia periodicamente report a Microsoft per contribuire a migliorare SQL Server. Non sono necessarie attività di gestione per il programma Analisi utilizzo software, a meno che non lo si voglia disabilitare dopo il provisioning. È possibile personalizzare o disabilitare Analisi utilizzo software connettendosi alla VM con Desktop remoto. Eseguire quindi l'utilità **Segnalazione errori e utilizzo funzionalità di SQL Server** . Seguire le istruzioni per disabilitare il reporting. Per altre informazioni sulla raccolta di dati, vedere l'[informativa sulla privacy di SQL Server](https://docs.microsoft.com/sql/getting-started/microsoft-sql-server-privacy-statement).

## <a name="related-products-and-services"></a>Prodotti e servizi correlati
### <a name="windows-virtual-machines"></a>Macchine virtuali Windows
* [Panoramica di Macchine virtuali](../overview.md)

### <a name="storage"></a>Archiviazione
* [Introduzione ad Archiviazione di Microsoft Azure](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Rete
* [Panoramica della rete virtuale](../../../virtual-network/virtual-networks-overview.md)
* [Indirizzi IP in Azure](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Creare un nome di dominio completo nel portale di Azure](../portal-create-fqdn.md)

### <a name="sql"></a>SQL
* [Documentazione di SQL Server](https://docs.microsoft.com/sql/index)
* [Confronto con il database SQL di Azure](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

## <a name="next-steps"></a>Passaggi successivi

Introduzione a SQL Server in Macchine virtuali di Azure:

* [Creare una VM di SQL Server nel portale di Azure](quickstart-sql-vm-create-portal.md)

Risposte alle domande comuni sulle VM SQL:

* [Domande frequenti su SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-faq.md)
