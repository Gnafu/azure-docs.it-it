---
title: Eseguire runbook nel ruolo di lavoro ibrido per runbook di Automazione di Azure
description: Questo articolo fornisce informazioni sull'esecuzione di runbook su computer presenti nel data center locale o in un provider di servizi cloud con il ruolo di lavoro ibrido per runbook.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 75fc5336458670f4904584b3349f4fac7dc4ca68
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2018
ms.locfileid: "34054122"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Esecuzione di runbook in un ruolo di lavoro ibrido per runbook

Non esiste alcuna differenza nella struttura dei runbook che vengono eseguiti in Automazione di Azure e di quelli eseguiti in Hybrid Runbook Workers. I runbook usati nell'uno o nell'altro caso saranno tuttavia molto diversi perché, mentre i runbook per un ruolo di lavoro ibrido per runbook gestiscono solitamente le risorse nel computer locale o all'interno di risorse nell'ambiente locale in cui sono eseguiti, i runbook in Automazione di Azure gestiscono solitamente le risorse nel cloud Azure.

Quando si creano runbook da eseguire in un ruolo di lavoro ibrido per runbook, è opportuno modificare e testare i runbook all'interno del computer che ospita il ruolo di lavoro ibrido. Il computer host include tutti i moduli di PowerShell e i diritti di accesso di rete necessari per gestire e accedere alle risorse locali. Dopo che un runbook è stato modificato e testato nel computer con il ruolo di lavoro ibrido, è possibile caricarlo nell'ambiente Automazione di Azure in cui risulterà disponibile per essere eseguito nel ruolo di lavoro ibrido. È importante sapere che se i processi vengono eseguiti con l'account di sistema locale per Windows o per un account utente speciale **nxautomation** su Linux, è possibile che emergano alcune piccole differenze, aspetto di cui è importante tenere conto quando si creano runbook per il ruolo di lavoro ibrido per runbook.

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Avvio di un runbook in un ruolo di lavoro ibrido per runbook

[avvio di un runbook in Automazione di Azure](automation-starting-a-runbook.md) illustra diversi modi in cui è possibile eseguire l'avvio dei runbook. Hybrid Runbook Workers aggiunge un'opzione **RunOn** in cui è possibile specificare il nome di un gruppo di computer di lavoro runbook ibridi. Se si specifica un gruppo, il runbook verrà recuperato ed eseguito da uno dei computer di lavoro inclusi in tale gruppo. Se non si specifica l'opzione, verrà eseguito come di consueto in Automazione di Azure.

Quando si avvia un runbook nel portale di Azure, viene visualizzata l'opzione **Esegui in**, che consente di scegliere tra **Azure** o **Ruolo di lavoro ibrido**. Se si seleziona **Computer di lavoro ibrido**, sarà quindi possibile selezionare il gruppo da un elenco a discesa.

