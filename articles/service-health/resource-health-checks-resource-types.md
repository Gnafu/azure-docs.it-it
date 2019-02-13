---
title: Tipi di risorse supportati tramite Integrità risorse di Azure|Microsoft Docs
description: Tipi di risorse supportati tramite Integrità risorse di Azure
services: Resource health
documentationcenter: ''
author: stephbaron
manager: ''
editor: ''
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 01/29/2019
ms.author: stbaron
ms.openlocfilehash: d88eb0ee5e8ec4c14efeb47a144ec7080ac6eee3
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55508794"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Tipi di risorse e controlli di integrità in Integrità risorse di Azure
Di seguito è riportato un elenco completo di tutti i controlli eseguiti tramite Integrità risorse suddivisi per tipi di risorse.

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers
|Controlli eseguiti|
|---|
|<ul><li>Il server è operativo?</li><li>La memoria del server è insufficiente?</li><li>Il server è in fase di avvio?</li><li>Il server è in fase di recupero?</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Controlli eseguiti|
|---|
|<ul><li>Il servizio Gestione API è operativo?</li></ul>|

## <a name="microsoftcacheredisredis"></a>Microsoft.CacheRedis/Redis
|Controlli eseguiti|
|---|
|<ul><li>Tutti i nodi della cache sono operativi?</li><li>La cache è raggiungibile dal data center?</li><li>La cache ha raggiunto il numero massimo di connessioni?</li><li> La cache ha esaurito la memoria disponibile? </li><li>La cache sta riscontrando un numero elevato di errori di pagina?</li><li>Il carico della cache è elevato?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|Controlli eseguiti|
|---|
|<ul> <li>Il portale supplementare è accessibile per le operazioni di configurazione della rete CDN?</li><li>Si stanno verificando problemi di recapito con gli endpoint della rete CDN?</li><li>Gli utenti possono modificare la configurazione delle risorse della rete CDN?</li><li>Le modifiche di configurazione si propagano come previsto?</li><li>Gli utenti possono gestire la configurazione della rete CDN tramite il portale di Azure, PowerShell o l'API?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Controlli eseguiti|
|---|
|<ul><li>Il server host è operativo?</li><li>L'avvio del sistema operativo host è stato completato?</li><li>Sono stati eseguiti il provisioning e l'accensione del contenitore della macchina virtuale?</li><li>È disponibile la connettività di rete tra l'host e l'account di archiviazione?</li><li>L'avvio del sistema operativo guest è stato completato?</li><li>È in corso la manutenzione pianificata?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|Controlli eseguiti|
|---|
|<ul><li>L'account è raggiungibile dal data center?</li><li>È disponibile il provider di risorse Servizi cognitivi?</li><li>I Servizi cognitivi sono disponibili nell'area appropriata?</li><li>Le operazioni di lettura possono essere eseguite nell'account di archiviazione contenente i metadati delle risorse?</li><li>È stata raggiunta la quota di chiamate dell'API?</li><li>È stato raggiunto il limite di lettura delle chiamate dell'API?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|Controlli eseguiti|
|---|
|<ul><li>Il server che ospita la macchina virtuale è operativo?</li><li>L'avvio del sistema operativo host è stato completato?</li><li>Sono stati eseguiti il provisioning e l'accensione del contenitore della macchina virtuale?</li><li>È disponibile la connettività di rete tra l'host e l'account di archiviazione?</li><li>L'avvio del sistema operativo guest è stato completato?</li><li>È in corso la manutenzione pianificata?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|Controlli eseguiti|
|---|
|<ul><li>Gli utenti hanno riscontrato problemi nell'inviare o elencare i processi di Data Lake Analytics?</li><li>I processi di Data Lake Analytics non vengono completati a causa di errori del sistema?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|Controlli eseguiti|
|---|
|<ul><li>Gli utenti riscontrano problemi quando caricano i dati in Data Lake Store?</li><li>Gli utenti riscontrano problemi quando scaricano i dati da Data Lake Store?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs

