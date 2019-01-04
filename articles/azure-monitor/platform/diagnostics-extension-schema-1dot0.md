---
title: Schema di configurazione di Diagnostica di Azure 1.0
description: Utile SOLO se si usa Azure SDK 2.4 e versioni precedenti con le macchine virtuali di Azure, il set di scalabilità di macchine virtuali, Service Fabric o servizi Cloud.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 333fec5b9459979d7735f0dd292d7367d86353fe
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53325527"
---
# <a name="azure-diagnostics-10-configuration-schema"></a>Schema di configurazione di Diagnostica di Azure 1.0
> [!NOTE]
> Diagnostica di Azure è il componente usato per raccogliere i contatori delle prestazioni e altre statistiche da Macchine virtuali, set di scalabilità di macchine virtuali, Service Fabric e Servizi cloud di Azure.  Questa pagina è utile solo se si usa uno di questi servizi.
>

Lo strumento Diagnostica di Azure viene usato con altri prodotti di diagnostica Microsoft, quali Monitoraggio di Azure, Application Insights e Log Analytics.

Il file di configurazione di Diagnostica di Azure definisce i valori usati per inizializzare il monitor di diagnostica. Il file viene usato per inizializzare le impostazioni di diagnostica quando viene avviato il monitor di diagnostica.  

 Per impostazione predefinita, il file dello schema di configurazione di Diagnostica di Azure viene installato nella directory `C:\Program Files\Microsoft SDKs\Azure\.NET SDK\<version>\schemas`. Sostituire `<version>` con la versione installata di [Azure SDK](http://www.windowsazure.com/develop/downloads/).  

> [!NOTE]
>  Il file di configurazione della diagnostica viene generalmente usato con le attività di avvio che richiedono la raccolta dei dati di diagnostica in una fase precedente del processo di avvio. Per altre informazioni sull'uso di Diagnostica di Azure, vedere [Raccogliere dati di registrazione usando Diagnostica di Azure](assetId:///83a91c23-5ca2-4fc9-8df3-62036c37a3d7).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Esempio del file di configurazione della diagnostica  
 L'esempio seguente illustra un tipico file di configurazione della diagnostica:  

```xml  
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"  
      configurationChangePollInterval="PT1M"  
      overallQuotaInMB="4096">  
   <DiagnosticInfrastructureLogs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  
   <Logs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  

   <Directories bufferQuotaInMB="1024"   
      scheduledTransferPeriod="PT1M">  

      <!-- These three elements specify the special directories   
           that are set up for the log types -->  
      <CrashDumps container="wad-crash-dumps" directoryQuotaInMB="256" />  
      <FailedRequestLogs container="wad-frq" directoryQuotaInMB="256" />  
      <IISLogs container="wad-iis" directoryQuotaInMB="256" />  

      <!-- For regular directories the DataSources element is used -->  
      <DataSources>  
         <DirectoryConfiguration container="wad-panther" directoryQuotaInMB="128">  
            <!-- Absolute specifies an absolute path with optional environment expansion -->  
            <Absolute expandEnvironment="true" path="%SystemRoot%\system32\sysprep\Panther" />  
         </DirectoryConfiguration>  
         <DirectoryConfiguration container="wad-custom" directoryQuotaInMB="128">  
            <!-- LocalResource specifies a path relative to a local   
                 resource defined in the service definition -->  
            <LocalResource name="MyLoggingLocalResource" relativePath="logs" />  
         </DirectoryConfiguration>  
      </DataSources>  
   </Directories>  

   <PerformanceCounters bufferQuotaInMB="512" scheduledTransferPeriod="PT1M">  
      <!-- The counter specifier is in the same format as the imperative   
           diagnostics configuration API -->  
      <PerformanceCounterConfiguration   
         counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT5S" />  
   </PerformanceCounters>  

   <WindowsEventLog bufferQuotaInMB="512"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M">  
      <!-- The event log name is in the same format as the imperative   
           diagnostics configuration API -->  
      <DataSource name="System!*" />  
   </WindowsEventLog>  
</DiagnosticMonitorConfiguration>  
```  

## <a name="diagnosticsconfiguration-namespace"></a>Spazio dei nomi DiagnosticsConfiguration  
 Lo spazio dei nomi XML per il file di configurazione della diagnostica è il seguente:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="schema-elements"></a>Elementi dello schema  
 Il file di configurazione della diagnostica include gli elementi seguenti.


## <a name="diagnosticmonitorconfiguration-element"></a>Elemento DiagnosticMonitorConfiguration  
Elemento di livello superiore del file di configurazione della diagnostica.  

Attributi:

|Attributo  |type   |Obbligatoria| Predefinito | DESCRIZIONE|  
|-----------|-------|--------|---------|------------|  
|**configurationChangePollInterval**|duration|Facoltativo | PT1M| Specifica l'intervallo con cui il monitor di diagnostica esegue il polling per le modifiche della configurazione di diagnostica.|  
|**overallQuotaInMB**|unsignedInt|Facoltativo| 4000 MB. Se si specifica un valore, non deve superare la quantità |Spazio totale di archiviazione del file system allocato per tutti i buffer di registrazione.|  

## <a name="diagnosticinfrastructurelogs-element"></a>Elemento DiagnosticInfrastructureLogs  
Definisce la configurazione del buffer per i log generati dall'infrastruttura di diagnostica sottostante.

Elemento padre: [Elemento DiagnosticMonitorConfiguration](#DiagnosticMonitorConfiguration).  

Attributi:

|Attributo|type|DESCRIZIONE|  
|---------|----|-----------------|  
|**bufferQuotaInMB**|unsignedInt|facoltativo. Specifica lo spazio massimo di archiviazione del file system disponibile per i dati specificati.<br /><br /> Il valore predefinito è 0.|  
|**scheduledTransferLogLevelFilter**|stringa|facoltativo. Specifica il livello di gravità minimo per le voci di log trasferite. Il valore predefinito è **Non definito**. Altri valori possibili sono **Dettagli**, **Informazioni**, **Avviso**, **Errore** e **Critico**.|  
|**scheduledTransferPeriod**|duration|facoltativo. Specifica l'intervallo tra trasferimenti di dati pianificati, arrotondato per eccesso al minuto più vicino.<br /><br /> Il valore predefinito è PT0S.|  

## <a name="logs-element"></a>Elemento Logs  
 Definisce la configurazione del buffer per i log di base di Azure.

 Elemento padre: [Elemento DiagnosticMonitorConfiguration](#DiagnosticMonitorConfiguration).  

Attributi:  

|Attributo|type|DESCRIZIONE|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|facoltativo. Specifica lo spazio massimo di archiviazione del file system disponibile per i dati specificati.<br /><br /> Il valore predefinito è 0.|  
|**scheduledTransferLogLevelFilter**|stringa|facoltativo. Specifica il livello di gravità minimo per le voci di log trasferite. Il valore predefinito è **Non definito**. Altri valori possibili sono **Dettagli**, **Informazioni**, **Avviso**, **Errore** e **Critico**.|  
|**scheduledTransferPeriod**|duration|facoltativo. Specifica l'intervallo tra trasferimenti di dati pianificati, arrotondato per eccesso al minuto più vicino.<br /><br /> Il valore predefinito è PT0S.|  

## <a name="directories-element"></a>Elemento Directories  
Definisce la configurazione del buffer per i log basati su file che è possibile definire.

Elemento padre: [Elemento DiagnosticMonitorConfiguration](#DiagnosticMonitorConfiguration).  


Attributi:  

|Attributo|type|DESCRIZIONE|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|facoltativo. Specifica lo spazio massimo di archiviazione del file system disponibile per i dati specificati.<br /><br /> Il valore predefinito è 0.|  
|**scheduledTransferPeriod**|duration|facoltativo. Specifica l'intervallo tra trasferimenti di dati pianificati, arrotondato per eccesso al minuto più vicino.<br /><br /> Il valore predefinito è PT0S.|  

## <a name="crashdumps-element"></a>Elemento CrashDumps  
 Definisce la directory dei dump di arresto anomalo del sistema.

 Elemento padre: [Elemento Directories](#Directories).  

Attributi:  

|Attributo|type|DESCRIZIONE|  
|---------------|----------|-----------------|  
|**container**|stringa|Nome del contenitore in cui dovrà essere trasferito il contenuto della directory.|  
|**directoryQuotaInMB**|unsignedInt|facoltativo. Specifica le dimensioni massime della directory in MB.<br /><br /> Il valore predefinito è 0.|  

## <a name="failedrequestlogs-element"></a>Elemento FailedRequestLogs  
 Definisce la directory dei log di richieste non riuscite.

 Elemento padre: [elemento Directories](#Directories).  

Attributi:  

|Attributo|type|DESCRIZIONE|  
|---------------|----------|-----------------|  
|**container**|stringa|Nome del contenitore in cui dovrà essere trasferito il contenuto della directory.|  
|**directoryQuotaInMB**|unsignedInt|facoltativo. Specifica le dimensioni massime della directory in MB.<br /><br /> Il valore predefinito è 0.|  

##  <a name="iislogs-element"></a>Elemento IISLogs  
 Definisce la directory di log IIS.

 Elemento padre: [elemento Directories](#Directories).  

Attributi:  

|Attributo|type|DESCRIZIONE|  
|---------------|----------|-----------------|  
|**container**|stringa|Nome del contenitore in cui dovrà essere trasferito il contenuto della directory.|  
|**directoryQuotaInMB**|unsignedInt|facoltativo. Specifica le dimensioni massime della directory in MB.<br /><br /> Il valore predefinito è 0.|  

## <a name="datasources-element"></a>Elemento DataSources  
 Definisce zero o più directory di log aggiuntivi.

 Elemento padre: [Elemento Directories](#Directories).

## <a name="directoryconfiguration-element"></a>Elemento DirectoryConfiguration  
 Definisce la directory di file di log da monitorare.

 Elemento padre: [Elemento DataSources](#DataSources).

Attributi:

|Attributo|type|DESCRIZIONE|  
|---------------|----------|-----------------|  
|**container**|stringa|Nome del contenitore in cui dovrà essere trasferito il contenuto della directory.|  
|**directoryQuotaInMB**|unsignedInt|facoltativo. Specifica le dimensioni massime della directory in MB.<br /><br /> Il valore predefinito è 0.|  

## <a name="absolute-element"></a>Elemento Absolute  
 Definisce un percorso assoluto della directory da monitorare con espansione dell'ambiente facoltativa.

 Elemento padre: [Elemento DirectoryConfiguration](#DirectoryConfiguration).  

Attributi:  

|Attributo|type|DESCRIZIONE|  
|---------------|----------|-----------------|  
|**path**|stringa|Richiesto. Percorso assoluto della directory da monitorare.|  
|**expandEnvironment**|boolean|Richiesto. Se impostato su **true**, le variabili di ambiente nel percorso verranno espanse.|  

## <a name="localresource-element"></a>Elemento LocalResource  
 Definisce un percorso relativo a una risorsa locale nella definizione del servizio.

 Elemento padre: [Elemento DirectoryConfiguration](#DirectoryConfiguration).  

Attributi:  

|Attributo|type|DESCRIZIONE|  
|---------------|----------|-----------------|  
|**nome**|stringa|Richiesto. Nome della risorsa locale che contiene la directory da monitorare.|  
|**relativePath**|stringa|Richiesto. Percorso relativo della risorsa locale da monitorare.|  

## <a name="performancecounters-element"></a>Elemento PerformanceCounters  
 Definisce il percorso del contatore delle prestazioni da raccogliere.

 Elemento padre: [Elemento DiagnosticMonitorConfiguration](#DiagnosticMonitorConfiguration).


 Attributi:  

|Attributo|type|DESCRIZIONE|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|facoltativo. Specifica lo spazio massimo di archiviazione del file system disponibile per i dati specificati.<br /><br /> Il valore predefinito è 0.|  
|**scheduledTransferPeriod**|duration|facoltativo. Specifica l'intervallo tra trasferimenti di dati pianificati, arrotondato per eccesso al minuto più vicino.<br /><br /> Il valore predefinito è PT0S.|  

## <a name="performancecounterconfiguration-element"></a>Elemento PerformanceCounterConfiguration  
 Definisce il contatore delle prestazioni da raccogliere.

 Elemento padre: [Elemento PerformanceCounters](#PerformanceCounters).  

 Attributi:  

|Attributo|type|DESCRIZIONE|  
|---------------|----------|-----------------|  
|**counterSpecifier**|stringa|Richiesto. Percorso del contatore delle prestazioni da raccogliere.|  
|**sampleRate**|duration|Richiesto. Frequenza con la quale raccogliere il contatore delle prestazioni.|  

## <a name="windowseventlog-element"></a>Elemento WindowsEventLog  
 Definisce i registri eventi da monitorare.

 Elemento padre: [Elemento DiagnosticMonitorConfiguration](#DiagnosticMonitorConfiguration).

  Attributi:

|Attributo|type|DESCRIZIONE|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|facoltativo. Specifica lo spazio massimo di archiviazione del file system disponibile per i dati specificati.<br /><br /> Il valore predefinito è 0.|  
|**scheduledTransferLogLevelFilter**|stringa|facoltativo. Specifica il livello di gravità minimo per le voci di log trasferite. Il valore predefinito è **Non definito**. Altri valori possibili sono **Dettagli**, **Informazioni**, **Avviso**, **Errore** e **Critico**.|  
|**scheduledTransferPeriod**|duration|facoltativo. Specifica l'intervallo tra trasferimenti di dati pianificati, arrotondato per eccesso al minuto più vicino.<br /><br /> Il valore predefinito è PT0S.|  

## <a name="datasource-element"></a>Elemento DataSource  
 Definisce il registro eventi da monitorare.

 Elemento padre: [Elemento WindowsEventLog](#windowsEventLog).  

 Attributi:

|Attributo|type|DESCRIZIONE|  
|---------------|----------|-----------------|  
|**nome**|stringa|Richiesto. Espressione XPath che specifica il log da raccogliere.|  
