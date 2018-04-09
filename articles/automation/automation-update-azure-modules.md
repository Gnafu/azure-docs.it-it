---
title: Aggiornare i moduli di Azure in Automazione di Azure
description: L'articolo descrive in che modo è ora possibile aggiornare i moduli di Azure PowerShell comuni disponibili per impostazione predefinita in Automazione di Azure.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: f1f7068de1781d1cc66a412752f6fd99d603a6be
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Come aggiornare i moduli di Azure PowerShell in Automazione di Azure

I moduli di Azure PowerShell più comuni sono forniti per impostazione predefinita in ogni account di Automazione. Il team di Azure aggiorna regolarmente i moduli di Azure. Pertanto, l'account di Automazione prevede una modalità di aggiornamento dei moduli non appena nel portale sono disponibili nuove versioni.  

Poiché i moduli vengono aggiornati regolarmente dal gruppo di prodotto, possono essere eseguite modifiche con i cmdlet inclusi, che potrebbero influire negativamente sui runbook a seconda del tipo di modifica, ad esempio ridenominando un parametro o deprecando completamente un cmdlet. Per evitare conseguenze sui runbook e sui processi che vengono automatizzati, si consiglia di eseguire un test e di convalidare prima di procedere. Se non si dispone di un account di automazione dedicato a questo scopo, è consigliabile crearne uno in modo da poter testare numerosi scenari e permutazioni diverse durante lo sviluppo dei runbook, oltre a poter eseguire modifiche iterative, ad esempio l'aggiornamento dei moduli PowerShell. Dopo aver convalidato i risultati e applicato le modifiche necessarie, procedere coordinando la migrazione di tutti i runbook modificati ed eseguire l'aggiornamento di seguito come descritto nella produzione.

## <a name="updating-azure-modules"></a>Aggiornamento dei moduli di Azure

1. Nella pagina Moduli dell'account di Automazione è presente un'opzione denominata **Aggiorna moduli di Azure**. Tale opzione è sempre abilitata.<br><br> ![Opzione Aggiorna i moduli di Azure nella pagina Moduli](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. Fare clic su **Aggiorna i moduli di Azure**. Viene visualizzata una notifica di conferma che chiede se si desidera continuare.<br><br> ![Notifica di aggiornamento dei moduli di Azure](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Fare clic su **Sì** per avviare il processo di aggiornamento del modulo. Il processo richiede circa 15-20 minuti per aggiornare i seguenti moduli:

  * Azure
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    Se i moduli sono già aggiornati, il processo verrà completato in pochi secondi. Al termine del processo di aggiornamento, si riceverà una notifica.<br><br> ![Stato di aggiornamento dei moduli di Azure](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

> [!NOTE]
> Quando viene eseguito un nuovo processo pianificato, Automazione di Azure usa i moduli più recenti nell'account di automazione.    

Se si usano i cmdlet di questi moduli di Azure PowerShell nei runbook, si potrebbe voler eseguire il processo di aggiornamento all'incirca ogni mese per assicurarsi di disporre dei moduli più recenti.

## <a name="next-steps"></a>Passaggi successivi

* Per maggiori informazioni sui moduli di integrazione e sulla creazione di moduli personalizzati per l'ulteriore integrazione di Automazione con altri sistemi, servizi o soluzioni, vedere [Moduli di integrazione](automation-integration-modules.md).

* Valutare la possibilità di utilizzare l’integrazione del controllo del codice sorgente tramite [GitHub Enterprise](automation-scenario-source-control-integration-with-github-ent.md) o [Visual Studio Team Services](automation-scenario-source-control-integration-with-vsts.md) per gestire e controllare le versioni del portfolio di configurazione e del runbook di automazione.  