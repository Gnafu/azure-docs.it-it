---
title: Limiti e quote della sottoscrizione di Azure
description: Fornisce un elenco di limiti, quote e vincoli comuni relativi alle sottoscrizioni e ai servizi di Azure. Sono incluse informazioni su come aumentare i limiti e i valori massimi.
services: multiple
author: rothja
manager: jeffreyg
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.topic: article
ms.date: 12/10/2018
ms.author: byvinyal
ms.openlocfilehash: 00930fd99fd4c0078623aa11f3e2cd5b1c549034
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812668"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Sottoscrizione di Azure e limiti, quote e vincoli dei servizi
In questo documento sono elencati alcuni dei limiti più comuni di Microsoft Azure, che vengono definiti anche quote. Al momento nel documento non vengono trattati tutti i servizi di Azure. Nel corso del tempo l'elenco verrà ampliato e aggiornato in modo da coprire un maggior numero di servizi della piattaforma.

Vedere [Prezzi di Azure](https://azure.microsoft.com/pricing/) per altre informazioni sui prezzi di Azure. Nella pagina è possibile stimare i costi usando il [Calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/) o visitando la pagina dei dettagli dei prezzi per un servizio, ad esempio [Macchine virtuali Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Per suggerimenti su come gestire i costi, vedere [Evitare costi imprevisti con la fatturazione del costi e la fatturazione di Azure](billing/billing-getting-started.md).

> [!NOTE]
> Per aumentare il limite o la quota oltre il valore **Limite predefinito**, è possibile [aprire una richiesta di assistenza clienti online senza alcun addebito](azure-resource-manager/resource-manager-quota-errors.md). I limiti non possono essere aumentati oltre il valore **Limite massimo** definito nelle tabelle seguenti. Se non è presente nessuna colonna **Limite massimo**, per la risorsa specificata non sono disponibili limiti regolabili.
>
> Le [sottoscrizioni per la versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p) non hanno i requisiti necessari per un aumento dei limiti o delle quote. Se si ha una [sottoscrizione per la versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p), è possibile eseguire l'aggiornamento a una sottoscrizione con [pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/). Per ulteriori informazioni, vedere [Aggiornare la versione di valutazione gratuita alla sottoscrizione con pagamento in base al consumo](billing/billing-upgrade-azure-subscription.md) e [Domande frequenti sulla sottoscrizione per la versione di valutazione gratuita](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-the-azure-resource-manager"></a>Limiti e Azure Resource Manager
È ora possibile combinare più risorse di Azure in un singolo gruppo di risorse di Azure. Quando si usano i gruppi di risorse, i limiti in precedenza globali vengono gestiti a livello di area con Gestione risorse di Azure. Per altre informazioni sui gruppi di risorse di Azure, vedere [Panoramica di Azure Resource Manager](azure-resource-manager/resource-group-overview.md).

Nei limiti indicati di seguito è stata aggiunta una nuova tabella che indica eventuali differenze applicate quando si usa Gestione risorse di Azure. Sono ad esempio presenti una tabella **Limiti delle sottoscrizioni** e una tabella **Limiti delle sottoscrizioni - Azure Resource Manager**. Quando un limite si applica a entrambi gli scenari, viene indicato solo nella prima tabella. Se non diversamente indicato, i limiti sono globali in tutte le aree.

> [!NOTE]
> È importante sottolineare che le quote per le risorse nei gruppi di risorse di Azure sono da intendersi per ogni area accessibile dalla sottoscrizione e non per ogni sottoscrizione, come nel caso delle quote di gestione del servizio. Si considerino, ad esempio, le quote di vCPU. Se è necessario richiedere un aumento della quota con supporto per le vCPU, è necessario stabilire quante vCPU si vuole usare e in quali aree e quindi effettuare una richiesta specifica per le quote di vCPU del gruppo di risorse di Azure per le quantità e le aree desiderate. Se quindi è necessario usare 30 vCPU in Europa occidentale per eseguire l'applicazione, è necessario richiedere in modo specifico 30 vCPU in Europa occidentale. La quota di vCPU per le altre aree non verrà tuttavia aumentata, ma sarà disponibile una quota di 30 vCPU solo in Europa occidentale.
> <!-- -->
> Di conseguenza, può risultare utile stabilire le quote per il gruppo di risorse di Azure necessarie per il carico di lavoro in ogni area e richiedere tale quantità in ogni area in cui si prevede di eseguire la distribuzione. Per altre informazioni su come individuare le quote correnti per aree specifiche, vedere l'argomento relativo alla [risoluzione dei problemi di distribuzione](resource-manager-common-deployment-errors.md) .
>
>

## <a name="service-specific-limits"></a>Limiti specifici del servizio
* [Active Directory](#active-directory-limits)
* [Gestione API](#api-management-limits)
* [Servizio app](#app-service-limits)
* [Gateway applicazione](#application-gateway-limits)
* [Application Insights](#application-insights-limits)
* [Automazione](#automation-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Database di Azure per MySQL](#azure-database-for-mysql)
* [Database di Azure per PostgreSQL](#azure-database-for-postgresql)
* [Griglia di eventi di Azure](#azure-event-grid-limits)
* [Mappe di Azure](#azure-maps-limits)
* [Monitoraggio di Azure](#monitor-limits)
* [Criteri di Azure](#azure-policy-limits)
* Cache Redis di Azure
* [Backup](#backup-limits)
* [Batch](#batch-limits)
* [Servizi BizTalk](#biztalk-services-limits)
* [RETE CDN](#cdn-limits)
* [Servizi cloud](#cloud-services-limits)
* [Istanze di Container](#container-instances-limits)
* [Registro Container](#container-registry-limits)
* [Kubernetes Service](#kubernetes-service-limits)
* [Data Factory](#data-factory-limits)
* [Analisi Data Lake](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Servizio Migrazione del database di Azure](#database-migration-service-limits)
* [DNS](#dns-limits)
* [Hub eventi](#event-hubs-limits)
* [Firewall di Azure](#azure-firewall-limits)
* [Frontdoor](#azure-front-door-service-limits)
* [Hub IoT](#iot-hub-limits)
* [Servizio Device Provisioning in hub IoT](#iot-hub-device-provisioning-service-limits)
* [Insieme di credenziali di chiave](#key-vault-limits)
* [Log Analytics](#log-analytics-limits)
* [Identità gestita](#managed-identity-limits)
* [Servizi multimediali](#media-services-limits)
* [Servizi mobili](#mobile-services-limits)
* [Autenticazione a più fattori](#multi-factor-authentication)
* [Rete](#networking-limits)
* [Network Watcher](#network-watcher-limits)
* [Servizio di Hub di notifica](#notification-hub-service-limits)
* [Gruppo di risorse](#resource-group-limits)
* [Controllo degli accessi in base al ruolo](#role-based-access-control-limits)
* [Utilità di pianificazione](#scheduler-limits)
* [Search](#search-limits)
* [Bus di servizio](#service-bus-limits)
* [Servizio SignalR](#signalr-service-limits)
* [Site Recovery](#site-recovery-limits)
* [Database SQL](#sql-database-limits)
* [SQL Data Warehouse](#sql-data-warehouse-limits)
* [Archiviazione](#storage-limits)
* [Sistema StorSimple](#storsimple-system-limits)
* [Analisi dei flussi](#stream-analytics-limits)
* [Sottoscrizione](#subscription-limits)
* [Gestione traffico](#traffic-manager-limits)
* [Macchine virtuali](#virtual-machines-limits)
* [Set di scalabilità di macchine virtuali](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Limiti delle sottoscrizioni
#### <a name="subscription-limits---azure-service-management-classic-resources"></a>Limiti delle sottoscrizioni - Gestione dei servizi di Azure (Risorse classiche)
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Limiti delle sottoscrizioni - Azure Resource Manager
I limiti seguenti si applicano quando si usano Gestione risorse di Azure e i gruppi di risorse di Azure. I limiti che non cambiano in caso di uso di Gestione risorse di Azure non sono elencati di seguito. Per tali limiti, fare riferimento alla tabella precedente.

Per informazioni sui limiti di lettura e scrittura dell'API di Gestione risorse, vedere [Limitazione delle richieste di Gestione risorse](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Limiti relativi a Gruppo di risorse
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Limiti relativi a Macchine virtuali
#### <a name="virtual-machine-limits"></a>Limiti relativi alla macchina virtuale
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Limiti relativi a Macchine virtuali - Gestione risorse di Azure
I limiti seguenti si applicano quando si usano Gestione risorse di Azure e i gruppi di risorse di Azure. I limiti che non cambiano in caso di uso di Gestione risorse di Azure non sono elencati di seguito. Per tali limiti, fare riferimento alla tabella precedente.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>Limiti dei set di scalabilità delle macchine virtuali
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Limiti per Istanze di Container
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Limiti per Registro contenitori
La tabella seguente illustra le funzionalità e i limiti dei [livelli di servizio](./container-registry/container-registry-skus.md) Base, Standard e Premium.

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="kubernetes-service-limits"></a>Limiti di Kubernetes Service
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="networking-limits"></a>Limiti relativi alla rete
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>Limiti relativi alla rete
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="application-gateway-limits"></a>Limiti del gateway applicazione

Se non diversamente specificato, la tabella è valida per gli SKU v1, v2, Standard e WAF.
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Limiti relativi a Network Watcher
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="traffic-manager-limits"></a>Limiti relativi a Gestione traffico
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>Limiti relativi a DNS
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Limiti relativi al Firewall di Azure
[!INCLUDE [azure-firewall-limits](../includes/firewall-limits.md)]

#### <a name="azure-front-door-service-limits"></a>Limiti relativi al servizio Frontdoor di Azure
[!INCLUDE [azure-front-door-service-limits](../includes/front-door-limits.md)]

### <a name="storage-limits"></a>Limiti relativi ad Archiviazione
<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

Per altre informazioni sui limiti dell'account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](storage/common/storage-scalability-targets.md).

#### <a name="storage-resource-provider-limits"></a>Limiti relativi al provider delle risorse di archiviazione 

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Limiti relativi ad Archiviazione BLOB di Azure
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Limiti relativi a File di Azure
Per altre informazioni sui limiti relativi a File di Azure, vedere [Azure Files scalability and performance targets](storage/files/storage-files-scale-targets.md) (Obiettivi di scalabilità e prestazioni per File di Azure).

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Limiti relativi a Sincronizzazione file di Azure
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Limiti relativi ad Archiviazione code di Azure
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Limiti relativi ad Archiviazione tabelle di Azure
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>Limiti relativi ai dischi della macchina virtuale
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Vedere [Dimensioni della macchina virtuale](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per altri dettagli.

#### <a name="managed-virtual-machine-disks"></a>Dischi delle macchine virtuali gestiti

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Dischi delle macchine virtuali non gestiti

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="cloud-services-limits"></a>Limiti relativi a Servizi cloud
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>Limiti relativi a Servizio app
I limiti del servizio App seguenti includono limiti per le App Web, App mobili e App API.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>Limiti relativi all'utilità di pianificazione
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Limiti relativi a Batch
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>Limiti relativi a Servizi BizTalk
La tabella seguente mostra i limiti per i servizi Biztalk di Azure.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Limiti relativi ad Azure Cosmos DB
Azure Cosmos DB è un database con scalabilità globale in cui la velocità effettiva e lo spazio di archiviazione possono essere ridimensionati in modo da gestire qualsiasi requisito dell'applicazione. Per eventuali domande sulla scalabilità offerta da Azure Cosmos DB, inviare un messaggio di posta elettronica all'indirizzo askcosmosdb@microsoft.com.

### <a name="azure-database-for-mysql"></a>Database di Azure per MySQL
Per i limiti del Database di Azure per MySQL, vedere [Limiti di Database di Azure per MySQL](mysql/concepts-limits.md).

### <a name="azure-database-for-postgresql"></a>Database di Azure per PostgreSQL
Per i limiti del Database di Azure per PostgreSQL, vedere [Limiti di Database di Azure per PostgreSQL](postgresql/concepts-limits.md).

### <a name="search-limits"></a>Limiti relativi a Ricerca
I piano tariffari determinano la capacità e i limiti del servizio di ricerca. Sono disponibili i piani seguenti:

* *Gratuito* , offre un servizio multi-tenant, condiviso con altri sottoscrittori di Azure, progettato per la valutazione e progetti di sviluppo di piccole dimensioni.
* *Basic* fornisce risorse di calcolo dedicate per i carichi di lavoro di produzione su scala più ridotta, con un massimo di 3 repliche per i carichi di lavoro di query a disponibilità elevata.
* *Standard (S1, S2, S3, S3 ad alta densità)* è per i carichi di lavoro di produzione più consistenti. All'interno del livello standard esistono più livelli per consentire di scegliere una configurazione delle risorse ottimale per il profilo di carico di lavoro.

**Limiti per ogni sottoscrizione**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Limiti per servizio di ricerca**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Per informazioni più dettagliati sui limiti, ad esempio dimensioni dei documenti, query al secondo, chiavi, richieste e risposte, vedere [Limiti dei servizi in Ricerca di Azure](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Limiti relativi a Servizi multimediali
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="cdn-limits"></a>Limiti relativi alla rete CDN
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Limiti relativi a Servizi mobili
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitor-limits"></a>Limiti relativi al monitoraggio
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>Limiti relativi al servizio di Hub di notifica
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Limiti relativi all'hub eventi
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Limiti relativi al bus di servizio
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>Limiti relativi all'hub IoT
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>Limiti relativi al servizio Device Provisioning in hub IoT
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>Limiti relativi a Data factory
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Limiti di Data Lake Analytics
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Limiti di Data Lake Store
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>Limiti relativi al Servizio Migrazione del database di Azure
[!INCLUDE [database-migration-service-limits](../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>Limiti relativi ad analisi di flusso
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Limiti relativi ad Active Directory
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="azure-event-grid-limits"></a>Limiti relativi a Griglia di eventi di Azure
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Limiti di Mappe di Azure
[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="azure-policy-limits"></a>Limiti di Criteri di Azure
[!INCLUDE [policy-limits](../includes/azure-policy-limits.md)]

### <a name="storsimple-system-limits"></a>Limiti relativi al sistema StorSimple
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="log-analytics-limits"></a>Limiti relativi a Log Analytics
[!INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Limiti relativi a Backup
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="signalr-service-limits"></a>Limiti relativi al servizio SignalR
[!INCLUDE [signalr-service-limits](../includes/signalr-service-limits.md)]

### <a name="site-recovery-limits"></a>Limiti relativi a Site Recovery
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Limiti relativi ad Application Insights
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>Limiti relativi a Gestione API
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-cache-for-redis-limits"></a>Cache Redis per limiti di Azure
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Limiti relativi all'insieme di credenziali delle chiavi
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Limiti di automazione
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="managed-identity-limits"></a>Limiti relativi all'identità gestita
[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>Limiti relativi al controllo degli accessi in base al ruolo
[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>Limiti relativi a database SQL
Per i limiti relativi al database SQL, vedere [Limiti delle risorse del database SQL per i database singoli](sql-database/sql-database-vcore-resource-limits-single-databases.md) e [Limiti delle risorse del database SQL per pool elastici e database in pool](sql-database/sql-database-vcore-resource-limits-elastic-pools.md).

### <a name="sql-data-warehouse-limits"></a>Limiti relativi a SQL Data Warehouse
Per i limiti relativi a SQL Data Warehouse, vedere [Limiti di capacità di SQL Data Warehouse](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="see-also"></a>Vedere anche 
[Informazioni sui limiti di Azure e su come aumentarli](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Dimensioni delle macchine virtuali e dei servizi cloud per Azure](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Dimensioni per i servizi cloud](cloud-services/cloud-services-sizes-specs.md)
