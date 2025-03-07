---
title: Installare pacchetti dell'applicazione nei nodi di calcolo - Azure Batch | Microsoft Docs
description: Usare la funzionalità dei pacchetti dell’applicazione di Azure Batch per gestire facilmente più applicazioni e versioni ed eseguire l'installazione su nodi di calcolo in Batch.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 3b6044b7-5f65-4a27-9d43-71e1863d16cf
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/26/2019
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9c9d6d13efaa07bff2a1eaabe05725a3257cf895
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70095683"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Distribuire le applicazioni nei nodi di calcolo con i pacchetti dell'applicazione Batch

I pacchetti dell'applicazione sono una funzionalità di Azure Batch che consente di gestire e distribuire facilmente le applicazioni per le attività nei nodi di calcolo del pool. I pacchetti dell'applicazione consentono di caricare e gestire più versioni delle applicazioni eseguite dalle attività, inclusi i file di supporto. È quindi possibile di distribuire automaticamente una o più applicazioni nei nodi di calcolo del pool.

In questo articolo viene illustrato come caricare e gestire pacchetti dell'applicazione nel portale di Azure. Si apprenderà quindi come installarli nei nodi di calcolo di un pool con la libreria [batch .NET][api_net] .

> [!NOTE]
> I pacchetti dell'applicazione sono supportati in tutti i pool di Batch creati dopo il 5 luglio 2017. Sono supportati nei pool di Batch creati tra il 10 marzo 2016 e il 5 luglio 2017 solo se il pool è stato creato usando una configurazione del servizio cloud. I pool di batch creati prima del 10 marzo 2016 non supportano i pacchetti dell'applicazione.
>
> Le API per la creazione e la gestione dei pacchetti dell'applicazione fanno parte della libreria .NET per la [gestione di batch][api_net_mgmt] . Le API per l'installazione dei pacchetti dell'applicazione in un nodo di calcolo fanno parte della libreria [batch .NET][api_net] . Funzionalità simili si trovano nelle API Batch disponibili per altri linguaggi. 
>
> La funzionalità dei pacchetti dell’applicazione descritta di seguito sostituisce la funzionalità App Batch disponibile nelle versioni precedenti del servizio.

## <a name="application-package-requirements"></a>Requisiti dei pacchetti dell'applicazione
Per usare i pacchetti dell'applicazione, è necessario [collegare un account di archiviazione di Azure](#link-a-storage-account) all'account Batch.

## <a name="about-applications-and-application-packages"></a>Informazioni sulle applicazioni e sui pacchetti dell’applicazione
Per *applicazione* in Azure Batch si intende un set di file binari con versione che possono essere scaricati automaticamente nei nodi di calcolo del pool. Un *pacchetto dell'applicazione* è invece un *set specifico* di tali file binari e rappresenta una *versione* specifica dell'applicazione.

![Diagramma di alto livello di applicazioni e pacchetti applicazione][1]

### <a name="applications"></a>Applicazioni
Un'applicazione in Batch contiene uno o più pacchetti dell'applicazione e specifica le opzioni di configurazione per l'applicazione. Un'applicazione può ad esempio specificare la versione predefinita del pacchetto dell'applicazione da installare nei nodi di calcolo e se i pacchetti possono essere aggiornati o eliminati.

### <a name="application-packages"></a>Pacchetti dell'applicazione
Un pacchetto dell'applicazione è un file zip contenente i file binari e i file di supporto dell'applicazione necessari per le attività di esecuzione dell'applicazione. Ogni pacchetto dell’applicazione rappresenta una versione specifica dell'applicazione.

È possibile specificare i pacchetti dell'applicazione a livello di pool e di attività. È possibile specificare uno o più di questi pacchetti ed eventualmente una versione quando si crea un pool o un'attività.

* **pacchetti dell'applicazione del pool** vengono distribuiti in *ogni* nodo del pool. Le applicazioni vengono distribuite quando un nodo viene aggiunto a un pool e quando viene riavviato o oppure la sua immagine viene ricreata.
  
    I pacchetti dell'applicazione del pool possono essere usati quando tutti i nodi in un pool eseguono le attività di un processo. È possibile specificare uno o più pacchetti dell'applicazione quando si crea un pool e aggiungere o aggiornare i pacchetti di un pool esistente. Se si aggiornano i pacchetti dell'applicazione di un pool esistente, è necessario riavviare i nodi per installare il nuovo pacchetto.