|Controlli eseguiti|
|---|
|<ul><li>L'hub IoT è operativo?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Controlli eseguiti|
|---|
|<ul><li>Ci sono state richieste di database o raccolta non soddisfatte a causa della mancata disponibilità di un servizio di Azure Cosmos DB?</li><li>Ci sono state richieste di documenti non soddisfatte a causa della mancata disponibilità di un servizio di Azure Cosmos DB?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults
|Controlli eseguiti|
|---|
|<ul><li>Esistono richieste all'insieme di credenziali delle chiavi non riuscite a causa di problemi della piattaforma di Azure Key Vault?</li><li>Le richieste all'insieme di credenziali delle chiavi vengono limitate a causa di un numero eccessivo di richieste effettuate dal cliente?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|Controlli eseguiti|
|---|
|<ul><li>Il tunnel VPN è connesso?</li><li>Sono presenti conflitti di configurazione nella connessione?</li><li>Le chiavi precondivise sono configurate correttamente?</li><li>Il dispositivo VPN locale è raggiungibile?</li><li>Esistono mancate corrispondenze nei criteri di sicurezza IPSec/IKE?</li><li>Il provisioning della connessione VPN S2S è stato eseguito correttamente oppure la connessione è in stato di errore?</li><li>Il provisioning della connessione da rete virtuale a rete virtuale è stato eseguito correttamente oppure la connessione è in stato di errore?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Controlli eseguiti|
|---|
|<ul><li>Il gateway VPN è raggiungibile da Internet?</li><li>Il gateway VPN è in modalità standby?</li><li>Il servizio VPN è in esecuzione nel gateway?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|Controlli eseguiti|
|---|
|<ul><li> Le operazioni di runtime come la registrazione, l'installazione o l'invio possono essere eseguite nello spazio dei nomi?</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/Capacities
|Controlli eseguiti|
|---|
|<ul><li>La risorsa capacità è operativa?</li><li>Tutti i carichi di lavoro sono operativi?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|Controlli eseguiti|
|---|
|<ul><li>Il sistema operativo host è operativo?</li><li>La raccolta di aree di lavoro è raggiungibile dall'esterno del data center?</li><li>Il provider di risorse PowerBI è disponibile?</li><li>Il servizio PowerBI è disponibile nell'area appropriata?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Controlli eseguiti|
|---|
|<ul><li>Le operazioni di diagnostica possono essere eseguite nel cluster?</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces
|Controlli eseguiti|
|---|
|<ul><li>I clienti riscontrano errori del bus di servizio generati dall'utente?</li><li>Gli utenti riscontrano un aumento degli errori temporanei a causa di un aggiornamento dello spazio dei nomi del bus di servizio?</li></ul>|

## <a name="microsoftsqlserverdatabase"></a>Microsoft.SQL/Server/database
|Controlli eseguiti|
|---|
|<ul><li> Sono stati eseguiti accessi al database?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts
|Controlli eseguiti|
|---|
|<ul><li>Esistono richieste per leggere i dati dall'account di archiviazione non riuscite a causa di problemi della piattaforma di Archiviazione di Azure?</li><li>Esistono richieste per scrivere i dati nell'account di archiviazione non riuscite a causa di problemi della piattaforma di Archiviazione di Azure?</li><li>Il cluster di archiviazione in cui risiede l'account di archiviazione non è disponibile?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Controlli eseguiti|
|---|
|<ul><li>Gli host in cui il processo è in esecuzione sono operativi?</li><li>Non è stato possibile avviare il processo?</li><li>Sono in corso aggiornamenti di runtime?</li><li>Il processo si trova in uno stato previsto, ad esempio in esecuzione o arrestato dal cliente?</li><li>Si sono verificate eccezioni di memoria insufficiente per il processo?</li><li>Sono in corso aggiornamenti di calcolo pianificati?</li><li>È disponibile Gestione esecuzioni (piano di controllo)?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Controlli eseguiti|
|---|
|<ul><li>Il server host è operativo?</li><li>Internet Information Services è in esecuzione?</li><li>Il bilanciamento del carico è in esecuzione?</li><li>Il piano del servizio app è raggiungibile dal data center?</li><li>L'account di archiviazione che ospita il contenuto dei siti per ServerFarm è disponibile?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|Controlli eseguiti|
|---|
|<ul><li>Il server host è operativo?</li><li>Internet Information Server è in esecuzione?</li><li>Il bilanciamento del carico è in esecuzione?</li><li>L'app Web è raggiungibile dal data center?</li><li>L'account di archiviazione che ospita il contenuto del sito è disponibile?</li></ul>|

# <a name="next-steps"></a>Passaggi successivi
-  Vedere [Introduzione al dashboard per l'integrità dei servizi di Azure](service-health-overview.md) e [Introduzione a Integrità risorse di Azure](resource-health-overview.md) per informazioni più specifiche. 
-  [Domande frequenti su Integrità risorse di Azure](resource-health-faq.md)
- Impostare gli avvisi per ricevere notifiche sui problemi di integrità. Per altre informazioni, vedere [Configurare gli avvisi per gli eventi di Integrità dei servizi](../azure-monitor/platform/alerts-activity-log-service-notifications.md). 
