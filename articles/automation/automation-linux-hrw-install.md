---
title: Ruolo di lavoro ibrido per runbook Linux di Automazione di Azure
description: Questo articolo fornisce informazioni sull'installazione di un ruolo di lavoro ibrido per runbook di Automazione di Azure che consente di eseguire i runbook sui computer Linux nel data center locale o nell'ambiente cloud.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: bc6c98784195aaf80cb6ca32ef29f75666099b06
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-deploy-a-linux-hybrid-runbook-worker"></a>Come distribuire un ruolo di lavoro ibrido per runbook di Linux

I runbook in Automazione di Azure non possono accedere alle risorse in altri cloud o negli ambienti locali perché vengono eseguiti nel cloud di Azure. La funzionalità Ruolo di lavoro ibrido per runbook di Automazione di Azure consente di eseguire runbook direttamente sul computer che ospita il ruolo e su risorse nell'ambiente per gestire tali risorse locali. I runbook vengono infatti archiviati e gestiti in Automazione di Azure e quindi distribuiti a uno o più computer designati.

Questa funzionalità è illustrata nell'immagine seguente:<br>  

![Panoramica di Hybrid Runbook Workers](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Per una panoramica tecnica sul ruolo di lavoro ibrido per runbook, vedere [Panoramica dell'architettura di Automazione](automation-offering-get-started.md#automation-architecture-overview). Esaminare le informazioni seguenti per quanto riguarda i [requisiti hardware e software](automation-offering-get-started.md#hybrid-runbook-worker) e le [informazioni per preparare la rete](automation-offering-get-started.md#network-planning) prima di iniziare la distribuzione di un ruolo di lavoro ibrido per runbook. Dopo avere distribuito correttamente un ruolo di lavoro per runbook, esaminare [Esecuzione dei runbook per Hybrid Runbook Workers](automation-hrw-run-runbooks.md) per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud.     

## <a name="hybrid-runbook-worker-groups"></a>Gruppi di computer di lavoro runbook ibridi
Ogni computer di lavoro runbook ibrido è un membro di un gruppo di computer di lavoro runbook ibridi che è possibile specificare quando si installa l'agente. Un gruppo può includere un solo agente, ma è possibile installarvi più agenti per garantire una disponibilità elevata.

Quando si avvia un runbook in un ruolo di lavoro ibrido per runbook, è necessario specificare il gruppo in cui verrà eseguito. I membri del gruppo determinano il ruolo di lavoro che gestirà la richiesta. Non è possibile scegliere un computer di lavoro specifico.

## <a name="installing-linux-hybrid-runbook-worker"></a>Installazione del ruolo di lavoro ibrido per runbook di Linux
La procedura per installare e configurare manualmente un ruolo di lavoro ibrido per runbook in un computer Linux è semplice. È necessario abilitare la soluzione **Ruolo di lavoro ibrido per runbook di Automazione** nell'area di lavoro di Log Analytics e quindi eseguire un set di comandi per registrare il computer come ruolo di lavoro e aggiungerlo a un gruppo nuovo o esistente. 

Prima di procedere, è necessario annotare l'area di lavoro di Log Analytics a cui è collegato l'account di Automazione e anche la chiave primaria per l'account di Automazione. È possibile trovare entrambi gli elementi nel portale, selezionando l'account di Automazione, **Area di lavoro** per l'ID dell'area di lavoro, quindi scegliendo **Chiavi** per la chiave primaria.  

1.  Abilitare la soluzione "Ruolo di lavoro ibrido per runbook di Automazione" in Azure. A tale scopo, è possibile:

   1. Aggiungere la soluzione **Ruolo di lavoro ibrido per runbook di Automazione** alla sottoscrizione usando la procedura descritta in [Aggiungere soluzioni di gestione di Log Analytics di Azure all'area di lavoro](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions).
   2. Eseguire il cmdlet seguente:

        ```powershell
         $null = Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

2.  Eseguire il comando seguente modificando i valori per i parametri *-w*, *-k*, *-g* e *-e*. Per il parametro *-g* sostituire il valore con il nome del gruppo del ruolo di lavoro ibrido per runbook che il nuovo ruolo di lavoro ibrido per runbook di Linux deve aggiungere. Se il nome non è già presente nell'account di Automazione, viene eseguito un nuovo ruolo di lavoro ibrido per runbook con lo stesso nome.
    
    ```python
    sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
    ```
3. Dopo il completamento del comando, il pannello Gruppi di ruoli di lavoro ibridi nel portale di Azure mostrerà il nuovo gruppo e il numero di membri oppure, nel caso di un gruppo esistente, il numero di membri verrà incrementato. È possibile selezionare il gruppo nell'elenco nel pannello **Gruppi di ruoli di lavoro ibridi** e selezionare il riquadro **Ruoli di lavoro per runbook**. Nel pannello **Ruoli di lavoro per runbook** sono elencati i membri del gruppo.  


## <a name="turning-off-signature-validation"></a>Disattivazione della convalida della firma 
Per impostazione predefinita, i ruoli di lavoro ibridi per runbook di Linux richiedono la convalida della firma. Se si esegue un runbook non firmato in un ruolo di lavoro, viene visualizzato un errore che contiene "Convalida firma non riuscita". Per disattivare la convalida della firma, eseguire il comando seguente, sostituendo il secondo parametro con l'ID dell'area di lavoro di Log Analytics:

 ```python
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Tipi di runbook supportati

I ruoli di lavoro ibridi per runbook di Linux non supportano il set completo di tipi di runbook disponibili in Automazione di Azure.

I tipi di runbook seguenti possono essere usati in un ruolo di lavoro ibrido di Linux:

* Python 2
* PowerShell
 
I tipi di runbook seguenti non possono essere usati in un ruolo di lavoro ibrido di Linux:

* Flusso di lavoro PowerShell
* Grafico
* Grafico del flusso di lavoro di PowerShell

## <a name="next-steps"></a>Passaggi successivi

* Esaminare [Esecuzione dei runbook per Hybrid Runbook Workers](automation-hrw-run-runbooks.md) per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud.
* Per istruzioni su come rimuovere i ruoli di lavoro ibridi per runbook, vedere [Remove Azure Automation Hybrid Runbook Workers](automation-remove-hrw.md) (Rimuovere i ruoli di lavoro ibridi per runbook di Automazione di Azure)