* **pacchetti dell'applicazione per le attività** vengono distribuiti solo in un nodo di calcolo che dovrà eseguire un'attività, appena prima di eseguire la riga di comando dell'attività. Se il pacchetto dell'applicazione specificato con la versione corrispondente si trova già nel nodo, non verrà ridistribuito e verrà usato il pacchetto esistente.
  
    I pacchetti dell'applicazione per le attività sono utili in ambienti di pool condivisi, in cui diversi processi vengono eseguiti in un pool e il pool non viene eliminato quando viene completato un processo. Se il processo ha meno attività che nodi nel pool, i pacchetti dell'applicazione di attività possono ridurre il trasferimento dei dati, perché l'applicazione viene distribuita solo nei nodi che eseguono le attività.
  
    Altri scenari che possono trarre vantaggio dai pacchetti dell'applicazione per le attività sono i processi che usano un'applicazione di dimensioni elevate, ma solo per poche attività. Una fase di pre-elaborazione o un'attività di unione in cui l'applicazione di pre-elaborazione o di unione è pesante possono ad esempio beneficiare dell'uso di pacchetti dell'applicazione per le attività.

> [!IMPORTANT]
> Sono previste restrizioni al numero di applicazioni e di pacchetti dell'applicazione in un account Batch e alle dimensioni massime del pacchetto dell'applicazione. Per informazioni dettagliate su questi limiti, vedere [Quote e limiti per il servizio Azure Batch](batch-quota-limit.md).
> 
> 

### <a name="benefits-of-application-packages"></a>Vantaggi dei pacchetti dell'applicazione
Con i pacchetti dell'applicazione è possibile semplificare il codice nella soluzione Batch e ridurre il sovraccarico richiesto in termini di gestione delle applicazioni eseguite delle attività.

Con i pacchetti dell'applicazione non è necessario che l'attività di avvio del pool specifichi un lungo elenco di singoli file di risorse da installare nei nodi. Non è necessario gestire manualmente più versioni dei file dell'applicazione in Archiviazione di Azure o nei nodi. Per accedere ai file nell'account di archiviazione non è necessario generare un [URL di firma di accesso condiviso](../storage/common/storage-dotnet-shared-access-signature-part-1.md) . Batch interagisce in background con Archiviazione di Azure per archiviare e distribuire i pacchetti dell'applicazione nei nodi di calcolo.

> [!NOTE] 
> La dimensione totale di un'attività di avvio deve essere inferiore o uguale a 32768 caratteri, inclusi i file di risorse e le variabili di ambiente. Se l'attività di avvio supera questo limite, l'uso di pacchetti dell'applicazione è un'altra opzione. Si può inoltre creare un archivio compresso contenente i file di risorse, caricarlo come un BLOB in Archiviazione di Azure e quindi decomprimerlo dalla riga di comando dell'attività di avvio. 
>
>

## <a name="upload-and-manage-applications"></a>Caricare e gestire le applicazioni
Per gestire i pacchetti dell'applicazione nell'account batch, è possibile utilizzare le API di [portale di Azure][portal] o gestione batch. Nelle sezioni successive verrà prima di tutto mostrato come collegare un account di archiviazione, quindi verrà descritta l'aggiunta di applicazioni e di pacchetti e la loro gestione con il portale.

### <a name="link-a-storage-account"></a>Collegare un account di archiviazione
Per usare i pacchetti dell'applicazione, è prima necessario collegare un [account di archiviazione di Azure](batch-api-basics.md#azure-storage-account) all'account Batch. Se non è stato ancora configurato un account di archiviazione, il portale di Azure visualizza un avviso nel momento in cui si fa clic per la prima volta su **Applicazioni** nell'account Batch.



!['Nessun account di archiviazione configurato' nel portale di Azure][9]

Il servizio Batch usa l'account di archiviazione associato per archiviare i pacchetti dell'applicazione. Dopo aver collegato i due account, Batch può distribuire automaticamente i pacchetti archiviati nell'account di archiviazione collegato nei nodi di calcolo. Per collegare un account di archiviazione a un account Batch, fare clic su **Account di archiviazione** nella finestra **Avviso** e quindi fare di nuovo clic su **Account di archiviazione**.

![Selezionare il pannello Account di archiviazione nel portale di Azure][10]

È consigliabile creare un account di archiviazione da usare *specificamente* con l'account Batch e selezionarlo qui. Dopo aver creato un account di archiviazione, è possibile collegarlo all'account Batch usando la finestra **Account di archiviazione**.

