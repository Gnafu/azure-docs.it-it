---
title: Automatizzare la rimozione di gruppi di risorse con Automazione di Azure
description: Versione del flusso di lavoro PowerShell di uno scenario di Automazione di Azure che include runbook per rimuovere tutti i gruppi di risorse nella sottoscrizione.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f0ec42a1e62c4aa35bb6bdffce0acf00a971cb7d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194520"
---
# <a name="azure-automation-scenario---automate-removal-of-resource-groups"></a>Scenario di Automazione di Azure: automatizzare la rimozione di gruppi di risorse
Molti clienti creano più gruppi di risorse. Alcuni potrebbero essere usati per gestire applicazioni di produzione e altri come ambienti di sviluppo, test e staging. La rimozione di un gruppo di risorse con un clic è più complessa rispetto all'automazione della distribuzione di queste risorse. È possibile semplificare questa comune attività di gestione con Automazione di Azure. Ciò è utile se si usa una sottoscrizione di Azure con un limite di spesa tramite un'offerta riservata ai membri, ad esempio di MSDN o del programma Microsoft Partner Network Cloud Essentials.

Questo scenario si basa su un runbook di PowerShell ed è progettato per rimuovere uno o più gruppi di risorse specificati dalla sottoscrizione. L'impostazione predefinita del runbook prevede l'esecuzione preliminare di un test, per evitare di eliminare per errore il gruppo di risorse prima di essere pronti a completare la procedura.   

## <a name="getting-the-scenario"></a>Come ottenere lo scenario
Questo scenario è costituito da un runbook di PowerShell scaricabile da [PowerShell Gallery](https://www.powershellgallery.com/packages/Remove-ResourceGroup/1.0/DisplayScript). Può anche essere importato direttamente dalla [raccolta di runbook](automation-runbook-gallery.md) nel portale di Azure.<br><br>

| Runbook | DESCRIZIONE |
| --- | --- |
| Remove-ResourceGroup |Rimuove uno o più gruppi di risorse di Azure e le risorse associate dalla sottoscrizione. |

<br>
Per questo runbook vengono definiti i parametri di input seguenti:

| Parametro | DESCRIZIONE |
| --- | --- |
| NameFilter (obbligatorio) |Specifica un filtro per i nomi per limitare i gruppi di risorse che si intende eliminare. È possibile passare più valori usando un elenco delimitato da virgole.<br>Il filtro non rileva la distinzione tra maiuscole e minuscole e troverà la corrispondenza con tutti i gruppi di risorse contenenti la stringa. |
| PreviewMode (facoltativo) |Esegue il runbook per verificare quali gruppi di risorse verrebbero eliminati, ma non esegue alcuna azione.<br>Il valore predefinito è **true** che consente di evitare l'eliminazione accidentale di uno o più gruppi di risorse passati al runbook. |

## <a name="install-and-configure-this-scenario"></a>Installare e configurare lo scenario
### <a name="prerequisites"></a>prerequisiti
Questo runbook esegue l'autenticazione con l' [account RunAs di Azure](automation-sec-configure-azure-runas-account.md).    

### <a name="install-and-publish-the-runbooks"></a>Installare e pubblicare i runbook
Dopo aver scaricato il runbook, è possibile importarlo usando la procedura descritta tra quelle di [importazione di un runbook](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-azure-automation). Pubblicare il runbook dopo averlo importato correttamente nell'account di automazione.

## <a name="using-the-runbook"></a>Uso del runbook
I passaggi seguenti illustrano l'esecuzione di questo runbook e consentono di acquisire familiarità con il suo funzionamento. In questo esempio verrà semplicemente testato il runbook, il gruppo di risorse non verrà realmente eliminato.  

1. Dal portale di Azure aprire l'account di automazione e fare clic su **Runbook**.
2. Selezionare il runbook **Remove-ResourceGroup** e fare clic su **Avvia**.
3. Quando si avvia il runbook, viene aperta la pagina **Avvia runbook**, dove è possibile configurare i parametri. Immettere i nomi dei gruppi di risorse della sottoscrizione che è possibile usare per il test e la cui eventuale eliminazione accidentale non causerebbe problemi.

   > [!NOTE]
   > Verificare che il parametro **Previewmode** sia impostato su **true** per evitare di eliminare i gruppi di risorse selezionati. Questo runbook non rimuoverà il gruppo di risorse contenente l'account di automazione che esegue il runbook stesso.  
   >
   >
1. Dopo avere configurato i valori di tutti i parametri, fare clic su **OK**. Il runbook verrà inserito in coda per l'esecuzione.  

Per visualizzare i dettagli del processo del runbook **Remove-ResourceGroup** nel portale di Azure, in **Risorsa** selezionare **Processi** nel runbook. Selezionare il processo da visualizzare. Nel riepilogo del processo saranno visualizzati i parametri di input e il flusso di output, oltre alle informazioni generali sul processo e alle eventuali eccezioni che si sono verificate.<br> ![Stato del processo del runbook Remove-ResourceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-status.png)

Il **Riepilogo processi** include i messaggi dai flussi di output, di avviso e di errore. Selezionare **Output** per visualizzare i risultati dettagliati dell'esecuzione del runbook.<br> ![Risultati di output del runbook Remove-ResourceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-output.png)

## <a name="next-steps"></a>Passaggi successivi
* Per iniziare a creare il runbook, vedere [Creazione o importazione di un runbook in Automazione di Azure](automation-creating-importing-runbook.md).
* Per iniziare a usare i runbook del flusso di lavoro PowerShell, vedere [Il primo runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md).
