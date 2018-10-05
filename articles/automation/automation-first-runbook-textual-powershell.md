---
title: Il primo runbook PowerShell in Automazione di Azure
description: Esercitazione che illustra la creazione, i test e la pubblicazione di un semplice runbook PowerShell.
keywords: Azure PowerShell, esercitazione sugli script di PowerShell, automazione di PowerShell
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8f3185a2c7633ba0cb5a9b266bcddf023d3c36e1
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47166453"
---
# <a name="my-first-powershell-runbook"></a>Il primo runbook PowerShell

> [!div class="op_single_selector"]
> * [Grafico](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Flusso di lavoro PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Questa esercitazione illustra la creazione di un [runbook PowerShell](automation-runbook-types.md#powershell-runbooks) in Automazione di Azure. Si inizia con un runbook semplice che viene testato e pubblicato, quindi viene illustrato come tenere traccia dello stato del processo del runbook. Si modifica quindi il runbook per gestire effettivamente le risorse di Azure, avviando in questo caso una macchina virtuale di Azure. Si rende infine il runbook più affidabile aggiungendo i relativi parametri.

## <a name="prerequisites"></a>Prerequisiti
Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](automation-offering-get-started.md) che conterrà il runbook ed eseguirà l'autenticazione con le risorse di Azure. Questo account deve avere l'autorizzazione per avviare e arrestare la macchina virtuale.
* Macchina virtuale di Azure. La macchina virtuale viene arrestata e avviata, quindi non deve essere una macchina virtuale di produzione.

## <a name="step-1---create-new-runbook"></a>Passaggio 1: Creare nuovo runbook
Si inizia creando un runbook semplice che restituisce il testo *Hello World*.

1. Nel portale di Azure aprire l'account di automazione.

   La pagina dell'account di automazione offre una visualizzazione rapida delle risorse di questo account. Dovrebbero essere già disponibili alcuni asset. Per la maggior parte sono i moduli inclusi automaticamente in un nuovo account di automazione. Dovrebbe essere disponibile anche l'asset credenziali citato nei [prerequisiti](#prerequisites).

1. Fare clic su **Runbook** in **Automazione processi** per aprire l'elenco dei runbook.
2. Creare un nuovo runbook facendo clic sul pulsante **+ Aggiungi runbook** e quindi su **Crea un nuovo runbook**.
3. Denominare il runbook *MyFirstRunbook-PowerShell*.
4. In questo caso si sta creando un [runbook PowerShell](automation-runbook-types.md#powershell-runbooks), quindi selezionare **Powershell** per **Tipo di runbook**.
5. Fare clic su **Crea** per creare il runbook e aprire l'editor di testo.

## <a name="step-2---add-code-to-the-runbook"></a>Passaggio 2: - aggiungere un codice al runbook
È possibile digitare il codice direttamente nel runbook, oppure è possibile selezionare i cmdlet, i runbook e le risorse dal controllo della libreria e aggiungerle al runbook con tutti i parametri correlati. Per questa procedura dettagliata si digiterà direttamente nel runbook.

1. Il runbook è attualmente vuoto; digitare *Write-Output "Hello World."* nel corpo dello script.<br><br> ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  
2. Salvare il runbook facendo clic su **Salva**.

## <a name="step-3---test-the-runbook"></a>Passaggio 3: Testare il runbook
Prima di pubblicare il runbook per renderlo disponibile nell'ambiente di produzione, occorre testarlo per verificare che funzioni correttamente. Quando si testa un runbook, è necessario eseguire la versione **Bozza** e visualizzarne l'output in modo interattivo.

1. Fare clic su **Pannello di test** per aprire il pannello di test.
2. Fare clic su **Avvia** per avviare il test. Questa deve essere l'unica opzione abilitata.
3. Viene creato un [processo del runbook](automation-runbook-execution.md) e il relativo stato viene visualizzato.

   Lo stato del processo verrà avviato come *In coda* per indicare che è in attesa della disponibilità di un ruolo di lavoro per runbook nel cloud. Lo stato passa ad *Avvio in corso* quando un ruolo di lavoro richiede il processo e quindi a *In esecuzione* quando l'esecuzione del runbook viene effettivamente avviata.  

1. Al termine del processo del runbook, viene visualizzato l'output. In questo caso dovrebbe essere visualizzato *Hello World*.<br><br> ![Output del Riquadro test](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  
2. Chiudere il riquadro di test per tornare all'area di disegno.

## <a name="step-4---publish-and-start-the-runbook"></a>Passaggio 4: Pubblicare e avviare il runbook
Il runbook creato è ancora in modalità bozza. È necessario pubblicarlo prima di poterlo eseguire in produzione.  Quando si pubblica un runbook, è possibile sovrascrivere la versione pubblicata esistente con la versione bozza. In questo caso non esiste ancora una versione pubblicata perché il runbook è appena stato creato.

1. Fare clic su **Pubblica** per pubblicare il runbook, quindi su **Sì** quando richiesto.
2. Se si scorre verso sinistra per visualizzare il runbook nella pagina **Runbook**, come **Stato di creazione** viene visualizzato **Pubblicato**.
3. Scorrere verso destra per visualizzare il riquadro **MyFirstRunbook-PowerShell**.  
   Le opzioni nella parte superiore consentono di avviare il runbook, visualizzarlo, pianificarlo per l'avvio in un momento successivo o creare un [webhook](automation-webhooks.md) per poterlo avviare con una chiamata HTTP.
4. Per avviare il runbook, fare clic su **Avvia** e quindi su **OK** quando si apre la pagina Avvia runbook.
5. Verrà aperta una pagina per il processo del runbook creato. È possibile chiudere questo riquadro, ma in questo caso lo si lascerà aperto per poter controllare lo stato del processo.
6. Lo stato del processo è visualizzato in **Riepilogo processi** e corrisponde agli stati osservati quando è stato testato il runbook.<br><br> ![Riepilogo dei processi](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)<br>  
7. Quando lo stato del runbook è *Completato*, in **Overview** fare clic su **Output**. Viene aperto il riquadro Output dove si può vedere il testo *Hello World*.<br><br> ![Output processo](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)<br> 
8. Chiudere la pagina Output.
9. Fare clic su **Tutti i log** per aprire il riquadro Flussi relativo al processo del runbook. Nel flusso di output dovrebbe essere visibile solo *Hello World*, ma potrebbero essere visualizzati altri flussi per un processo del runbook, ad esempio Verbose ed Error se il runbook scrive in questi flussi.<br><br> ![Tutti i log](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)<br>   
10. Chiudere la finestra Flussi e la pagina dei processi per tornare alla pagina MyFirstRunbook-PowerShell.
11. In **Dettagli** fare clic su **Processi** per aprire la pagina dei processi per questo runbook. Sono elencati tutti i processi creati da questo runbook. Dovrebbe essere elencato un solo processo, perché il processo è stato eseguito una sola volta.<br><br> ![Elenco processi](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)  
12. È possibile fare clic su questo processo per aprire lo stesso pannello visualizzato quando è stato avviato il runbook. In questo modo è possibile tornare indietro nel tempo e visualizzare i dettagli di tutti i processi creati per un runbook particolare.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Passaggio 5: Aggiungere l'autenticazione per gestire le risorse di Azure
Il runbook è stato testato e pubblicato, ma finora non esegue alcuna attività utile. Si vuole fare in modo che gestisca le risorse di Azure. Sarà tuttavia in grado di eseguire questa operazione solo dopo aver fatto in modo che esegua l'autenticazione con le credenziali indicate nei [prerequisiti](#prerequisites). A questo scopo si userà il cmdlet **Connect-AzureRmAccount**. Se si gestiscono risorse in più sottoscrizioni, è necessario usare il parametro **-AzureRmContext** con [Get-AzureRmContext](/powershell/module/azurerm.profile/get-azurermcontext).

   ```powershell
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
-ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $AzureContext
   ```

1. Aprire l'editor di testo facendo clic su **Modifica** nella pagina MyFirstRunbook-PowerShell.
2. Non è più necessaria la riga **Write-Output**, quindi andare avanti ed eliminarla.
3. Digitare o copiare e incollare il codice seguente che gestisce l'autenticazione con l'account RunAs di Automazione:

   ```powershell
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```

   > [!IMPORTANT]
   > **Add-AzureRmAccount** e **Login-AzureRmAccount** sono ora alias per **Connect-AzureRMAccount**. Se il cmdlet **Connect-AzureRMAccount**  non esiste, è possibile usare **Add-AzureRmAccount**  o **Login-AzureRmAccount** oppure aggiornare i moduli nell'account di Automazione alle versioni più recenti.

4. Fare clic su **Riquadro di test** in modo da testare il runbook.
5. Fare clic su **Avvia** per avviare il test. Al termine verrà visualizzato un output simile al seguente, con le informazioni di base sull'account. Questo conferma che la credenziale è valida.<br><br> ![Autentica](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Passaggio 6 - aggiungere il codice per avviare una macchina virtuale
Ora che il runbook esegue l'autenticazione per la sottoscrizione di Azure è possibile gestire le risorse. Si aggiunge un comando per avviare una macchina virtuale. È possibile selezionare una macchina virtuale qualsiasi nella sottoscrizione di Azure. Per ora il nome sarà hardcoded nel runbook.

1. Dopo *Connect-AzureRmAccount* digitare *Start-AzureRmVM -Name 'NomeVM' -ResourceGroupName 'NomeGruppoDiRisorse'* specificando il nome e il nome del gruppo di risorse della macchina virtuale da avviare.  
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```
   <br>
2. Salvare il runbook e poi fare clic su **Riquadro di test** in modo da poterne eseguire il test.
3. Fare clic su **Avvia** per avviare il test. Dopo aver completato l'attività, controllare che la macchina virtuale sia stata avviata.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Passaggio 7: Aggiungere un parametro di input al runbook
Ora il runbook avvia la macchina virtuale specificata nel runbook, ma sarebbe più utile se si potesse specificare la macchina virtuale quando si avvia il runbook. Per fornire questa funzionalità, si aggiungeranno dei parametri di input al runbook.

1. Aggiungere parametri per *VMName* e *ResourceGroupName* al runbook e usare queste variabili con il cmdlet **Start-AzureRmVM** come nell'esempio seguente.

   ```
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```
   <br>
1. Salvare il runbook e aprire il riquadro Test. È ora possibile fornire i valori per le due variabili di input usate nel test.
2. Chiudere il riquadro Test.
3. Fare clic su **Pubblica** per pubblicare la nuova versione del runbook.
4. Arrestare la macchina virtuale avviata nel passaggio precedente.
5. Fare clic su **OK** per avviare il runbook. Digitare **VMName** e **ResourceGroupName** per la macchina virtuale da avviare.<br><br> ![Passare i parametri](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>  
6. Quando il runbook viene completato, controllare che la macchina virtuale sia stata avviata.

## <a name="differences-from-powershell-workflow"></a>Differenze rispetto al flusso di lavoro PowerShell
I runbook PowerShell hanno lo stesso ciclo di vita, le stesse funzionalità e la stessa modalità di gestione dei runbook Flusso di lavoro PowerShell, ma con alcune differenze e limitazioni:

1. L'esecuzione dei runbook PowerShell è più rapida rispetto ai runbook Flusso di lavoro PowerShell, perché non prevedono un passaggio di compilazione.
2. Grazie al supporto dei checkpoint, i runbook Flusso di lavoro PowerShell possono riprendere da qualsiasi punto del runbook, mentre i runbook PowerShell possono riprendere solo dall'inizio.
3. I runbook Flusso di lavoro PowerShell supportano l'esecuzione seriale e parallela, mentre i runbook PowerShell possono eseguire i comandi solo in modo seriale.
4. In un runbook Flusso di lavoro PowerShell un'attività, un comando o un blocco di script può avere un proprio spazio di esecuzione, mentre in un runbook PowerShell tutti gli elementi in uno script vengono eseguiti in un singolo spazio di esecuzione. Esistono anche [differenze di sintassi](https://technet.microsoft.com/magazine/dn151046.aspx) tra un runbook PowerShell nativo e un runbook Flusso di lavoro PowerShell.

## <a name="next-steps"></a>Passaggi successivi
* Per iniziare a usare runbook grafici, vedere [Il primo runbook grafico](automation-first-runbook-graphical.md)
* Per iniziare a usare runbook del flusso di lavoro PowerShell, vedere [Il primo runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md)
* Per altre informazioni sui tipi di runbook, i relativi vantaggi e le limitazioni, vedere [Tipi di runbook di Automazione di Azure](automation-runbook-types.md)
* Per altre informazioni sulla funzionalità di supporto degli script PowerShell, vedere il blog relativo al [supporto di script PowerShell nativi in Automazione di Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)