> [!NOTE] 
> Non è attualmente possibile usare i pacchetti dell'applicazione con un account di archiviazione di Azure configurato con [regole del firewall](../storage/common/storage-network-security.md).
> 

Il servizio Batch usa Archiviazione di Azure per archiviare i pacchetti dell'applicazione come BLOB in blocchi. Viene addebitato il [costo normale][storage_pricing] per i dati BLOB in blocchi e le dimensioni di ogni pacchetto non possono superare le [dimensioni massime del BLOB in blocchi](../storage/common/storage-scalability-targets.md#azure-blob-storage-scale-targets). Controllare la dimensione e il numero dei pacchetti dell'applicazione e rimuovere periodicamente i pacchetti obsoleti per ridurre al minimo il costo.
> 
> 

### <a name="view-current-applications"></a>Visualizzare le applicazioni correnti
Per visualizzare le applicazioni nell'account Batch, fare clic sulla voce di menu **Applicazioni** nel menu di sinistra mentre l'**account Batch** è aperto.

![Riquadro Applicazioni][2]

Selezionando questa opzione di menu viene visualizzata la finestra **Applicazioni**:

![Elenco applicazioni][3]

La finestra visualizza l'ID di ogni applicazione nell'account e le proprietà seguenti:

* **Pacchetti**: il numero delle versioni associate a questa applicazione.
* **Versione predefinita**: la versione dell'applicazione che verrà installata se non si indica una versione quando si specifica l'applicazione per un pool. Questa impostazione è facoltativa.
* **Consenti aggiornamenti**: il valore che specifica se sono consentiti aggiornamenti, eliminazioni e aggiunte per il pacchetto. Se l'opzione è impostata su **No**, gli aggiornamenti del pacchetto e le eliminazioni sono disabilitate per l'applicazione. È possibile aggiungere solo nuove versioni del pacchetto dell'applicazione. Il valore predefinito è **Sì**.

Se si vuole visualizzare la struttura di file del pacchetto dell'applicazione nel nodo di calcolo, passare all'account batch nel portale. Dall'account batch passare a **pool**. Selezionare il pool che contiene i nodi di calcolo a cui si è interessati.

![Nodi nel pool][13]

Dopo aver selezionato il pool, passare al nodo di calcolo in cui è installato il pacchetto dell'applicazione. Da qui, i dettagli del pacchetto dell'applicazione si trovano nella cartella **applicazioni** . Cartelle aggiuntive nel nodo di calcolo contengono altri file, ad esempio attività di avvio, file di output, output degli errori e così via.

![File nel nodo][14]

### <a name="view-application-details"></a>Visualizzare i dettagli dell'applicazione
Per visualizzare i dettagli dell'applicazione, selezionare l'applicazione nella finestra **Applicazioni**.

![Dettagli applicazione][4]

Nei dettagli dell'applicazione è possibile configurare le impostazioni seguenti per l'applicazione.

* **Consenti aggiornamenti**: specificare se i pacchetti dell'applicazione possono essere aggiornati o eliminati. Vedere "Aggiornare o eliminare un pacchetto dell'applicazione" più avanti in questo articolo.
* **Versione predefinita**: specificare un pacchetto dell'applicazione predefinito da distribuire nei nodi di calcolo.
* **Nome visualizzato**: si tratta di un nome descrittivo che la soluzione Batch può usare per visualizzare informazioni sull'applicazione, ad esempio nell'interfaccia utente di un servizio offerto ai clienti tramite Batch.

### <a name="add-a-new-application"></a>Aggiungere una nuova applicazione
Per creare un'applicazione nuova, aggiungere un pacchetto dell'applicazione usando un ID applicazione nuovo e univoco. Il primo pacchetto dell'applicazione aggiunto usando il nuovo ID applicazione crea anche la nuova applicazione.

Fare clic su **Applicazioni** > **Aggiungi**.

![Pannello Nuova applicazione nel portale di Azure][5]

La finestra **Nuova applicazione** contiene i campi seguenti per specificare le impostazioni della nuova applicazione e del pacchetto dell'applicazione.

**ID dell'applicazione**

Questo campo specifica l'ID della nuova applicazione, che è soggetto alle regole standard di convalida dell'ID di Azure Batch. Di seguito sono riportate le regole per fornire un ID applicazione:

* Nei nodi di Windows, l'ID può contenere qualsiasi combinazione di caratteri alfanumerici, trattini e caratteri di sottolineatura. Nei nodi di Linux, sono consentiti solo caratteri alfanumerici e caratteri di sottolineatura.
* Non può contenere più di 64 caratteri.
* Deve essere univoco nell’account Batch.
* Mantiene le maiuscole/minuscole e non fa distinzione tra maiuscole e minuscole.

