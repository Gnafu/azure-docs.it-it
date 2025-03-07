---
title: Patch Orchestration Application di Azure Service Fabric | Microsoft Docs
description: Applicazione per automatizzare l'applicazione di patch ai sistemi operativi in un cluster di Service Fabric.
services: service-fabric
documentationcenter: .net
author: khandelwalbrijeshiitr
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: brkhande
ms.openlocfilehash: ccc0399b6ac886ec8d9ef7d207c3539f1d078070
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65951934"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Applicare patch al sistema operativo Windows nel cluster di Service Fabric

> 
> [!IMPORTANT]
> Versione dell'applicazione 1.2. * sarà più supportata 30 aprile 2019. Eseguire l'aggiornamento alla versione più recente.


[Aggiornamenti automatici dell'immagine del sistema operativo con i set di scalabilità di macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) è la procedura consigliata per mantenere i sistemi operativi con patch in Azure e Patch Orchestration Application è un wrapper del servizio dei sistemi Gestione ripristini di Service Fabric che consente la pianificazione delle patch del sistema operativo basate sulla configurazione per i cluster non ospitati in Azure. Patch Orchestration Application non è obbligatorio per i cluster non ospitati in Azure, ma la pianificazione dell'installazione delle patch dai domini di aggiornamento è necessaria per applicare le patch agli host di cluster di Service Fabric senza tempi di inattività.

Patch Orchestration Application è un'applicazione Azure Service Fabric che automatizza l'applicazione di patch nei sistemi operativi in un cluster di Service Fabric senza tempi di inattività.

L'app Patch Orchestration offre le funzionalità seguenti:

- **Installazione automatica dell'aggiornamento del sistema operativo**. Gli aggiornamenti del sistema operativo vengono scaricati e installati automaticamente. I nodi del cluster vengono riavviati in base alle esigenze senza tempi di inattività del cluster.

- **Integrazione dell'integrità e l'applicazione di patch compatibile con il cluster**. Durante l'applicazione degli aggiornamenti, Patch Orchestration Application esegue il monitoraggio dell'integrità dei nodi del cluster. I nodi del cluster sono aggiornati un nodo alla volta o un dominio di aggiornamento alla volta. Se l'integrità del cluster si arresta a causa del processo di applicazione di patch, l'applicazione di patch viene interrotta per impedire l'aggravarsi del problema.

## <a name="internal-details-of-the-app"></a>Dettagli interni dell'app

Patch Orchestration Application è costituita dai sottocomponenti seguenti:

- **Coordinator Service**: il servizio con stato è responsabile di quanto segue:
    - Il coordinamento del processo di Windows Update nell'intero cluster.
    - L'archiviazione del risultato delle operazioni completate di Windows Update.
- **Node Agent Service**: è un servizio senza stato che viene eseguito in tutti i nodi del cluster di Service Fabric. Il servizio è responsabile per:
    - Il bootstrap del Node Agent NTService.
    - Il monitoraggio di Node Agent NTService
- **Node Agent NTService**: questo servizio di Windows NT viene eseguito con un privilegio di livello superiore (SYSTEM). Il Node Agent Service e il Coordinator Service vengono invece eseguiti con un privilegio di livello inferiore (NETWORK SERVICE). Il servizio è responsabile dell'esecuzione dei seguenti processi di Windows Update in tutti i nodi del cluster:
    - Disabilitazione della connessione automatica a Windows Update nel nodo.
    - Download e installazione di Windows Update in base al criterio fornito dall'utente.
    - Riavvio della macchina dopo l'installazione di Windows Update.
    - Caricamento dei risultati di Windows Update nel Coordinator Service.
    - Generazione di report sull'integrità in caso di un'operazione non riuscita dopo l'esaurimento di tutti i tentativi.

> [!NOTE]
> Patch Orchestration App usa il sistema di servizio di gestione della riparazione di Service Fabric per disabilitare o abilitare il nodo ed eseguire i controlli di integrità. L'attività di riparazione creata da Patch Orchestration App tiene traccia dell'avanzamento di Windows Update per ogni nodo.

## <a name="prerequisites"></a>Prerequisiti

> [!NOTE]
> Versione minima di .NET framework necessaria è 4.6.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Abilitare il servizio di gestione della riparazione (se non è già in esecuzione)

Patch Orchestration App richiede che il servizio del sistema di gestione della riparazione sia abilitato nel cluster.

#### <a name="azure-clusters"></a>Cluster di Azure

I cluster di Azure al livello di durabilità silver hanno il servizio di gestione della riparazione abilitato per impostazione predefinita. I cluster di Azure al livello di durabilità gold potrebbero avere o non avere il servizio di gestione della riparazione abilitato, a seconda relativa data di creazione. I cluster di Azure al livello di durabilità bronze, per impostazione predefinita, non hanno il servizio di gestione della riparazione abilitato. Se il servizio è già abilitato, è possibile vederlo in esecuzione nella sezione relativa ai servizi del sistema in Service Fabric Explorer.

##### <a name="azure-portal"></a>Portale di Azure
È possibile abilitare il servizio di gestione della riparazione dal portale di Azure al momento della configurazione del cluster. Selezionare l'opzione **Includi funzionalità di gestione ripristini** in **Aggiungi funzionalità** durante la configurazione del cluster.
![Immagine dell'attivazione del servizio di gestione della riparazione dal portale di Azure](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>Modello di distribuzione di Azure Resource Manager
In alternativa, è possibile usare il [modello di distribuzione Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) per abilitare il servizio di gestione della riparazione nei cluster nuovi ed esistenti di Service Fabric. Ottenere il modello per il cluster che si vuole distribuire. È possibile usare i modelli di esempio o creare un modello di distribuzione Azure Resource Manager personalizzato. 

Per abilitare il servizio di gestione della riparazione tramite il [modello di distribuzione Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm):

1. Verificare prima di tutto che `apiversion` sia impostato su `2017-07-01-preview` per la risorsa `Microsoft.ServiceFabric/clusters`. Se il valore è diverso, è necessario aggiornare `apiVersion` al valore `2017-07-01-preview` o superiore:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. A questo punto, abilitare il servizio di gestione della riparazione aggiungendo la sezione seguente `addonFeatures` dopo la sezione `fabricSettings`:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Dopo aver aggiornato il modello del cluster con queste modifiche, applicarle e consentire la conclusione dell'aggiornamento. È ora possibile vedere il servizio del sistema di gestione della riparazione in esecuzione nel cluster. Viene chiamato `fabric:/System/RepairManagerService` nella sezione relativa ai servizi del sistema in Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Cluster locali autonomi

È possibile usare le [Impostazioni di configurazione per un cluster autonomo in Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest) per abilitare il servizio di gestione della riparazione nei cluster di Service Fabric nuovi ed esistenti.

Per abilitare il servizio di gestione della riparazione:

1. In primo luogo, verificare che `apiversion` in [Configurazioni generali del cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) sia impostato su `04-2017` o versione successiva:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

2. A questo punto abilitare il servizio di gestione della riparazione aggiungendo la sezione seguente `addonFeatures` dopo la sezione `fabricSettings`, come illustrato di seguito:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Aggiornare il manifesto del cluster con queste modifiche usando il manifesto del cluster aggiornato [Creare un cluster autonomo in esecuzione su Windows Server](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) o [Aggiornare il cluster autonomo di Azure Service Fabric in Windows Server](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server). Quando il cluster è in esecuzione con il manifesto del cluster aggiornato, è possibile vedere il servizio del sistema di gestione della riparazione in esecuzione nel cluster, denominato `fabric:/System/RepairManagerService`, nella sezione relativa ai servizi del sistema in Service Fabric Explorer.

### <a name="configure-windows-updates-for-all-nodes"></a>Configurare gli aggiornamenti di Windows per tutti i nodi

Gli aggiornamenti automatici di Windows potrebbero causare la perdita di disponibilità perché più nodi del cluster possono riavviarsi nello stesso momento. Patch Orchestration App, per impostazione predefinita, tenta di disabilitare la connessione automatica a Windows Update su ogni nodo del cluster. Tuttavia, nel caso in cui le impostazioni vengano gestite da un amministratore o dai Criteri di gruppo, è consigliabile impostare i criteri di Windows Update sulla notifica prima del download in modo esplicito.

## <a name="download-the-app-package"></a>Scaricare il pacchetto dell'app

Per scaricare il pacchetto dell'applicazione, visitare il rilascio di GitHub [pagina](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) di Patch Orchestration Application.

## <a name="configure-the-app"></a>Configurare l'app

Il comportamento di Patch Orchestration App può essere configurato per soddisfare le esigenze. Eseguire l'override dei valori predefiniti passando il parametro dell'applicazione durante la creazione o l'aggiornamento dell'applicazione. È possibile fornire i parametri dell'applicazione specificando `ApplicationParameter` ai cmdlet `Start-ServiceFabricApplicationUpgrade` o `New-ServiceFabricApplication`.

|**Parametro**        |**Tipo**                          | **Dettagli**|
|:-|-|-|
|MaxResultsToCache    |long                              | Numero massimo di risultati di Windows Update memorizzabili nella cache. <br>Il valore predefinito è 3000 presumendo che il: <br> - Numero di nodi sia 20. <br> - Numero di aggiornamenti eseguiti su un nodo per ogni mese sia pari a cinque. <br> - Numero di risultati per ogni operazione sia pari a 10. <br> - I risultati per gli ultimi tre mesi debbano essere archiviati. |
|TaskApprovalPolicy   |Enum <br> {NodeWise, UpgradeDomainWise}                          |TaskApprovalPolicy indica i criteri che devono essere usati dal Coordinator Service per installare gli aggiornamenti di Windows Update nei nodi del cluster di Service Fabric.<br>                         I valori consentiti sono i seguenti: <br>                                                           <b>NodeWise</b>. Windows Update viene installato un nodo alla volta. <br>                                                           <b>UpgradeDomainWise</b>. Windows Update viene installato un dominio di aggiornamento alla volta (al massimo, tutti i nodi appartenenti a un dominio di aggiornamento possono passare per Windows Update).<br> Fare riferimento alla sezione delle [domande frequenti](#frequently-asked-questions) su come scegliere i migliori criteri per il cluster.
|LogsDiskQuotaInMB   |long  <br> (Valore predefinito: 1024)               |Dimensione massima in MB dei log di Patch Orchestration App che è possibile salvare in modo permanente e locale sui nodi.
| WUQuery               | string<br>(Valore predefinito: "IsInstalled=0")                | Eseguire una query per ottenere gli aggiornamenti di Windows. Per altre informazioni, vedere [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx).
| InstallWindowsOSOnlyUpdates | Boolean <br> (impostazione predefinita: false)                 | Usare questo flag per controllare quali aggiornamenti devono essere scaricati e installati. Sono consentiti i valori seguenti: <br>true: installa solo gli aggiornamenti del sistema operativo Windows.<br>false: installa tutti gli aggiornamenti disponibili nel computer.          |
| WUOperationTimeOutInMinutes | Int <br>(Valore predefinito: 90)                   | Specifica il timeout per qualsiasi operazione di Windows Update (ricerca, download o installazione). L'operazione viene interrotta se non viene completata entro il timeout specificato.       |
| WURescheduleCount     | Int <br> (Valore predefinito: 5)                  | Il numero massimo di volte in cui il servizio ripianifica l'aggiornamento di Windows quando un'operazione continua ad avere esito negativo.          |
| WURescheduleTimeInMinutes | Int <br>(Valore predefinito: 30) | L'intervallo con cui il servizio ripianifica l'aggiornamento di Windows se il problema persiste. |
| WUFrequency           | Stringa separata da virgole (Valore predefinito: "Weekly, Wednesday, 7:00:00")     | La frequenza di installazione di Windows Update. Il formato e i valori possibili sono: <br>- Monthly, DD, HH:MM:SS, ad esempio, Monthly, 5,12:22:32.<br>I valori consentiti per il campo DD (day) sono i numeri compresi nell'intervallo 1-28 e "last". <br> -   Weekly, DAY, HH:MM:SS, ad esempio Weekly, Tuesday, 12:22:32.  <br> -   Daily, HH:MM:SS, ad esempio, Daily, 12:22:32.  <br> - None: indica che non deve essere eseguito Windows Update.  <br><br> Si noti che gli orari sono in formato UTC.|
| AcceptWindowsUpdateEula | Boolean <br>Predefinito: True | Impostando questo flag, l'applicazione accetta il contratto di licenza dell'utente finale per Windows Update per conto del proprietario della macchina.              |

> [!TIP]
> Se si desidera che Windows Update venga eseguito immediatamente, impostare `WUFrequency` in relazione al tempo di distribuzione dell'applicazione. Ad esempio, si supponga di disporre di un cluster di test a cinque nodi e si prevede di distribuire l'app all'incirca alle 17:00:00 UTC. Se si presuppone che l'aggiornamento o la distribuzione dell'applicazione richieda al massimo 30 minuti, impostare WUFrequency su "Daily, 17:30:00".

## <a name="deploy-the-app"></a>Distribuire l'app

1. Completare tutti i passaggi preliminari per preparare il cluster.
2. Distribuire Patch Orchestration App come qualsiasi altra app di Service Fabric. È possibile distribuire l'app tramite PowerShell. Seguire la procedura in [Distribuire e rimuovere applicazioni con PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
3. Per configurare l'applicazione al momento della distribuzione, passare `ApplicationParameter` al cmdlet `New-ServiceFabricApplication`. Per praticità, è stato fornito lo script Deploy.ps1 insieme all'applicazione. Per usare lo script:

    - Connettersi a un cluster di Service Fabric tramite `Connect-ServiceFabricCluster`.
    - Eseguire lo script Deploy.ps1 di PowerShell con il valore `ApplicationParameter` appropriato.

> [!NOTE]
> Mantenere lo script e la cartella dell'applicazione PatchOrchestrationApplication nella stessa directory.

## <a name="upgrade-the-app"></a>Aggiornare l'app

Per aggiornare una Patch Orchestration App esistente tramite PowerShell, seguire la procedura in [Aggiornamento di un'applicazione di Service Fabric mediante PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell).

## <a name="remove-the-app"></a>Rimuovere l'app

Per rimuovere l'applicazione, seguire la procedura in [Distribuire e rimuovere applicazioni con PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).

Per praticità, è stato fornito lo script Undeploy.ps1 insieme all'applicazione. Per usare lo script:

  - Connettersi a un cluster di Service Fabric tramite ```Connect-ServiceFabricCluster```.

  - Eseguire lo script Undeploy.ps1 di PowerShell.

> [!NOTE]
> Mantenere lo script e la cartella dell'applicazione PatchOrchestrationApplication nella stessa directory.

## <a name="view-the-windows-update-results"></a>Visualizzare i risultati di Windows Update

Patch Orchestration Application espone l'API REST per visualizzare i risultati cronologici all'utente. Un esempio del file JSON dei risultati:
```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2019-05-13T08:44:56.4836889Z",
        "OperationStartTime": "2019-05-13T08:44:33.5285601Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0,
            "HResult": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

I campi del formato JSON sono descritti di seguito.

Campo | Valori | Dettagli
-- | -- | --
OperationResult | 0 - Completata<br> 1 - Completata con errori<br> 2 - Non riuscita<br> 3 - Interrotta<br> 4 - Interrotta con timeout | Indica il risultato dell'operazione globale, che in genere implica l'installazione di uno o più aggiornamenti.
ResultCode | Stesso di OperationResult | Questo campo indica il risultato dell'operazione di installazione di un singolo aggiornamento.
OperationType | 1 - Installazione<br> 0 - Ricerca e download.| Installation è l'unico OperationType visualizzato nei risultati per impostazione predefinita.
WindowsUpdateQuery | Impostazione predefinita: "IsInstalled = 0" |Query di aggiornamento di Windows usata per ricercare gli aggiornamenti. Per altre informazioni, vedere [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx).
RebootRequired | true: il riavvio è necessario<br> false: il riavvio non è necessario | Indica se è necessario riavviare per completare l'installazione degli aggiornamenti.
OperationStartTime | DateTime | Indica l'ora in cui operation(Download/Installation) avviato.
OperationTime | DateTime | Indica l'ora in cui operation(Download/Installation) completata.
HResult | 0 - Successful<br> altro - errore| Indica il motivo dell'errore dell'aggiornamento di windows con updateID "7392acaf-6a85-427c-8a8d-058c25beb0d6".

Se non è stato ancora pianificato alcun aggiornamento, il file JSON dei risultati è vuoto.

Accedere al cluster per query Windows Update i risultati. Trovare quindi l'indirizzo della replica per il primario del Coordinator Service e raggiungere l'URL dal browser: http://&lt;REPLICA-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults.

L'endpoint REST per Coordinator Service dispone di una porta dinamica. Per controllare l'URL esatto, fare riferimento a Service Fabric Explorer. Ad esempio, i risultati sono disponibili in `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults`.

![Immagine dell'endpoint REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


Nel caso in cui il proxy inverso sia abilitato nel cluster, l'utente può accedere all'URL anche dall'esterno del cluster.
L'endpoint da scegliere è: http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults.

Per abilitare il proxy inverso nel cluster, seguire la procedura in [Proxy inverso in Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> Dopo aver configurato il proxy inverso, tutti i microservizi nel cluster che espongono un endpoint HTTP sono indirizzabili dall'esterno del cluster.

## <a name="diagnosticshealth-events"></a>Eventi di diagnostica/integrità

La sezione seguente descrive come diagnosi/debug dei problemi con l'installazione degli aggiornamenti tramite Patch Orchestration Application nei cluster di Service Fabric.

> [!NOTE]
> Si devono avere versione v1.4.0 di POA installato per ottenere molte dell'indicate di seguito self-miglioramenti della diagnostica.

Crea il NodeAgentNTService [riparare attività](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtask?view=azure-dotnet) per installare gli aggiornamenti nei nodi. Ogni attività viene quindi preparata da CoordinatorService in base ai criteri di approvazione di attività. Le attività predefinite vengono infine approvate da Repair Manager che non approvo qualsiasi attività se il cluster si trovi in stato non integro. Consente di passare procedura dettagliata per comprendere il modo in cui gli aggiornamenti procedere su un nodo.

1. NodeAgentNTService, in esecuzione in ogni nodo, Cerca aggiornamenti di Windows disponibili all'ora pianificata. Se sono disponibili aggiornamenti, viene prelevato e li scarica sul nodo.
2. Dopo gli aggiornamenti vengono scaricati, NodeAgentNTService, Crea attività di ripristino corrispondente per il nodo con il nome POS___ < unique_id >. È possibile visualizzare queste attività usando i cmdlet di ripristino [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps) o in SFX nella sezione dei dettagli nodo. Dopo aver creato l'attività di ripristino, si sposta rapidamente al [richiesti stato](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtaskstate?view=azure-dotnet).
3. Il servizio coordinatore, periodicamente cerca le attività di riparazione in stato di attestazione e prosegue e li Aggiorna stato di base di TaskApprovalPolicy preparazione. Se il TaskApprovalPolicy è configurato per essere NodeWise, un'attività di ripristino corrispondente a un nodo è preparato solo se non è attualmente alcuna altra attività di riparazione in stato di preparazione/Approved/Executing/ripristino del database. Analogamente, nel caso in cui di UpgradeWise TaskApprovalPolicy, viene verificato in qualsiasi momento sono disponibili le attività in questi stati solo per i nodi che appartengono allo stesso dominio di aggiornamento. Una volta un'attività di ripristino viene spostata in stato di preparazione, il nodo di Service Fabric corrispondente è [disabilitato](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) preventivo come "Riavvia".

   Poa(v1.4.0 and ABOVE) post gli eventi con proprietà "ClusterPatchingStatus" su CoordinaterService per visualizzare i nodi che sono in corso corretti. Immagine seguente mostra che gli aggiornamenti è installato _poanode_0:

    [![Immagine del Cluster lo stato dell'applicazione di patch](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

4. Dopo che il nodo è disabilitato, l'attività di ripristino viene spostato in stato Executing. Si noti che un'attività di ripristino bloccato in preparazione dello stato, dopo aver perché un nodo è bloccato nello stato disabilitato possono comportare il blocco di nuove attività di ripristino e quindi arrestare l'applicazione di patch del cluster.
5. Una volta durante l'esecuzione dello stato attività di ripristino, viene avviata l'installazione di patch su tale nodo. In questo caso, una volta installata la patch, il nodo può o potrebbe non essere riavviato a seconda della patch. Dopo che l'attività di ripristino viene spostato al ripristino dello stato, che abilita nuovamente il nodo nuovo e quindi viene contrassegnata come completata.

   In v1.4.0 e versioni successive le versioni dell'applicazione, lo stato dell'aggiornamento è reperibile esaminando gli eventi di integrità nel NodeAgentService con la proprietà "WUOperationStatus-[NodeName]". Le sezioni evidenziate nelle immagini seguenti mostrano lo stato di windows update nel nodo 'poanode_0' e 'poanode_2':

   [![Immagine dello stato di operazione di Windows update](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![Immagine dello stato di operazione di Windows update](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   Una possibile anche ottenere i dettagli usando powershell, connettendosi al cluster e recuperare lo stato dell'attività di riparazione tramite [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps). Come esempio seguente illustra tale "POS__poanode_2_125f2969-933c-4774-85 d 1-ebdf85e79f15" attività è in stato DownloadComplete. Significa che gli aggiornamenti sono stati scaricati nel nodo "poanode_2" e verrà tentata l'installazione dopo che l'attività passa allo stato Executing.

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   Se c'è ancora molto da trovare quindi, accedere a macchine Virtuali/macchine virtuali specifiche per trovare ulteriori informazioni sul problema mediante i registri eventi di Windows. Quanto sopra accennato attività di ripristino può avere solo questi stati secondari dell'executor:

      ExecutorSubState | Dettagli
    -- | -- 
      None=1 |  Implica che non vi era un'operazione in corso nel nodo. Transizioni di stato possibili.
      DownloadCompleted=2 | Implica un'operazione di download è stata completata con esito positivo, parziale o errore o.
      InstallationApproved=3 | Implica un'operazione di download è stata completata in precedenza e di gestione della riparazione ha approvato l'installazione.
      InstallationInProgress=4 | Corrisponde allo stato di esecuzione dell'attività di ripristino.
      InstallationCompleted=5 | Implica l'installazione è stata completata con esito positivo, esito positivo parziale o errore.
      RestartRequested=6 | Implica patch installazione è stata completata ed è presente un'azione di riavvio in sospeso nel nodo.
      RestartNotNeeded=7 |  Implica che il riavvio non è stato necessario dopo il completamento dell'installazione della patch.
      RestartCompleted=8 | Implica che il riavvio completato correttamente.
      OperationCompleted=9 | Completata l'operazione di aggiornamento di Windows.
      OperationAborted=10 | Implica che l'operazione di aggiornamento di windows viene interrotta.

6. In v1.4.0 e di sopra dell'applicazione, al termine del tentativo di aggiornamento in un nodo, un evento con la proprietà "WUOperationStatus-[NodeName]" è registrato per NodeAgentService per notificare quando tenteranno successivo, per scaricare e installare l'aggiornamento, avviare. Vedere l'immagine seguente:

     [![Immagine dello stato di operazione di Windows update](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostic-logs"></a>Log di diagnostica

Vengono raccolti i log dell'app di orchestrazione delle patch come parte dei log di runtime di Service Fabric.

Nel caso in cui si vogliano acquisire i log tramite lo strumento o la pipeline di diagnostica preferita. Patch orchestration application Usa gli ID provider corretti di seguito per registrare gli eventi tramite [origine evento](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Report sull'integrità

Patch Orchestration App pubblica anche i report sull'integrità per il Coordinator Service o il Node Agent Service nei casi seguenti:

#### <a name="the-node-agent-ntservice-is-down"></a>Il Node Agent NTService è inattivo

Se il Node Agent NTService è inattivo in un nodo, viene generato un report sull'integrità dei livelli di avviso per il Node Agent Service.

#### <a name="the-repair-manager-service-is-not-enabled"></a>Il servizio di gestione della riparazione non è abilitato

Se il servizio di gestione della riparazione non viene individuato nel cluster, viene generato un report sull'integrità dei livelli di avviso per il Coordinator Service.

## <a name="frequently-asked-questions"></a>Domande frequenti

D: **Perché il cluster è in uno stato di errore quando Patch Orchestration App è in esecuzione?**

R. Durante il processo di installazione, Patch Orchestration App disabilita o riavvia i nodi, il che può comportare temporaneamente la violazione dell'integrità del cluster.

A seconda dei criteri impostati per l'applicazione, durante un'operazione di applicazione di patch è possibile che diventi non disponibile un singolo nodo *oppure* un intero dominio di aggiornamento simultaneamente.

Al termine dell'installazione di Windows Update, i nodi vengono abilitati di nuovo dopo il riavvio.

Nell'esempio seguente il cluster è passato temporaneamente a uno stato di errore perché due nodi erano inattivi e il criterio MaxPercentageUnhealthNodes è stato violato. L'errore è temporaneo fino a quando l'operazione di aggiornamento è in corso.

![Immagine del cluster non integro](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Se il problema persiste, fare riferimento alla sezione relativa alla risoluzione dei problemi.

D: **Patch Orchestration App è in stato di avviso**

R. Verificare se la causa principale è un report sull'integrità inviato all'applicazione. L'avviso in genere contiene i dettagli del problema. Se il problema è temporaneo, è previsto il ripristino automatico dell'applicazione da questo stato.

D: **Che cosa è possibile fare se il cluster non è integro ed è necessario eseguire un aggiornamento del sistema operativo?**

R. Patch Orchestration App non installa gli aggiornamenti mentre il cluster non è in uno stato di integrità. Provare a portare il cluster in uno stato di integrità per sbloccare il flusso di lavoro di Patch Orchestration App.

D: **È necessario impostare TaskApprovalPolicy come 'NodeWise' o 'UpgradeDomainWise' per il cluster?**

R. 'UpgradeDomainWise' rende l'applicazione generare di patch ai cluster più veloce grazie all'applicazione di patch in parallelo a tutti i nodi appartenenti a un dominio di aggiornamento. Ciò significa che i nodi appartenenti a un intero dominio di aggiornamento risulteranno non disponibili (in stato [disabilitato](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)) durante il processo dell'applicazione di patch.

Al contrario, i criteri 'NodeWise' eseguono la patch di un solo nodo alla volta. Ciò implica che l'applicazione della patch a tutto il cluster richiederà più tempo. Tuttavia, al massimo un solo nodo sarebbe non disponibile (in stato [disabilitato](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)) durante il processo dell'applicazione di patch.

Se il cluster è in grado di funzionare con un numero N-1 dei domini di aggiornamento durante il ciclo di applicazione di patch (dove N è il numero totale di domini di aggiornamento nel cluster), è possibile impostare i criteri come 'UpgradeDomainWise'; altrimenti impostarli su 'NodeWise'.

D: **Quanto tempo ci vuole ad applicare una patch a un nodo?**

R. L'applicazione di una patch a un nodo può richiedere una quantità di tempo che va da minuti (ad esempio, [aggiornamenti delle definizioni di Windows Defender](https://www.microsoft.com/en-us/wdsi/definitions)) ad ore (ad esempio, [aggiornamenti cumulativi di Windows](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)). Il tempo necessario per applicare una patch a un nodo dipende principalmente dai fattori seguenti 
 - Dimensione degli aggiornamenti
 - Numero degli aggiornamenti da applicare in una finestra di gestione delle patch
 - Il tempo impiegato per installare gli aggiornamenti, riavviare il nodo (se richiesto) e completare i passaggi di installazione post-riavvio.
 - Prestazioni della VM/computer e le condizioni della rete.

D: **Quanto tempo occorre per applicare patch a un intero cluster?**

R. Il tempo necessario per applicare patch a un intero cluster dipende dai fattori seguenti:

- Tempo necessario per applicare patch a un nodo.
- I criteri del Coordinator Service. I criteri predefiniti, `NodeWise`, comportano l'applicazione di patch a un solo nodo alla volta e tale operazione è più lenta di `UpgradeDomainWise`. Ad esempio:  si supponga che l'applicazione di patch a un nodo richieda circa 1 ora, e si debbano applicare patch a un cluster di 20 nodi (dello stesso tipo) con 5 domini di aggiornamento, ognuno dei quali contiene 4 nodi.
    - Sono necessarie circa 20 ore per applicare patch all'intero cluster, se il criterio è `NodeWise`
    - Sono necessarie 5 ore se il criterio è `UpgradeDomainWise`
- Caricamento del cluster - ogni operazione di installazione di patch richiede la rilocazione del carico di lavoro dei clienti in altri nodi disponibili nel cluster. Un nodo in fase di patch può essere in stato di [disabilitazione](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling) durante l'operazione. Se il cluster è in prossimità di picchi di carico, il processo di disabilitazione richiederà più tempo. Di conseguenza il processo complessivo dell'applicazione di patch in tali condizioni potrebbe risultare lento.
- Errori di integrità del cluster durante l'applicazione delle patch - Qualsiasi [riduzione delle prestazioni](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error) nell'[dell'integrità del cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction) interromperebbe il processo dell'applicazione di patch. Questo tempo si aggiungerà al tempo necessario per l'applicazione di patch all'intero cluster.

D: **Qual è il motivo per cui vengono visualizzati alcuni aggiornamenti nei risultati di Windows Update ottenuti tramite l'API REST, ma non nella cronologia di Windows Update sul computer?**

R. Alcuni aggiornamenti del prodotto vengono visualizzati solo nella rispettiva cronologia patch/di aggiornamento. Ad esempio gli aggiornamenti di Windows Defender potrebbero essere visualizzati o meno nella cronologia di Windows Update in Windows Server 2016.

D: **È possibile usare Patch Orchestration App per applicare le patch al cluster di sviluppo (cluster con un solo nodo)?**

R. No, non è possibile usare Patch Orchestration App per applicare le patch a un cluster con un solo nodo. Questa limitazione dipende dalla progettazione, perché i [servizi di sistema di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) o le app dei clienti incorreranno in tempi di inattività, pertanto qualsiasi processo di ripristino per l'applicazione di patch non verrebbe mai approvato da Repair Manager.

D: **La modalità di patch di nodi del cluster in Linux**

R. Visualizzare [aggiornamenti automatici di immagine del sistema operativo del set di scalabilità di macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) per orchestrare gli aggiornamenti in linux.

Q.**il motivo per cui ciclo di aggiornamento richiede così lungo?**

R. Eseguire una query per il risultato json, quindi, attraverso la voce del ciclo di aggiornamento per tutti i nodi e quindi, è possibile provare a scoprire il tempo impiegato dall'installazione dell'aggiornamento in ogni nodo usando OperationStartTime e OperationTime(OperationCompletionTime). Se si è verificato lungo intervallo di tempo in mentre non era in corso alcun aggiornamento, è possibile che il cluster è in stato di errore e a causa che l'istruzione repair manager non ha approvato le altre le attività di riparazione. Se l'installazione dell'aggiornamento ha impiegato a lungo in qualsiasi nodo, quindi, è possibile possibile che nodo non è stato aggiornato da molto tempo e numerosi aggiornamenti erano in attesa di installazione, che ha richiesto tempo. È anche possibile che un caso in cui l'applicazione di patch su un nodo è bloccata a causa di un nodo rimanga bloccato nello stato che generalmente si verifica perché la disabilitazione del nodo disabilitato potrebbe causare situazioni di perdita del quorum/data.

D: **Il motivo per cui è necessario disabilitare il nodo quando POA è l'applicazione delle patch?**

R. Patch orchestration application disabilita il nodo con l'intento 'riavvia' di cui viene arrestata/rialloca tutti i servizi di infrastruttura del servizio in esecuzione nel nodo. Questa operazione viene eseguita per garantire che le applicazioni non giungere usando una combinazione di DLL nuovi e precedenti, pertanto si consiglia di non correggere un nodo senza la sua disabilitazione.

## <a name="disclaimers"></a>Dichiarazioni di non responsabilità

- Patch Orchestration App accetta il contratto licenza di Windows Update per conto dell'utente. Facoltativamente, l'impostazione può essere disattivata nella configurazione dell'applicazione.

- Patch Orchestration App raccoglie i dati di telemetria per tenere traccia dell'uso e delle prestazioni. I dati di telemetria dell'applicazione seguono l'impostazione dei dati di telemetria del runtime di Service Fabric (attiva per impostazione predefinita).

## <a name="troubleshooting"></a>risoluzione dei problemi

### <a name="a-node-is-not-coming-back-to-up-state"></a>Un nodo non torna allo stato attivo

**Il nodo potrebbe essere bloccato in uno stato di disattivazione perché**:

Un controllo di sicurezza è in sospeso. Per risolvere questo problema, assicurarsi che siano disponibili sufficienti nodi in uno stato di integrità.

**Il nodo potrebbe essere bloccato in uno stato di disattivazione perché**:

- Il nodo è stato disabilitato manualmente.
- Il nodo è stato disabilitato a causa di un processo dell'infrastruttura di Azure in corso.
- Il nodo è stato disabilitato temporaneamente da Patch Orchestration App per applicare le patch al nodo.

**Il nodo potrebbe essere bloccato in uno stato inattivo perché**:

- Il nodo è stato impostato su uno stato inattivo manualmente.
- Il nodo è in fase di riavvio (che è possibile che venga avviato da Patch Orchestration App).
- Il nodo è inattivo a causa di problemi relativi a una rete o macchina o macchina virtuale difettosa.

### <a name="updates-were-skipped-on-some-nodes"></a>Gli aggiornamenti sono stati ignorati in alcuni nodi

Patch Orchestration App tenta di installare un aggiornamento di Windows in base ai criteri di ripianificazione. Il servizio tenta di recuperare il nodo e di ignorare l'aggiornamento in base ai criteri dell'applicazione.

In questo caso, viene generato un report sull'integrità dei livelli di avviso per Node Agent Service. Il risultato di Windows Update contiene anche la possibile causa dell'errore.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>L'integrità del cluster passa nello stato di errore durante l'installazione dell'aggiornamento

Un aggiornamento difettoso di Windows può compromettere l'integrità di un'applicazione o di un cluster in un nodo particolare o in un dominio di aggiornamento. Patch Orchestration App interrompe qualsiasi successiva operazione di aggiornamento di Windows fino a quando il cluster è di nuovo integro.

Un amministratore deve intervenire e stabilire perché l'applicazione o il cluster è diventato non integro a causa di Windows Update.

## <a name="release-notes"></a>Note sulla versione

>[!NOTE]
> A partire dalla versione 1.4.0, note sulla versione e le versioni sono disponibili nella versione di GitHub [pagina](https://github.com/microsoft/Service-Fabric-POA/releases/).

### <a name="version-110"></a>Versione 1.1.0
- Versione pubblica

### <a name="version-111"></a>Versione 1.1.1
- Correzione di un bug in SetupEntryPoint di NodeAgentService che ha impedito l'installazione di NodeAgentNTService.

### <a name="version-120"></a>Versione 1.2.0

- Correzioni di bug nel flusso di lavoro di riavvio del sistema.
- Correzione di bug nella creazione di attività RM a causa delle quali il controllo dell'integrità durante la preparazione delle attività di ripristino non ha avuto luogo come previsto.
- Modificata la modalità di avvio per il servizio di windows POANodeSvc da automatico ad automatico ritardato.

### <a name="version-121"></a>Versione 1.2.1

- Correzione di bug nel flusso di lavoro di riduzione delle prestazioni del cluster. È stata introdotta la logica di Garbage Collection per le attività di riparazione di Patch Orchestration Application relative a nodi inesistenti.

### <a name="version-122"></a>Versione 1.2.2

- Varie correzioni di bug.
- I file binari sono ora firmati.
- Aggiunta del collegamento a sfpkg per l'applicazione.

### <a name="version-130"></a>Versione 1.3.0

- L'impostazione di InstallWindowsOSOnlyUpdates su false consente di installare tutti gli aggiornamenti disponibili.
- Modifica della logica per disabilitare gli aggiornamenti automatici. Questa correzione risolve un bug che impediva la disabilitazione degli aggiornamenti automatici nei sistemi Server 2016 e versioni successive.
- Vincolo di posizionamento per entrambi i microservizi di POA per casi d'uso avanzata con parametri.

### <a name="version-131"></a>Versione 1.3.1
- Correzione della regressione per cui POA 1.3.0 non funziona in Windows Server 2012 R2 o versione precedente a causa di un errore durante la disabilitazione degli aggiornamenti automatici. 
- Correzione di un bug per cui la configurazione di InstallWindowsOSOnlyUpdates risulta sempre true.
- Modifica del valore predefinito di InstallWindowsOSOnlyUpdates su False.

### <a name="version-132"></a>Versione 1.3.2
- Correzione di un problema che effettua il ciclo di vita dell'applicazione di patch su un nodo nel caso in cui sono presenti nodi con nome che sono subset del nome del nodo corrente. Per tali nodi, è possibile che l'applicazione di patch non sia stata eseguita o il riavvio sia in sospeso. 