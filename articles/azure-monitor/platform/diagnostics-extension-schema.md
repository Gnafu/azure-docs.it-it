---
title: Cronologia delle versioni dello schema di configurazione dell'estensione Diagnostica di Azure
description: Pertinenti per la raccolta di contatori delle prestazioni in Macchine virtuali, set di scalabilità di macchine virtuali di Microsoft Azur, Service Fabric e Servizi cloud di Azure.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/04/2019
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: e8ea8ea749243821e5382fc285e3c38f05d4c6b5
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735080"
---
# <a name="azure-diagnostics-extension-configuration-schema-versions-and-history"></a>Versioni e cronologia degli schemi di configurazione dell'estensione di Diagnostica di Azure
Questa pagina rappresenta l'indice delle versioni degli schemi di estensione di Diagnostica di Azure forniti con Microsoft Azure SDK.  

> [!NOTE]
> Il componente estensione di Diagnostica di Azure viene usato per raccogliere i contatori delle prestazioni e altre statistiche da:
> - Macchine virtuali di Azure
> - Set di scalabilità di macchine virtuali
> - Service Fabric
> - Servizi cloud
> - Gruppi di sicurezza di rete
>
> Questa pagina è utile solo se si usa uno di questi servizi.

L'estensione Diagnostica di Azure viene usata con altri prodotti di diagnostica Microsoft, come monitoraggio di Azure, che include Application Insights e Log Analytics. Per altre informazioni, vedere [Panoramica degli strumenti di monitoraggio Microsoft](../../azure-monitor/overview.md).

## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Grafico delle versioni fornite per Azure SDK e Diagnostica  

|Versione di Azure SDK | Versione dell'estensione di Diagnostica | Modello|  
|------------------|-------------------------------|------|  
|1.x               |1.0                            |plug-in|  
|2.0 - 2.4         |1.0                            |plug-in|  
|2.5               |1.2                            |estensione|  
|2.6               |1.3                            |"|  
|2.7               |1.4                            |"|  
|2.8               |1,5                            |"|  
|2,9               |1.6                            |"|
|2.96              |1.7                            |"|
|2.96              |1.8                            |"|
|2.96              |1.8.1                          |"|
|2.96              |1.9                            |"|
|2.96              |1.11                           |"|


 Diagnostica di Azure versione 1.0 era inizialmente disponibile in un modello plug-in, vale a dire che con l'installazione del pacchetto Azure SDK si otteneva la versione di Diagnostica di Azure disponibile al suo interno.  

 A partire dall'SDK 2.5 (diagnostica versione 1.2) lo strumento Diagnostica di Azure è passata a un modello di estensione. Gli strumenti per usare le nuove funzionalità erano disponibili solo nei pacchetti Azure SDK più recenti, ma qualsiasi servizio che usava Diagnostica di Azure prelevava la versione più recente direttamente da Azure. Gli utenti che ancora usano SDK 2.5 caricano la versione più recente indicata nella tabella precedente, a prescindere dalla circostanza che usino o meno le funzionalità più recenti.  

## <a name="schemas-index"></a>Indice degli schemi  
Versioni diverse di Diagnostica di Azure usano schemi di configurazione diversi. Gli schemi 1,0 e 1,2 sono stati deprecati. Per ulteriori informazioni sulla versione 1,3 e successive, vedere la pagina relativa [allo schema di configurazione di diagnostica 1,3 e versioni successive](diagnostics-extension-schema-1dot3.md)  

## <a name="version-history"></a>Cronologia delle versioni

