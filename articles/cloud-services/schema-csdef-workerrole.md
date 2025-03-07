---
title: Schema WorkerRole di definizione di Servizi cloud di Azure | Microsoft Docs
services: cloud-services
ms.custom: ''
ms.date: 04/14/2015
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 41cd46bc-c479-43fa-96e5-d6c83e4e6d89
caps.latest.revision: 55
author: georgewallace
ms.author: gwallace
manager: gwallace
ms.openlocfilehash: ac1cce61a9b9b22c079066147d6a318f778999db
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945866"
---
# <a name="azure-cloud-services-definition-workerrole-schema"></a>Schema WorkerRole di definizione di Servizi cloud di Azure
Il ruolo di lavoro di Azure è un ruolo utile per lo sviluppo generalizzato e può eseguire l'elaborazione in background per un ruolo Web.

L'estensione predefinita per il file di definizione del servizio è csdef.

## <a name="basic-service-definition-schema-for-a-worker-role"></a>Schema di definizione del servizio di base per un ruolo di lavoro
Il formato di base del file di definizione del servizio contenente un ruolo di lavoro è il seguente.

```xml
<ServiceDefinition …>
  <WorkerRole name="<worker-role-name>" vmsize="<worker-role-size>" enableNativeCodeExecution="[true|false]">
    <Certificates>
      <Certificate name="<certificate-name>" storeLocation="[CurrentUser|LocalMachine]" storeName="[My|Root|CA|Trust|Disallow|TrustedPeople|TrustedPublisher|AuthRoot|AddressBook|<custom-store>" />
    </Certificates>
    <ConfigurationSettings>
      <Setting name="<setting-name>" />
    </ConfigurationSettings>
    <Endpoints>
      <InputEndpoint name="<input-endpoint-name>" protocol="[http|https|tcp|udp]" localPort="<local-port-number>" port="<port-number>" certificate="<certificate-name>" loadBalancerProbe="<load-balancer-probe-name>" />
      <InternalEndpoint name="<internal-endpoint-name" protocol="[http|tcp|udp|any]" port="<port-number>">
         <FixedPort port="<port-number>"/>
         <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>
      </InternalEndpoint>
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">
         <AllocatePublicPortFrom>
            <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>
         </AllocatePublicPortFrom>
      </InstanceInputEndpoint>
    </Endpoints>
    <Imports>
      <Import moduleName="[RemoteAccess|RemoteForwarder|Diagnostics]"/>
    </Imports>
    <LocalResources>
      <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />
    </LocalResources>
    <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />
    <Runtime executionContext="[limited|elevated]">
      <Environment>
         <Variable name="<variable-name>" value="<variable-value>">
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>
          </Variable>
      </Environment>
      <EntryPoint>
         <NetFxEntryPoint assemblyName="<name-of-assembly-containing-entrypoint>" targetFrameworkVersion="<.net-framework-version>"/>
         <ProgramEntryPoint commandLine="<application>" setReadyOnProcessStart="[true|false]" "/>
      </EntryPoint>
    </Runtime>
    <Startup priority="<for-internal-use-only>”>
      <Task commandLine="" executionContext="[limited|elevated]" taskType="[simple|foreground|background]">
        <Environment>
         <Variable name="<variable-name>" value="<variable-value>">
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>
          </Variable>
        </Environment>
      </Task>
    </Startup>
    <Contents>
      <Content destination="<destination-folder-name>" >
        <SourceDirectory path="<local-source-directory>" />
      </Content>
    </Contents>
  </WorkerRole>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Elementi dello schema
Il file di definizione del servizio include questi elementi, descritti in dettaglio nelle sezioni successive di questo argomento:

[WorkerRole](#WorkerRole)

[ConfigurationSettings](#ConfigurationSettings)

[Impostazione](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[Endpoints](#Endpoints)

[InputEndpoint](#InputEndpoint)

[InternalEndpoint](#InternalEndpoint)

[InstanceInputEndpoint](#InstanceInputEndpoint)

[AllocatePublicPortFrom](#AllocatePublicPortFrom)

[FixedPort](#FixedPort)

[FixedPortRange](#FixedPortRange)

[Certificati](#Certificates)

[Certificate](#Certificate)

[Imports](#Imports)

[Import (Importa) (Import (Importa)a)](#Import)

[Runtime](#Runtime)

[Environment](#Environment)

[EntryPoint](#EntryPoint)

[NetFxEntryPoint](#NetFxEntryPoint)

[ProgramEntryPoint](#ProgramEntryPoint)

[Variabile](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[Startup](#Startup)

[Attività](#Task)

[Contents](#Contents)

[Contenuto](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="WorkerRole"></a> WorkerRole
L'elemento `WorkerRole` descrive un ruolo utile per lo sviluppo generalizzato e può eseguire l'elaborazione in background per un ruolo Web. Un servizio può contenere zero o più ruoli di lavoro.

La tabella seguente descrive gli attributi dell'elemento `WorkerRole`.

| Attributo | Type | DESCRIZIONE |
| --------- | ---- | ----------- |
|name|stringa|Richiesto. Nome del ruolo di lavoro. Il nome del ruolo deve essere univoco.|
|enableNativeCodeExecution|boolean|facoltativo. Il valore predefinito è `true`. L'esecuzione del codice nativo e l'attendibilità totale sono abilitate per impostazione predefinita. Impostare questo attributo su `false` per disabilitare l'esecuzione del codice nativo per il ruolo di lavoro e usare invece l'attendibilità parziale di Azure.|
|vmsize|string|facoltativo. Impostare questo valore per modificare le dimensioni della macchina virtuale assegnata a questo ruolo. Il valore predefinito è `Small`. Per un elenco delle possibili dimensioni delle macchine virtuali e dei relativi attributi, vedere [Dimensioni delle macchine virtuali per i servizi cloud](cloud-services-sizes-specs.md).|

##  <a name="ConfigurationSettings"></a> ConfigurationSettings
L'elemento `ConfigurationSettings` descrive la raccolta delle impostazioni di configurazione per un ruolo di lavoro. Questo è l'elemento padre dell'elemento `Setting`.

##  <a name="Setting"></a> Setting
L'elemento `Setting` descrive una coppia di nome e valore che specifica un'impostazione di configurazione per un'istanza di un ruolo.

La tabella seguente descrive gli attributi dell'elemento `Setting`.

| Attributo | Type | DESCRIZIONE |
| --------- | ---- | ----------- |
|name|stringa|Richiesto. Nome univoco dell'impostazione di configurazione.|

Le impostazioni di configurazione per un ruolo sono coppie di nome e valore dichiarate nel file di definizione del servizio e impostate nel file di configurazione del servizio.

##  <a name="LocalResources"></a> LocalResources
L'elemento `LocalResources` descrive la raccolta delle risorse di archiviazione locali per un ruolo di lavoro. Questo è l'elemento padre dell'elemento `LocalStorage`.

##  <a name="LocalStorage"></a> LocalStorage
L'elemento `LocalStorage` identifica una risorsa di archiviazione locale che fornisce lo spazio del file system per il servizio in fase di esecuzione. Un ruolo può definire zero o più risorse di archiviazione locali.

> [!NOTE]
>  L'elemento `LocalStorage` può essere visualizzato come figlio dell'elemento `WorkerRole` per supportare la compatibilità con le versioni precedenti di Azure SDK.

La tabella seguente descrive gli attributi dell'elemento `LocalStorage`.

| Attributo | Type | DESCRIZIONE |
| --------- | ---- | ----------- |
|name|stringa|Richiesto. Nome univoco per l'archivio locale.|
|cleanOnRoleRecycle|boolean|facoltativo. Indica se l'archivio locale deve essere pulito quando il ruolo viene riavviato. Il valore predefinito è `true`.|
|sizeInMb|int|facoltativo. Quantità desiderata di spazio di archiviazione da allocare per l'archivio locale, in MB. Se non specificato, lo spazio di archiviazione predefinito allocato è 100 MB. La quantità minima di spazio di archiviazione che può essere allocato è 1 MB.<br /><br /> Le dimensioni massime delle risorse locali dipendono dalle dimensioni della macchina virtuale. Per altre informazioni, vedere [Dimensioni delle macchine virtuali per i servizi cloud](cloud-services-sizes-specs.md).|

Il nome della directory allocata alla risorsa di archiviazione locale corrisponde al valore specificato per l'attributo name.

##  <a name="Endpoints"></a> Endpoints
L'elemento `Endpoints` descrive la raccolta degli endpoint di input (esterni), interni e di input dell'istanza per un ruolo. Questo è l'elemento padre degli elementi `InputEndpoint`, `InternalEndpoint` e `InstanceInputEndpoint`.

Gli endpoint di input e interni vengono allocati separatamente. Un servizio può avere un totale di 25 endpoint di input, interni e di input dell'istanza che possono essere allocati nei 25 ruoli consentiti in un servizio. Con 5 ruoli, ad esempio, è possibile allocare 5 endpoint di input per ruolo, 25 endpoint di input a un singolo ruolo oppure 1 endpoint di input a ognuno dei 25 ruoli.

> [!NOTE]
>  Ogni ruolo distribuito richiede un'istanza per ruolo. Il provisioning predefinito per una sottoscrizione è limitato a 20 core e quindi a 20 istanze di un ruolo. Se l'applicazione richiede più istanze di quelle fornite dal provisioning predefinito, vedere [Supporto per fatturazione, gestione della sottoscrizione e quota](https://azure.microsoft.com/support/options/) per altre informazioni sull'aumento della quota.

##  <a name="InputEndpoint"></a> InputEndpoint
L'elemento `InputEndpoint` descrive un endpoint esterno per un ruolo di lavoro.

È possibile definire più endpoint come combinazione di endpoint HTTP, HTTPS, UDP e TCP. È possibile specificare qualsiasi numero di porta scelto per un endpoint di input, ma i numeri di porta specificati per ogni ruolo nel servizio devono essere univoci. Se ad esempio si specifica che un ruolo deve usare la porta 80 per HTTP e la porta 443 per HTTPS, è possibile specificare che un secondo ruolo deve usare la porta 8080 per HTTP e la porta 8043 per HTTPS.

La tabella seguente descrive gli attributi dell'elemento `InputEndpoint`.

| Attributo | Type | DESCRIZIONE |
| --------- | ---- | ----------- |
|name|stringa|Richiesto. Nome univoco per l'endpoint esterno.|
|protocollo|string|Richiesto. Protocollo di trasporto per l'endpoint esterno. Per un ruolo di lavoro, i possibili valori sono `HTTP`, `HTTPS`, `UDP` o `TCP`.|
|port|int|Richiesto. Porta per l'endpoint esterno. È possibile specificare qualsiasi numero di porta scelto, ma i numeri di porta specificati per ogni ruolo nel servizio devono essere univoci.<br /><br /> I possibili valori sono compresi tra 1 e 65535 inclusi (Azure SDK versione 1.7 o successiva).|
|certificato|string|Obbligatorio per un endpoint HTTPS. Nome di un certificato definito da un elemento `Certificate`.|
|localPort|int|facoltativo. Specifica una porta usata per le connessioni interne nell'endpoint. L'attributo `localPort` esegue il mapping della porta esterna nell'endpoint a una porta interna in un ruolo. È utile negli scenari in cui un ruolo deve comunicare con un componente interno su una porta diversa da quella esposta esternamente.<br /><br /> Se non specificato, il valore di `localPort` è lo stesso dell'attributo `port`. Impostare il valore di `localPort` su "*" per assegnare automaticamente una porta non allocata individuabile usando l'API di runtime.<br /><br /> I possibili valori sono compresi tra 1 e 65535 inclusi (Azure SDK versione 1.7 o successiva).<br /><br /> L'attributo `localPort` è disponibile solo se si usa Azure SDK versione 1.3 o successiva.|
|ignoreRoleInstanceStatus|boolean|facoltativo. Quando il valore di questo attributo è impostato su `true`, lo stato di un servizio viene ignorato e l'endpoint non verrà rimosso dal servizio di bilanciamento del carico. Impostare questo valore su `true` è utile per il eseguire il debug delle istanze occupate di un servizio. Il valore predefinito è `false`. **Nota:** Un endpoint può continuare a ricevere traffico anche quando il ruolo non è pronto.|
|loadBalancerProbe|string|facoltativo. Nome del probe di bilanciamento del carico associato all'endpoint di input. Per altre informazioni, vedere [Schema LoadBalancerProbe](schema-csdef-loadbalancerprobe.md).|

##  <a name="InternalEndpoint"></a> InternalEndpoint
L'elemento `InternalEndpoint` descrive un endpoint interno per un ruolo di lavoro. Un endpoint interno è disponibile solo per le altre istanze del ruolo in esecuzione nel servizio. Non è disponibile per i client esterni al servizio. Un ruolo di lavoro può avere fino a cinque endpoint interni HTTP, TCP o UDP.

La tabella seguente descrive gli attributi dell'elemento `InternalEndpoint`.

| Attributo | Type | DESCRIZIONE |
| --------- | ---- | ----------- |
|name|stringa|Richiesto. Nome univoco per l'endpoint interno.|
|protocollo|string|Richiesto. Protocollo di trasporto per l'endpoint interno. I possibili valori sono `HTTP`, `TCP`, `UDP` o `ANY`.<br /><br /> Il valore `ANY` specifica che sono consentiti tutti i protocolli e tutte le porte.|
|port|int|facoltativo. Porta usata per le connessioni interne con bilanciamento del carico nell'endpoint. Un endpoint con bilanciamento del carico usa due porte. la porta usata per l'indirizzo IP pubblico e la porta usata per l'indirizzo IP privato, che in genere sono impostate sullo stesso valore, ma è possibile scegliere di usare porte diverse.<br /><br /> I possibili valori sono compresi tra 1 e 65535 inclusi (Azure SDK versione 1.7 o successiva).<br /><br /> L'attributo `Port` è disponibile solo se si usa Azure SDK versione 1.3 o successiva.|

##  <a name="InstanceInputEndpoint"></a> InstanceInputEndpoint
L'elemento `InstanceInputEndpoint` descrive un endpoint di input dell'istanza per un ruolo di lavoro. Un endpoint di input dell'istanza viene associato a un'istanza del ruolo specifica usando il port forwarding nel servizio di bilanciamento del carico. Per ogni endpoint di input dell'istanza viene eseguito il mapping a una porta specifica compresa in un intervallo di porte possibili. Questo è l'elemento padre dell'elemento `AllocatePublicPortFrom`.

L'elemento `InstanceInputEndpoint` è disponibile solo se si usa Azure SDK versione 1.7 o successiva.

La tabella seguente descrive gli attributi dell'elemento `InstanceInputEndpoint`.

| Attributo | Type | DESCRIZIONE |
| --------- | ---- | ----------- |
|name|stringa|Richiesto. Nome univoco per l'endpoint.|
|localPort|int|Richiesto. Specifica la porta interna su cui tutte le istanze del ruolo saranno in ascolto per ricevere il traffico in ingresso inoltrato dal servizio di bilanciamento del carico. I possibili valori sono compresi tra 1 e 65535 inclusi.|
|protocollo|string|Richiesto. Protocollo di trasporto per l'endpoint interno. I possibili valori sono `udp` o `tcp`. Usare `tcp` per il traffico basato su http/https.|

##  <a name="AllocatePublicPortFrom"></a> AllocatePublicPortFrom
L'elemento `AllocatePublicPortFrom` descrive l'intervallo di porte pubbliche che può essere usato dai clienti esterni per accedere a ogni endpoint di input dell'istanza. Il numero di porta pubblica (indirizzo VIP) viene allocato da questo intervallo e assegnato a ogni singolo endpoint di istanza del ruolo durante la distribuzione e l'aggiornamento del tenant. Questo è l'elemento padre dell'elemento `FixedPortRange`.

L'elemento `AllocatePublicPortFrom` è disponibile solo se si usa Azure SDK versione 1.7 o successiva.

##  <a name="FixedPort"></a> FixedPort
L'elemento `FixedPort` specifica la porta per l'endpoint interno, che abilita le connessioni con bilanciamento del carico sull'endpoint.

L'elemento `FixedPort` è disponibile solo se si usa Azure SDK versione 1.3 o successiva.

La tabella seguente descrive gli attributi dell'elemento `FixedPort`.

| Attributo | Type | Descrizione |
| --------- | ---- | ----------- |
|port|int|Richiesto. Porta per l'endpoint interno. Se si impostano i valori minimo e massimo di `FixedPortRange` sulla stessa porta, si ottiene lo stesso risultato.<br /><br /> I possibili valori sono compresi tra 1 e 65535 inclusi (Azure SDK versione 1.7 o successiva).|

##  <a name="FixedPortRange"></a> FixedPortRange
L'elemento `FixedPortRange` specifica l'intervallo di porte assegnate all'endpoint interno o all'endpoint di input dell'istanza e imposta la porta usata per le connessioni con bilanciamento del carico sull'endpoint.

> [!NOTE]
>  Il funzionamento dell'elemento `FixedPortRange` è diverso a seconda dell'elemento in cui si trova. Quando l'elemento `FixedPortRange` è nell'elemento `InternalEndpoint`, apre tutte le porte nel servizio di bilanciamento del carico comprese nell'intervallo degli attributi min e max per tutte le macchine virtuali in cui il ruolo viene eseguito. Quando l'elemento `FixedPortRange` è nell'elemento `InstanceInputEndpoint`, apre una sola porta compresa nell'intervallo degli attributi min e max in ogni macchina virtuale che esegue il ruolo.

L'elemento `FixedPortRange` è disponibile solo se si usa Azure SDK versione 1.3 o successiva.

La tabella seguente descrive gli attributi dell'elemento `FixedPortRange`.

| Attributo | Type | DESCRIZIONE |
| --------- | ---- | ----------- |
|min|int|Richiesto. Numero di porta minimo nell'intervallo. I possibili valori sono compresi tra 1 e 65535 inclusi (Azure SDK versione 1.7 o successiva).|
|max|string|Richiesto. Numero di porta massimo nell'intervallo. I possibili valori sono compresi tra 1 e 65535 inclusi (Azure SDK versione 1.7 o successiva).|

##  <a name="Certificates"></a> Certificates
L'elemento `Certificates` descrive la raccolta dei certificati per un ruolo di lavoro. Questo è l'elemento padre dell'elemento `Certificate`. A un ruolo può essere associato un numero qualsiasi di certificati. Per altre informazioni sull'uso dell'elemento certificates, vedere [Modificare il file di definizione del servizio con un certificato](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="Certificate"></a> Certificate
L'elemento `Certificate` descrive un certificato associato a un ruolo di lavoro.

La tabella seguente descrive gli attributi dell'elemento `Certificate`.

| Attributo | Type | DESCRIZIONE |
| --------- | ---- | ----------- |
|name|stringa|Richiesto. Nome di questo certificato, usato per farvi riferimento quando viene associato a un elemento `InputEndpoint` HTTPS.|
|storeLocation|string|Richiesto. Posizione dell'archivio certificati in cui si può trovare questo certificato sul computer locale. I valori possibili sono `CurrentUser` e `LocalMachine`.|
|storeName|string|Richiesto. Nome dell'archivio certificati in cui si trova questo certificato sul computer locale. I possibili valori includono i nomi di archivio predefiniti `My`, `Root`, `CA`, `Trust`, `Disallowed`, `TrustedPeople`, `TrustedPublisher`, `AuthRoot`, `AddressBook` o i nomi di archivio personalizzati. Se viene specificato un nome di archivio personalizzato, l'archivio viene creato automaticamente.|
|permissionLevel|string|facoltativo. Specifica le autorizzazioni di accesso concesse ai processi di ruolo. Per consentire solo ai processi con privilegi elevati di accedere alla chiave privata, specificare l'autorizzazione `elevated`. L'autorizzazione `limitedOrElevated` consente a tutti i processi di ruolo di accedere alla chiave privata. I possibili valori sono `limitedOrElevated` o `elevated`. Il valore predefinito è `limitedOrElevated`.|

##  <a name="Imports"></a> Imports
L'elemento `Imports` descrive una raccolta dei moduli di importazione per un ruolo di lavoro, che aggiungono componenti al sistema operativo guest. Questo è l'elemento padre dell'elemento `Import`. Questo elemento è facoltativo e un ruolo può avere un solo blocco di runtime.

L'elemento `Imports` è disponibile solo se si usa Azure SDK versione 1.3 o successiva.

##  <a name="Import"></a> Import
L'elemento `Import` specifica un modulo da aggiungere al sistema operativo guest.

L'elemento `Import` è disponibile solo se si usa Azure SDK versione 1.3 o successiva.

La tabella seguente descrive gli attributi dell'elemento `Import`.

| Attributo | Type | Descrizione |
| --------- | ---- | ----------- |
|moduleName|string|Richiesto. Nome del modulo da importare. I moduli di importazione validi sono:<br /><br /> -   RemoteAccess<br />-   RemoteForwarder<br />-   Diagnostics<br /><br /> I moduli RemoteAccess e RemoteForwarder consentono di configurare l'istanza del ruolo per le connessioni desktop remote. Per altre informazioni, vedere [Abilitare una connessione Desktop remoto](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> Il modulo Diagnostics consente di raccogliere dati di diagnostica per un'istanza del ruolo|

##  <a name="Runtime"></a> Runtime
L'elemento `Runtime` descrive una raccolta di impostazioni di variabile di ambiente per un ruolo di lavoro, che controllano l'ambiente di runtime del processo host di Azure. Questo è l'elemento padre dell'elemento `Environment`. Questo elemento è facoltativo e un ruolo può avere un solo blocco di runtime.

L'elemento `Runtime` è disponibile solo se si usa Azure SDK versione 1.3 o successiva.

La tabella seguente descrive gli attributi dell'elemento `Runtime`:

| Attributo | Type | Descrizione |
| --------- | ---- | ----------- |
|executionContext|string|facoltativo. Specifica il contesto in cui viene avviato il processo del ruolo. Il contesto predefinito è `limited`.<br /><br /> -   `limited`: il processo viene avviato senza privilegi di amministratore.<br />-   `elevated`: il processo viene avviato con privilegi di amministratore.|

##  <a name="Environment"></a> Environment
L'elemento `Environment` descrive una raccolta delle impostazioni di variabile di ambiente per un ruolo di lavoro. Questo è l'elemento padre dell'elemento `Variable`. Un ruolo può avere un numero qualsiasi di variabili di ambiente impostate.

##  <a name="Variable"></a> Variable
L'elemento `Variable` specifica una variabile di ambiente da impostare nel sistema operativo guest.

L'elemento `Variable` è disponibile solo se si usa Azure SDK versione 1.3 o successiva.

La tabella seguente descrive gli attributi dell'elemento `Variable`:

| Attributo | Type | DESCRIZIONE |
| --------- | ---- | ----------- |
|name|stringa|Richiesto. Nome della variabile di ambiente da impostare.|
|value|string|facoltativo. Valore da impostare per la variabile di ambiente. È necessario includere un attributo value o un elemento `RoleInstanceValue`.|

##  <a name="RoleInstanceValue"></a> RoleInstanceValue
L'elemento `RoleInstanceValue` specifica un XPath da cui recuperare il valore della variabile.

La tabella seguente descrive gli attributi dell'elemento `RoleInstanceValue`.

| Attributo | Type | Descrizione |
| --------- | ---- | ----------- |
|xpath|string|facoltativo. Percorso delle impostazioni di distribuzione per l'istanza. Per altre informazioni, vedere [Variabili di configurazione con XPath](cloud-services-role-config-xpath.md).<br /><br /> È necessario includere un attributo value o un elemento `RoleInstanceValue`.|

##  <a name="EntryPoint"></a> EntryPoint
L'elemento `EntryPoint` specifica il punto di ingresso per un ruolo. Questo è l'elemento padre degli elementi `NetFxEntryPoint`. Questi elementi consentono di specificare un'applicazione diversa da quella predefinita WaWorkerHost.exe, che deve fungere da punto di ingresso di un ruolo.

L'elemento `EntryPoint` è disponibile solo se si usa Azure SDK versione 1.5 o successiva.

##  <a name="NetFxEntryPoint"></a> NetFxEntryPoint
L'elemento `NetFxEntryPoint` specifica il programma da eseguire per un ruolo.

> [!NOTE]
>  L'elemento `NetFxEntryPoint` è disponibile solo se si usa Azure SDK versione 1.5 o successiva.

La tabella seguente descrive gli attributi dell'elemento `NetFxEntryPoint`.

| Attributo | Type | Descrizione |
| --------- | ---- | ----------- |
|assemblyName|string|Richiesto. Percorso e nome file dell'assembly contenente il punto di ingresso. Il percorso è relativo alla cartella **\\%ROLEROOT%\Approot**. Non specificare **\\%ROLEROOT%\Approot** in `commandLine` perché è dato per scontato. **%ROLEROOT%** è una variabile di ambiente gestita da Azure e rappresenta la posizione della cartella radice per il ruolo. La cartella **\\%ROLEROOT%\Approot** rappresenta la cartella dell'applicazione per il ruolo.|
|targetFrameworkVersion|string|Richiesto. Versione di .NET Framework in cui è stato compilato l'assembly, Ad esempio `targetFrameworkVersion="v4.0"`.|

##  <a name="ProgramEntryPoint"></a> ProgramEntryPoint
L'elemento `ProgramEntryPoint` specifica il programma da eseguire per un ruolo. L'elemento `ProgramEntryPoint` consente di specificare un punto di ingresso del programma non basato su un assembly .NET.

> [!NOTE]
>  L'elemento `ProgramEntryPoint` è disponibile solo se si usa Azure SDK versione 1.5 o successiva.

La tabella seguente descrive gli attributi dell'elemento `ProgramEntryPoint`.

| Attributo | Type | Descrizione |
| --------- | ---- | ----------- |
|commandLine|string|Richiesto. Percorso, nome file e argomenti della riga di comando del programma da eseguire. Il percorso è relativo alla cartella **%ROLEROOT%\Approot**. Non specificare **%ROLEROOT%\Approot** in commandLine perché è dato per scontato. **%ROLEROOT%** è una variabile di ambiente gestita da Azure e rappresenta la posizione della cartella radice per il ruolo. La cartella **%ROLEROOT%\Approot** rappresenta la cartella dell'applicazione per il ruolo.<br /><br /> Se il programma termina, il ruolo viene riciclato, quindi è consigliabile impostare il programma per continuare l'esecuzione, invece che per l'avvio e l'esecuzione di un'attività limitata.|
|setReadyOnProcessStart|boolean|Richiesto. Specifica se l'istanza del ruolo attende che il programma della riga di comando segnali che è stato avviato. Questo valore deve essere impostato su `true` al momento. L'impostazione del valore su `false` è riservata per un utilizzo futuro.|

##  <a name="Startup"></a> Startup
L'elemento `Startup` descrive una raccolta di attività eseguite quando il ruolo viene avviato. Questo può essere l'elemento padre dell'elemento `Variable`. Per altre informazioni sull'uso delle attività di avvio del ruolo, vedere [Come configurare attività di avvio](cloud-services-startup-tasks.md). Questo elemento è facoltativo e un ruolo può avere un solo blocco di avvio.

La tabella seguente descrive l'attributo dell'elemento `Startup`.

| Attributo | Type | Descrizione |
| --------- | ---- | ----------- |
|priorità|int|Solo per uso interno.|

##  <a name="Task"></a> Task
L'elemento `Task` specifica l'attività di avvio eseguita quando il ruolo viene avviato. Le attività di avvio possono essere usate per eseguire attività che preparano il ruolo a eseguire tali componenti software di installazione o altre applicazioni. Le attività vengono eseguite nell'ordine in cui sono visualizzate nel blocco dell'elemento `Startup`.

L'elemento `Task` è disponibile solo se si usa Azure SDK versione 1.3 o successiva.

La tabella seguente descrive gli attributi dell'elemento `Task`.

| Attributo | Type | Descrizione |
| --------- | ---- | ----------- |
|commandLine|string|Richiesto. Uno script, ad esempio un file CMD, contenente i comandi da eseguire. Il comando di avvio e i file batch devono essere salvati in formato ANSI. I formati di file che impostano un byte order mark all'inizio del file non verranno elaborati correttamente.|
|executionContext|string|Specifica il contesto in cui viene eseguito lo script.<br /><br /> -   `limited` [impostazione predefinita]: viene eseguito con gli stessi privilegi del ruolo che ospita il processo.<br />-   `elevated`: viene eseguito con privilegi di amministratore.|
|taskType|string|Specifica il comportamento di esecuzione del comando.<br /><br /> -   `simple` [impostazione predefinita]: il sistema attende la fine dell'attività prima che vengano avviate altre attività.<br />-   `background`: il sistema non attende la fine dell'attività.<br />-   `foreground`: simile a background, ma il ruolo viene riavviato solo dopo la fine di tutte le attività in primo piano.|

##  <a name="Contents"></a> Contents
L'elemento `Contents` descrive la raccolta dei contenuti per un ruolo di lavoro. Questo è l'elemento padre dell'elemento `Content`.

L'elemento `Contents` è disponibile solo se si usa Azure SDK versione 1.5 o successiva.

##  <a name="Content"></a> Content
L'elemento `Content` definisce la posizione di origine del contenuto da copiare nella macchina virtuale di Azure e il percorso di destinazione in cui viene copiato.

L'elemento `Content` è disponibile solo se si usa Azure SDK versione 1.5 o successiva.

La tabella seguente descrive gli attributi dell'elemento `Content`.

| Attributo | Type | Descrizione |
| --------- | ---- | ----------- |
|destinazione|string|Richiesto. Posizione della macchina virtuale Azure in cui viene inserito il contenuto. Questa posizione è relativa alla cartella **%ROLEROOT%\Approot**.|

Questo è l'elemento padre dell'elemento `SourceDirectory`.

##  <a name="SourceDirectory"></a> SourceDirectory
L'elemento `SourceDirectory` definisce la directory locale da cui il contenuto viene copiato. Usare questo elemento per specificare i contenuti locali da copiare nella macchina virtuale di Azure.

L'elemento `SourceDirectory` è disponibile solo se si usa Azure SDK versione 1.5 o successiva.

La tabella seguente descrive gli attributi dell'elemento `SourceDirectory`.

| Attributo | Type | DESCRIZIONE |
| --------- | ---- | ----------- |
|path|stringa|Richiesto. Percorso relativo o assoluto di una directory locale i cui contenuti verranno copiati nella macchina virtuale di Azure. L'espansione delle variabili di ambiente nel percorso della directory è supportata.|

## <a name="see-also"></a>Vedere anche
[Cloud Service (classic) Definition Schema](schema-csdef-file.md) (Schema di definizione di Servizi cloud - Versione classica)