**Versione**

Questo campo specifica la versione del pacchetto dell'applicazione che si sta caricando. Le stringhe della versione sono soggette alle regole di convalida seguenti:

* Nei nodi di Windows la stringa di versione può contenere qualsiasi combinazione di caratteri alfanumerici, trattini, caratteri di sottolineatura e punti. Nei nodi di Linux, la stringa di versione può contenere solo caratteri alfanumerici e caratteri di sottolineatura.
* Non può contenere più di 64 caratteri.
* Devono essere univoche nell’applicazione.
* Mantengono le maiuscole/minuscole e non fanno distinzione tra maiuscole e minuscole.

**Pacchetto dell'applicazione**

Questo campo specifica il file ZIP contenente i file binari e i file di supporto dell'applicazione necessari per l'esecuzione dell'applicazione. Fare clic sulla casella **Selezionare un file** oppure sull'icona della cartella per cercare e selezionare un file ZIP contenente i file dell'applicazione.

Dopo aver selezionato un file, fare clic su **OK** per avviare il caricamento in Archiviazione di Azure. Al termine dell'operazione di caricamento, il portale visualizza una notifica. A seconda delle dimensioni del file che si sta caricando e della velocità della connessione di rete, l'operazione potrebbe richiedere alcuni minuti.

> [!WARNING]
> Non chiudere la finestra **Nuova applicazione** prima che l'operazione di caricamento sia terminata, altrimenti il processo di caricamento viene interrotto.
> 
> 

### <a name="add-a-new-application-package"></a>Aggiungere un nuovo pacchetto dell’applicazione
Per aggiungere una versione del pacchetto dell'applicazione per un'applicazione esistente, selezionare un'applicazione nella finestra **Applicazioni** e fare clic su **Pacchetti** > **Aggiungi**.