### <a name="diagnostics-extension-111"></a>Estensione di Diagnostica 1.11
Aggiunta del supporto per il sink di Monitoraggio di Azure. Questo sink è applicabile solo ai contatori delle prestazioni. Consente di inviare i contatori delle prestazioni raccolti nella macchina virtuale, VMSS o servizio cloud a Monitoraggio di Azure come metriche personalizzate. Il sink di Monitoraggio di Azure supporta:
* Il recupero di tutti i contatori delle prestazioni inviati a Monitoraggio di Azure tramite le [API di metrica di Monitoraggio di Azure.](https://docs.microsoft.com/rest/api/monitor/metrics/list)
* La visualizzazione di avvisi su tutti i contatori delle prestazioni inviati a Monitoraggio di Azure tramite la nuova [esperienza di avvisi unificati](../../azure-monitor/platform/alerts-overview.md) in Monitoraggio di Azure
* La considerazione dell'operatore carattere jolly nei contatori delle prestazioni come la dimensione "Istanza" per la metrica. Ad esempio se è stato raccolto il contatore "LogicalDisk(\*)/DiskWrites/sec" sarà possibile filtrare e suddividere la dimensione "Istanza" del tracciato o avviso su Disk Writes/sec per ogni disco logico (C:, D:, e così via)

Definire Monitoraggio di Azure come un nuovo sink nella configurazione dell'estensione di diagnostica
```json
"SinksConfig": {
    "Sink": [
        {
            "name": "AzureMonitorSink",
            "AzureMonitor": {}
        },
    ]
}
```

```XML
<SinksConfig>  
  <Sink name="AzureMonitorSink">
      <AzureMonitor/>
  </Sink>
</SinksConfig>
```
> [!NOTE]
> La configurazione del sink di Monitoraggio di Azure per le macchine virtuali e per il servizio cloud classico richiede la definizione di più parametri nella configurazione privata dell'estensione Diagnostica.
>
> Per altri dettagli, consultare la [documentazione dello schema dell'estensione diagnostica dettagliata.](diagnostics-extension-schema-1dot3.md)

Successivamente, è possibile configurare i contatori delle prestazioni da instradare al sink di Monitoraggio di Azure.
```json
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "AzureMonitorSink",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT1M",
            "unit": "percent"
        }
    ]
},
```
```XML
<PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
</PerformanceCounters>
```

### <a name="diagnostics-extension-19"></a>Estensione di Diagnostica 1.9
Aggiunta del supporto Docker.


### <a name="diagnostics-extension-181"></a>Estensione di Diagnostica 1.8.1
Possibilità di specificare un token di firma di accesso condiviso anziché una chiave dell'account di archiviazione in PrivateConfig. Se viene fornito un token di firma di accesso condiviso, la chiave dell'account di archiviazione viene ignorata.


```json
{
    "storageAccountName": "diagstorageaccount",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    }
}
```

```xml
<PrivateConfig>
    <StorageAccount name="diagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    <SecondaryStorageAccounts>
        <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>
</PrivateConfig>
```


### <a name="diagnostics-extension-18"></a>Estensione di Diagnostica 1.8
Aggiunta del tipo di archiviazione in PublicConfig. Il tipo di archiviazione può essere *Table*, *Blob* e *TableAndBlob*. *Table* è l'impostazione predefinita.


```json
{
    "WadCfg": {
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

```xml
<PublicConfig>
    <WadCfg />
    <StorageAccount>diagstorageaccount</StorageAccount>
    <StorageType>TableAndBlob</StorageType>
</PublicConfig>
```


### <a name="diagnostics-extension-17"></a>Estensione di Diagnostica 1.7
Aggiunta della possibilità di eseguire l'indirizzamento a EventHub.

### <a name="diagnostics-extension-15"></a>Estensione di Diagnostica 1.5
L'aggiunta dell'elemento sink e la possibilità di inviare dati di diagnostica ad [Application Insights](../../azure-monitor/app/cloudservices.md) semplificano la diagnosi dei problemi nell'intera applicazione, a livello di sistema e infrastruttura.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>Azure SDK 2.6 ed estensione di Diagnostica 1.3
Sono state apportate le modifiche seguenti ai progetti dei servizi cloud in Visual Studio. Queste modifiche sono applicabili anche alle versioni successive di Azure SDK.

* L'emulatore locale supporta ora la diagnostica. È quindi possibile raccogliere dati di diagnostica e assicurare che l'applicazione crei le tracce corrette durante lo sviluppo e il testing in Visual Studio. La stringa di connessione `UseDevelopmentStorage=true` abilita la raccolta di dati di diagnostica durante l'esecuzione del progetto servizio cloud in Visual Studio mediante l'emulatore di archiviazione di Azure. Tutti i dati di diagnostica vengono raccolti nell'account di archiviazione (archivio di sviluppo).
* La stringa di connessione dell'account di archiviazione di diagnostica (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) viene archiviata ancora una volta nel file di configurazione (.cscfg) del servizio. In Azure SDK 2.5 l'account di archiviazione di diagnostica viene specificato nel file diagnostics.wadcfgx.

Il funzionamento della stringa di connessione presenta alcune differenze sostanziali tra Azure SDK 2.4 e versioni precedenti e Azure SDK 2.6 e versioni successive.

* In Azure SDK 2.4 e versioni precedenti, la stringa di connessione viene usata in fase di runtime dal plug-in di diagnostica per ottenere le informazioni sull'account di archiviazione per il trasferimento dei log di diagnostica.
* In Azure SDK 2.6 e versioni successive, Visual Studio usa la stringa di connessione di diagnostica per configurare l'estensione di diagnostica con le informazioni appropriate sull'account di archiviazione durante la pubblicazione. La stringa di connessione consente di definire diversi account di archiviazione per diverse configurazioni del servizio, che verranno usate da Visual Studio durante la pubblicazione. Poiché, tuttavia, il plug-in di diagnostica non è più disponibile (dopo Azure SDK 2.5), il solo file .cscfg non è in grado di abilitare l'estensione di diagnostica. È necessario abilitare l'estensione separatamente tramite strumenti quali Visual Studio o PowerShell.
* Per semplificare il processo di configurazione dell'estensione di diagnostica con PowerShell, l'output del pacchetto da Visual Studio contiene anche il codice XML di configurazione pubblica per l'estensione di diagnostica per ogni ruolo. Visual Studio usa la stringa di connessione di diagnostica per popolare le informazioni sull'account di archiviazione presenti nella configurazione pubblica. I file di configurazione pubblica vengono creati nella cartella Extensions e seguono il modello `PaaSDiagnostics.<RoleName>.PubConfig.xml`. Eventuali distribuzioni basate su PowerShell possono usare questo modello per il mapping di ogni configurazione a un ruolo.
* La stringa di connessione nel file .cscfg viene usata anche dal portale di Azure per accedere ai dati di diagnostica, per consentirne la visualizzazione nella scheda **Monitoraggio** . La stringa di connessione è necessaria per configurare il servizio, in modo da visualizzare i dati dettagliati del monitoraggio nel portale.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migrazione di progetti in Azure SDK 2.6 e versioni successive
Quando si esegue la migrazione da Azure SDK 2.5 ad Azure SDK 2.6 o versioni successivi, eventuali account di archiviazione di diagnostica specificati nel file con estensione wadcfgx rimarranno in tale posizione. Per sfruttare la flessibilità di utilizzo di account di archiviazione diversi per configurazioni di archiviazione diverse, è necessario aggiungere manualmente la stringa di connessione al progetto. Se si sta eseguendo la migrazione di un progetto da Azure SDK 2.4 o versioni precedenti ad Azure SDK 2.6, le stringhe di connessione di diagnostica vengono mantenute. È tuttavia opportuno notare la diversa modalità di gestione delle stringhe di connessione in Azure SDK 2.6, come illustrato nella sezione precedente.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Modalità di determinazione dell'account di archiviazione di diagnostica da parte di Visual Studio
* Se nel file .cscfg viene specificata una stringa di connessione di diagnostica, Visual Studio la userà per configurare l'estensione di diagnostica durante la pubblicazione e durante la generazione dei file XML di configurazione pubblica nel corso della creazione dei pacchetti.
* Se nel file .cscfg non viene specificata alcuna stringa di connessione di diagnostica, Visual Studio userà di nuovo l'account di archiviazione specificato nel file con estensione wadcfgx per configurare l'estensione di diagnostica durante la pubblicazione e durante la generazione dei file XML di configurazione pubblica nel corso della creazione dei pacchetti.
* La stringa di connessione di diagnostica nel file .cscfg ha la priorità rispetto all'account di archiviazione specificato nel file con estensione wadcfgx. Se nel file .cscfg viene specificata una stringa di connessione di diagnostica, Visual Studio userà tale stringa e ignorerà l'account di archiviazione specificato nel file con estensione wadcfgx.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>Qual è la funzione della casella di controllo "Aggiorna le stringhe di connessione dell'archivio di sviluppo"?
La casella di controllo **Aggiorna le stringhe di connessione dell'archivio di sviluppo per la diagnostica e la memorizzazione nella cache con le credenziali dell'account di archiviazione di Microsoft Azure quando si esegue la pubblicazione in Microsoft Azure** offre un modo semplice per aggiornare eventuali stringhe di connessione dell'account di archiviazione con l'account di archiviazione di Azure specificato durante la pubblicazione.

Ad esempio, si supponga di selezionare questa casella di controllo e che la stringa di connessione di diagnostica specifichi `UseDevelopmentStorage=true`. Quando si pubblica il progetto in Azure, Visual Studio aggiornerà automaticamente la stringa di connessione di diagnostica con l'account di archiviazione specificato nella Pubblicazione guidata. Se, tuttavia, un account di archiviazione effettivo è stato specificato come stringa di connessione di diagnostica, verrà usato invece tale account.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Differenze della funzionalità di diagnostica tra Azure SDK 2.4 e versioni precedenti e Azure SDK 2.5 e versioni successive
Se si aggiorna il progetto da Azure SDK 2.4 ad Azure SDK 2.5 o versioni successive, è consigliabile ricordare le differenze seguenti a livello di funzionalità di diagnostica.

* **Le API di configurazione sono deprecate** : la configurazione a livello di codice della diagnostica è disponibile in Azure SDK 2.4 o versioni precedenti, ma è deprecata in Azure SDK 2.5 e versioni successive. Se la configurazione di diagnostica è attualmente definita nel codice, sarà necessario riconfigurare completamente tali impostazioni nel progetto sottoposto a migrazione, in modo da mantenere il funzionamento della diagnostica. Il file di configurazione della diagnostica per Azure SDK 2.4 è diagnostics.wadcfg, mentre è diagnostics.wadcfgx per Azure SDK 2.5 e versioni successive.
* **La diagnostica per le applicazioni del servizio cloud può essere configurata solo a livello di ruolo, non a livello di istanza.**
* **Ogni volta che si distribuisce l'app, le configurazioni di diagnostica vengono aggiornate** : ciò può provocare problemi di parità se si modifica la configurazione di diagnostica da Esplora server e quindi si ridistribuisce l'app.
* **In Azure SDK 2.5 e versioni successive i dump di arresto anomalo del sistema sono configurati nel file di configurazione, non nel codice** : se i dump di arresto anomalo del sistema sono configurati nel codice, sarà necessario trasferire manualmente la configurazione dal codice al file di configurazione, perché i dump di arresto anomalo del sistema non vengono trasferiti durante la migrazione in Azure SDK 2.6.