Usare il parametro **RunOn**. È possibile usare il comando seguente per avviare un runbook denominato Test-Runbook in un gruppo di ruoli di lavoro ibridi per runbook denominato MyHybridGroup usando Windows PowerShell.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> Il parametro **RunOn** è stato aggiunto al cmdlet **Start-AzureAutomationRunbook** nella versione 0.9.1 di Microsoft Azure PowerShell. È consigliabile [scaricare la versione più recente](https://azure.microsoft.com/downloads/) se la versione installata è precedente. È sufficiente installare questa versione nella workstation in cui si avvierà il runbook da PowerShell. Non è necessario installarla nel computer di lavoro, a meno che non si intenda avviare i runbook da tale computer.

## <a name="runbook-permissions"></a>Autorizzazioni per i runbook

I runbook eseguiti in un ruolo di lavoro ibrido per runbook non possono usare lo stesso metodo in genere usato per l'autenticazione dei runbook per le risorse di Azure, perché accedono a risorse esterne ad Azure. Il runbook può fornire la propria autenticazione alle risorse locali oppure è possibile specificare un account RunAs per fornire un contesto utente per tutti i runbook.

### <a name="runbook-authentication"></a>Autenticazione dei runbook

Per impostazione predefinita, i runbook vengono eseguiti nel contesto dell'account di sistema locale per Windows e di un account utente speciale **nxautomation** per Linux nel computer locale e devono quindi autenticarsi per le risorse a cui accedono.

Nel proprio runbook è possibile usare asset di tipo [Credenziali](automation-credentials.md) e [Certificato](automation-certificates.md) con cmdlet che consentono di specificare le credenziali per poter eseguire l'autenticazione per risorse diverse. L'esempio seguente illustra una parte di un runbook che riavvia un computer. Recupera le credenziali da un asset di tipo credenziale e il nome del computer da un asset di tipo variabile e quindi usa questi valori con il cmdlet Restart-Computer.

```azurepowershell-interactive
$Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
$Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

È anche possibile usare [InlineScript](automation-powershell-workflow.md#inlinescript), che consente di eseguire blocchi di codice in un altro computer con le credenziali specificate dal [parametro comune PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="runas-account"></a>Account RunAs

Per impostazione predefinita il ruolo di lavoro ibrido per runbook usa Sistema locale per Windows e un account utente speciale **nxautomation** per Linux eseguire i runbook. Per evitare che i runbook debbano autenticarsi per le risorse locali, è possibile specificare un account **RunAs** per un gruppo di ruoli di lavoro ibridi. Specificare un [asset credenziali](automation-credentials.md) con accesso alle risorse locali. Tutti i runbook useranno queste credenziali durante l'esecuzione in un ruolo di lavoro ibrido per runbook nel gruppo.

Il nome utente per le credenziali deve essere in uno dei formati seguenti:

* dominio\nome utente
* username@domain
* nome utente (per gli account locali nel computer locale)

Usare la procedura seguente per specificare un account RunAs per un gruppo di lavoro ibrido:

1. Creare un [asset credenziali](automation-credentials.md) con accesso alle risorse locali.
2. Nel portale di Azure aprire l'account di automazione.
3. Selezionare il riquadro **Gruppi di ruoli di lavoro ibridi** e quindi il gruppo.
4. Selezionare **Tutte le impostazioni** e quindi **Impostazioni del gruppo di lavoro ibrido**.
5. Modificare **Esegui come** da **Predefinito** a **Personalizzato**.
6. Selezionare le credenziali e fare clic su **Salva**.

### <a name="automation-run-as-account"></a>Account RunAs di Automazione

Nell'ambito del processo di compilazione automatizzato per la distribuzione di risorse in Azure, è possibile che sia necessario accedere a sistemi locali per supportare un'attività o un set di passaggi nella sequenza di distribuzione. Per supportare l'autenticazione in Azure con l'account RunAs, è necessario installare il certificato dell'account RunAs.

Il runbook di PowerShell seguente, *Export-RunAsCertificateToHybridWorker*, esporta il certificato RunAs dall'account di automazione di Azure e lo scarica e lo importa nell'archivio certificati del computer locale in un ruolo di lavoro ibrido connesso allo stesso account. Una volta completato questo passaggio, viene verificato che il ruolo di lavoro possa eseguire l'autenticazione in Azure usando l'account RunAs.

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
Run this runbook in the hybrid worker where you want the certificate installed.
This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
AUTHOR: Azure Automation Team
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test that authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm Azure Resource Manager calls are working
Get-AzureRmAutomationAccount | Select-Object AutomationAccountName
```

Salvare il runbook *Export-RunAsCertificateToHybridWorker* nel computer con un'estensione `.ps1`. Importarlo nell'account di Automazione e modificare il runbook, cambiando il valore della variabile `$Password` con quello della propria password. Pubblicare e quindi eseguire il runbook scegliendo come destinazione il gruppo di ruoli di lavoro ibridi che esegue e autentica i runbook usando l'account RunAs. Il flusso di processo segnala il tentativo di importare il certificato nell'archivio del computer locale e visualizza più righe a seconda del numero di account di Automazione definiti nella sottoscrizione e del fatto che l'autenticazione abbia o meno esito positivo.

## <a name="job-behavior"></a>Comportamento dei processi

Nei ruoli di lavoro ibridi per runbook i processi vengono gestiti in modo leggermente diverso rispetto a come vengono eseguiti nelle sandbox di Azure. Nei ruoli di lavoro ibridi per runbook, ad esempio, non è previsto alcun limite per la durata del processo. Se si ha un runbook a esecuzione prolungata, è possibile che si voglia che sia resiliente a un possibile riavvio, ad esempio al riavvio del computer che ospita il ruolo di lavoro ibrido. In caso di riavvio del computer host con il ruolo di lavoro ibrido, qualsiasi processo di runbook in esecuzione viene riavviato dall'inizio o dall'ultimo checkpoint per i runbook del flusso di lavoro di PowerShell. Se un processo di runbook viene riavviato più di 3 volte, viene sospeso.

## <a name="troubleshooting-runbooks-on-hybrid-runbook-worker"></a>Risoluzione dei problemi relativi ai runbook nel ruolo di lavoro ibrido per runbook

I log vengono archiviati localmente in ogni ruolo di lavoro ibrido in C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Il ruolo di lavoro ibrido registra anche errori ed eventi nel registro eventi di Windows disponibile in **Registri applicazioni e servizi\Microsoft-SMA\Operational**. Gli eventi correlati ai runbook eseguiti nel ruolo di lavoro vengono scritti in **Registri applicazioni e servizi\Microsoft-Automation\Operational**. Il registro **Microsoft-SMA** include molti più eventi relativi al processo del runbook inviato al ruolo di lavoro e all'elaborazione del runbook. Sebbene il registro eventi **Microsoft-Automation** non contenga molti eventi con informazioni utili alla risoluzione di problemi relativi all'esecuzione del runbook, contiene i risultati del processo del runbook.

[output e i messaggi di runbook](automation-runbook-output-and-messages.md) vengono inviati ad Automazione di Azure da ruoli di lavoro ibridi nello stesso modo in cui vengono eseguiti i processi per runbook nel cloud. È anche possibile abilitare i flussi Verbose e Progress come per qualsiasi altro runbook.

Se i runbook non vengono completati correttamente e il riepilogo del processo visualizza lo stato **Sospeso**, vedere l'articolo sulla risoluzione dei problemi [Ruolo di lavoro ibrido per runbook: un processo runbook termina con lo stato Sospeso](automation-troubleshooting-hybrid-runbook-worker.md#a-runbook-job-terminates-with-a-status-of-suspended).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui vari modi per avviare un runbook, vedere [Avvio di un runbook in Automazione di Azure](automation-starting-a-runbook.md).
* Per comprendere le diverse procedure per l'uso di PowerShell e dei runbook del flusso di lavoro di PowerShell in Automazione di Azure con l'editor di testo, vedere [Modifica di runbook testuali in Automazione di Azure](automation-edit-textual-runbook.md)