![Pannello per aggiungere un pacchetto dell'applicazione nel portale di Azure][8]

Come si può notare, i campi corrispondono a quelli della **nuova finestra dell'applicazione** , ma la casella **ID applicazione** è disabilitata. Come è stato fatto per la nuova applicazione, specificare la **versione** del nuovo pacchetto, scegliere il file ZIP del **pacchetto dell'applicazione** e quindi fare clic su **OK** per caricare il pacchetto.

### <a name="update-or-delete-an-application-package"></a>Aggiornare o eliminare un pacchetto dell'applicazione
Per aggiornare o eliminare un pacchetto dell'applicazione esistente, aprire i dettagli relativi all'applicazione, fare clic su **Pacchetti**, fare clic sui **puntini di sospensione** nella riga del pacchetto dell'applicazione che si vuole modificare e quindi selezionare l'azione da eseguire.

![Aggiornare o eliminare un pacchetto nel portale di Azure][7]

**Aggiornamento**

Se si seleziona **Aggiorna**, verrà visualizzata la finestra **Aggiorna pacchetto**. Questa finestra è simile alla finestra usata per creare un **nuovo pacchetto dell'applicazione**. In questo caso, però, è abilitato solo il campo di selezione del pacchetto, in cui è possibile specificare un nuovo file ZIP da caricare.

![Pannello per aggiornare un pacchetto nel portale di Azure][11]

**Elimina**

Se si seleziona **Elimina**, verrà chiesto di confermare l'eliminazione della versione del pacchetto e Batch eliminerà il pacchetto da Archiviazione di Azure. Se si elimina la versione predefinita di un'applicazione, verrà rimossa l'impostazione **Versione predefinita** per l'applicazione.

![Eliminare un'applicazione ][12]

## <a name="install-applications-on-compute-nodes"></a>Installare le applicazioni su nodi di calcolo
Dopo aver appreso come gestire i pacchetti dell'applicazione con il portale di Azure, verrà ora descritta la distribuzione dei pacchetti nei nodi di calcolo e la relativa esecuzione con attività di Batch.

### <a name="install-pool-application-packages"></a>Installare pacchetti dell'applicazione nel pool
Per installare un pacchetto dell'applicazione in tutti i nodi di calcolo di un pool, specificare uno o più *riferimenti* al pacchetto dell'applicazione per il pool. I pacchetti dell'applicazione specificati per un pool vengono installati su ciascun nodo di calcolo quando tale nodo viene aggiunto al pool e quando il nodo viene riavviato o ne viene ricreata l'immagine.

In batch .NET specificare uno o più [CloudPool][net_cloudpool]. [ApplicationPackageReferences][net_cloudpool_pkgref] quando si crea un nuovo pool o un pool esistente. La classe [ApplicationPackageReference][net_pkgref] specifica un ID applicazione e una versione da installare nei nodi di calcolo di un pool.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package is installed on each.
await myCloudPool.CommitAsync();
```

> [!IMPORTANT]
> Se la distribuzione di un pacchetto dell'applicazione non riesce per qualsiasi motivo, il servizio batch contrassegna il nodo come [inutilizzabile][net_nodestate] e nessuna attività è pianificata per l'esecuzione in tale nodo. In questo caso è necessario **riavviare** il nodo per reinizializzare la distribuzione del pacchetto. Il riavvio del nodo consente anche di pianificarne di nuovo le attività.
> 
> 

### <a name="install-task-application-packages"></a>Installare pacchetti dell'applicazione per le attività
Come per un pool, specificare i *riferimenti* del pacchetto dell'applicazione per un'attività. Quando un'attività è pianificata per l'esecuzione su un nodo, il pacchetto viene scaricato ed estratto appena prima dell'esecuzione della riga di comando dell'attività. Se un pacchetto specificato con la versione corrispondente è già installato nel nodo, non verrà scaricato e verrà usato il pacchetto esistente.

Per installare un pacchetto dell'applicazione di attività, configurare il [CloudTask][net_cloudtask]dell'attività. Proprietà [ApplicationPackageReferences][net_cloudtask_pkgref] :

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>Eseguire le applicazioni installate
I pacchetti specificati per un pool o un'attività vengono scaricati ed estratti in una directory denominata all'interno del nodo `AZ_BATCH_ROOT_DIR` . Batch crea anche una variabile di ambiente che contiene il percorso della directory denominata. Le righe di comando dell'attività usano questa variabile di ambiente quando fanno riferimento all'applicazione nel nodo. 

Nei nodi di Windows la variabile è nel formato seguente:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Nei nodi di Linux, il formato è leggermente diverso. Punti (.), trattini (-) e simboli di cancelletto (#) vengono convertiti in caratteri di sottolineatura nella variabile di ambiente. Si noti inoltre che il caso dell'ID dell'applicazione viene mantenuto. Ad esempio:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` e `version` sono valori che corrispondono all'applicazione e alla versione del pacchetto specificati per la distribuzione. Se ad esempio si specifica l'installazione della versione 2.7 dell'applicazione *blender* nei nodi di Windows, le righe di comando dell'attività useranno questa variabile di ambiente per accedere ai file corrispondenti:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

Nei nodi di Linux, specificare la variabile di ambiente nel formato seguente: Convertire punti (.), trattini (-) e cancelletti (#) in caratteri di sottolineatura e mantenere le lettere maiuscole/minuscole dell'ID applicazione:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
``` 

Quando si carica un pacchetto dell'applicazione, è possibile specificare una versione predefinita da distribuire ai nodi di calcolo. Se è stata specificata una versione predefinita per un'applicazione, è possibile omettere il suffisso della versione quando si fa riferimento l'applicazione. È possibile specificare la versione predefinita dell'applicazione nella finestra **Applicazioni** del portale di Azure, come illustrato in [Caricare e gestire le applicazioni](#upload-and-manage-applications).

Se ad esempio è stata specificata la versione predefinita "2.7" per l'applicazione *blender* e le attività fanno riferimento alla variabile di ambiente seguente, i nodi di Windows eseguiranno la versione 2.7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Il frammento di codice seguente mostra una riga di comando dell'attività di esempio che consente di avviare la versione predefinita dell'applicazione *blender* :

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Per altre informazioni sulle impostazioni dell'ambiente dei nodi di calcolo, vedere [Impostazioni di ambiente per le attività](batch-api-basics.md#environment-settings-for-tasks) in [Panoramica delle funzionalità di Batch per sviluppatori](batch-api-basics.md).
> 
> 

## <a name="update-a-pools-application-packages"></a>Aggiornare i pacchetti dell’applicazione di un pool
Se un pool esistente è già stato configurato con un pacchetto dell’applicazione, è possibile specificare un nuovo pacchetto per il pool. Se si specifica un nuovo riferimento al pacchetto per un pool, si applica quanto segue:

* Il servizio Batch installa il pacchetto appena specificato su tutti i nuovi nodi aggiunti al pool e su tutti i nodi esistenti riavviati o per i quali viene ricreata l'immagine.
* I nodi di calcolo che si trovano già nel pool quando si aggiornano i riferimenti al pacchetto non installano automaticamente il nuovo pacchetto dell’applicazione. Questi nodi di calcolo devono essere riavviati o ne deve essere ricreata l'immagine per ricevere il nuovo pacchetto.
* Quando viene distribuito un nuovo pacchetto, le variabili di ambiente create riflettono i riferimenti al nuovo pacchetto dell'applicazione.

In questo esempio il pool esistente ha la versione 2,7 dell'applicazione *Blender* configurata come uno dei relativi [CloudPool][net_cloudpool]. [ApplicationPackageReferences][net_cloudpool_pkgref]. Per aggiornare i nodi del pool con la versione 2.76 b, specificare un nuovo [ApplicationPackageReference][net_pkgref] con la nuova versione ed eseguire il commit della modifica.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Ora che è stata configurata la nuova versione, il servizio Batch installa la versione 2.76b su qualsiasi *nuovo* nodo aggiunto al pool. Per installare la versione 2.76b nei nodi *già* presenti nel pool, riavviarli o ricrearne l'immagine. Notare che i nodi riavviati mantengono i file delle distribuzioni precedenti del pacchetto.

## <a name="list-the-applications-in-a-batch-account"></a>Elencare le applicazioni in un account Batch
È possibile elencare le applicazioni e i relativi pacchetti in un account batch usando [Metodo applicationoperations][net_appops]. Metodo [ListApplicationSummaries][net_appops_listappsummaries] .

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="wrap-up"></a>Eseguire il wrapping
Con i pacchetti dell'applicazione è possibile assistere i clienti nella scelta delle applicazioni per i loro processi e specificare la versione corretta da usare durante l'elaborazione dei processi con il servizio abilitato per Batch. I clienti possono inoltre caricare e monitorare le applicazioni usate nel servizio.

## <a name="next-steps"></a>Passaggi successivi
* L' [API REST di batch][api_rest] fornisce anche il supporto per lavorare con i pacchetti dell'applicazione. Vedere ad esempio l'elemento [applicationPackageReferences][rest_add_pool_with_packages] in [aggiungere un pool a un account][rest_add_pool] per informazioni su come specificare i pacchetti da installare usando l'API REST. Vedere [applicazioni][rest_applications] per informazioni dettagliate su come ottenere informazioni sull'applicazione tramite l'API REST di batch.
* È possibile scoprire come [gestire quote e account Azure Batch con la gestione .NET per Batch](batch-management-dotnet.md)a livello di codice. La libreria [batch Management .NET][api_net_mgmt] può abilitare le funzionalità di creazione ed eliminazione di account per l'applicazione o il servizio batch.

[api_net]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/client?view=azure-dotnet
[api_net_mgmt]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/management?view=azure-dotnet
[api_rest]: https://docs.microsoft.com/rest/api/batchservice/
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagramma di alto livello di pacchetti applicazione"
[2]: ./media/batch-application-packages/app_pkg_02.png "Riquadro Applicazioni nel portale di Azure"
[3]: ./media/batch-application-packages/app_pkg_03.png "Pannello Applicazioni nel portale di Azure"
[4]: ./media/batch-application-packages/app_pkg_04.png "Pannello Dettagli applicazione nel portale di Azure"
[5]: ./media/batch-application-packages/app_pkg_05.png "Pannello Nuova applicazione nel portale di Azure"
[7]: ./media/batch-application-packages/app_pkg_07.png "Elenco a discesa per aggiornamento o eliminazione pacchetto nel portale di Azure"
[8]: ./media/batch-application-packages/app_pkg_08.png "Pannello per nuovo pacchetto dell'applicazione nel portale di Azure"
[9]: ./media/batch-application-packages/app_pkg_09.png " Nessun account di archiviazione collegato"
[10]: ./media/batch-application-packages/app_pkg_10.png "Selezionare il pannello Account di archiviazione nel portale di Azure"
[11]: ./media/batch-application-packages/app_pkg_11.png "Pannello Aggiorna pacchetto nel portale di Azure"
[12]: ./media/batch-application-packages/app_pkg_12.png "Finestra di conferma eliminazione pacchetto nel portale di Azure"
[13]: ./media/batch-application-packages/package-file-structure.png "Informazioni sui nodi di calcolo in portale di Azure"
[14]: ./media/batch-application-packages/package-file-structure-node.png "File nel nodo di calcolo visualizzato in portale di Azure"
