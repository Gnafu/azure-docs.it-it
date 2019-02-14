---
title: Ridimensionare verticalmente set di scalabilità di macchine virtuali di Azure | Microsoft Docs
description: Come eseguire la scalabilità verticale di una macchina virtuale in risposta agli avvisi di monitoraggio tramite Automazione di Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 16b17421-6b8f-483e-8a84-26327c44e9d3
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: manayar
ms.openlocfilehash: 1a8bfbe12156156944d4527ebb11fa6f1a1de544
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977236"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Scalabilità verticale automatica con set di scalabilità di macchine virtuali

In questo articolo viene descritto come ridimensionare in verticale i [set di macchine virtuali](https://azure.microsoft.com/services/virtual-machine-scale-sets/) di Azure con o senza un nuovo provisioning. Per la scalabilità verticale delle macchine virtuali non incluse nei set di scalabilità, vedere l'articolo [Scalabilità verticale di macchine virtuali di Azure tramite Automazione di Azure](../virtual-machines/windows/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Per ridimensionamento verticale, detto anche *aumento delle prestazioni* e *riduzione delle prestazioni*, si intende l'aumento o la riduzione delle dimensioni delle macchine virtuali (VM) in risposta a un carico di lavoro. Confrontare questo comportamento con la [scalabilità orizzontale](virtual-machine-scale-sets-autoscale-overview.md), detta anche *aumento delle istanze* e *riduzione delle istanze*, in cui il numero di macchine virtuali viene modificato in base al carico di lavoro.

Per nuovo provisioning si intende la rimozione di una VM esistente e la relativa sostituzione con una nuova. Quando si aumentano o si riducono le dimensioni delle macchine virtuali in un set di scalabilità di macchine virtuali, in alcuni casi può rendersi necessario ridimensionare le macchine virtuali esistenti e mantenere i dati, mentre in altri casi è necessario distribuire nuove macchine virtuali con dimensioni diverse. Questo documento illustra entrambi i casi.

Il ridimensionamento verticale, ovvero l'aumento o la riduzione delle prestazioni, può risultare utile quando:

* Un servizio basato su macchine virtuali è sottoutilizzato, ad esempio nel fine settimana. La riduzione delle dimensioni delle VM può ridurre i costi mensili.
* L'aumento delle dimensioni delle VM consente di soddisfare una maggiore richiesta senza creare altre macchine virtuali.

È possibile configurare l'attivazione della scalabilità verticale in base agli avvisi basati sulle metriche del set di scalabilità di macchine virtuali. Quando viene attivato, l'avviso genera un webhook che attiva un runbook in grado di aumentare o ridurre il set di scalabilità. Il ridimensionamento verticale può essere configurato seguendo questa procedura:

1. Creare un account di Automazione di Azure con funzionalità RunAs.
2. Importare i runbook di scalabilità verticale di Automazione di Azure per i set di scalabilità di macchine virtuali nella sottoscrizione.
3. Aggiungere un webhook al runbook.
4. Aggiungere un avviso al set di scalabilità di macchine virtuali con una notifica di webhook.

> [!NOTE]
> Il ridimensionamento verticale può avvenire solo entro determinati intervalli di dimensioni delle VM. Confrontare le specifiche di ogni dimensione prima di decidere il tipo di ridimensionamento (un numero più alto non sempre indica dimensioni della VM più grandi). È possibile scegliere di applicare il ridimensionamento tra le seguenti coppie di dimensioni:
> 
> | coppie di ridimensionamento di dimensioni delle macchine virtuali |  |
> | --- | --- |
> | Standard_A0 |Standard_A11 |
> | Standard_D1 |Standard_D14 |
> | Standard_DS1 |Standard_DS14 |
> | Standard_D1v2 |Standard_D15v2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> 
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Creare un account di Automazione di Azure con funzionalità RunAs
La prima operazione da eseguire è creare un account di Automazione di Azure che ospiterà i runbook usati per ridimensionare le istanze del set di scalabilità di macchine virtuali. [Automazione di Azure](https://azure.microsoft.com/services/automation/) ha introdotto di recente la funzionalità "Account RunAs", che consente di configurare l'entità servizio per l'esecuzione automatica di runbook per conto dell'utente. Per altre informazioni, vedere:

* [Autenticare runbook con account RunAs di Azure](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importare i runbook di ridimensionamento verticale di Automazione di Azure nella sottoscrizione
I runbook necessari per la scalabilità verticale del set di scalabilità di macchine virtuali sono già stati pubblicati nella raccolta di runbook di Automazione di Azure. Per importarli nella sottoscrizione seguire la procedura descritta in questo articolo:

* [Raccolte di runbook e moduli per l'automazione di Azure](../automation/automation-runbook-gallery.md)

Scegliere l'opzione Esplora raccolta dal menu Runbook:

![Runbook da importare][runbooks]

I runbook da importare sono visualizzati nell'immagine seguente: Selezionare il runbook in base al tipo di ridimensionamento con o senza un nuovo provisioning che si vuole usare:

![Raccolta di runbook][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Aggiungere un webhook al runbook
Dopo aver importato i runbook, aggiungere un webhook al runbook in modo che possa essere attivato da un avviso di un set di scalabilità di macchine virtuali. Informazioni dettagliate sulla creazione di un webhook per il runbook sono disponibili in questo articolo:

* [Webhook di Automazione di Azure](../automation/automation-webhooks.md)

> [!NOTE]
> Assicurarsi di copiare l'URI del webhook prima di chiudere la finestra di dialogo del webhook, perché questo indirizzo sarà necessario nella sezione successiva.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>Aggiungere un avviso al set di scalabilità di macchine virtuali
Di seguito è riportato uno script di PowerShell che mostra come aggiungere un avviso a un set di scalabilità di macchine virtuali. Vedere l'articolo seguente per ottenere il nome della metrica in base alla quale attivare l'avviso: [Metriche comuni per la scalabilità automatica di Monitoraggio di Azure](../azure-monitor/platform/autoscale-common-metrics.md).

```
$actionEmail = New-AzAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [!NOTE]
> È consigliabile configurare un intervallo di tempo ragionevole per l'avviso per evitare di attivare troppo spesso il ridimensionamento verticale e l'eventuale interruzione del servizio associato. Considerare un intervallo minimo di 20-30 minuti. Prendere in considerazione il ridimensionamento orizzontale se è necessario evitare qualsiasi interruzione.
> 
> 

Per altre informazioni su come creare gli avvisi, vedere gli articoli seguenti:

* [Esempi di avvio rapido con PowerShell per Monitoraggio di Azure](../azure-monitor/platform/powershell-quickstart-samples.md)
* [Esempi di avvio rapido dell'interfaccia della riga di comando multipiattaforma per Monitoraggio di Azure](../azure-monitor/platform/cli-samples.md)

## <a name="summary"></a>Summary
Questo articolo ha illustrato semplici esempi di ridimensionamento verticale. Con questi blocchi predefiniti, ovvero account di automazione, runbook, webhook e avvisi, è possibile connettere una vasta gamma di eventi con un set di azioni personalizzato.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
