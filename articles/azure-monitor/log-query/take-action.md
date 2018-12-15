---
title: Operazioni su un runbook di automazione di Azure avviato dall'utente in Log Analytics | Microsoft Docs
description: Questo articolo descrive come eseguire un runbook di automazione on demand dai risultati della ricerca di Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: 8730070e1b235324a53ad81957339f4ef17db6dc
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53193696"
---
# <a name="take-action-with-an-automation-runbook-from-a-log-analytics-log-search-result"></a>Eseguire operazioni con un runbook di automazione dai risultati della ricerca log di Log Analytics

Dai risultati della ricerca log di Azure Log Analytics è ora possibile selezionare **Take action** (Intervieni) per eseguire un runbook di automazione.  Il runbook consente di risolvere il problema o eseguire altre operazioni, come raccogliere informazioni sulla risoluzione del problema, inviare un messaggio di posta elettronica o creare una richiesta di servizio. 

## <a name="components-and-features-used"></a>Componenti e le funzionalità usati
* [Account di automazione di Azure](../../automation/automation-quickstart-create-account.md)
* [Area di lavoro di Log Analytics](../../azure-monitor/log-query/log-query-overview.md)

## <a name="to-initiate-runbook-from-log-search"></a>Per avviare il runbook dalla ricerca log

Per intervenire su un evento e avviare un runbook dai risultati della ricerca log, è necessario prima creare una ricerca log, dai cui risultati è possibile richiamare un runbook on demand.  A questo scopo, è possibile usare la funzionalità di ricerca log disponibile nel [portale di Azure](../../azure-monitor/log-query/log-query-overview.md).  In questo esempio si eseguirà una ricerca log dal portale di Azure con una dimostrazione di base di questa funzionalità.

1. Nel portale di Azure fare clic su **Tutti i servizi** e selezionare **Log Analytics**.  
2. Selezionare l'area di lavoro di Log Analytics.
3. Nell'area di lavoro selezionare **Ricerca log**.  
4. Nella pagina Ricerca log eseguire una ricerca log.  
5. Nei risultati della ricerca log fare clic sui puntini di sospensione a sinistra di uno dei campi disponibili e nella finestra popup visualizzata selezionare **Take action on** (Intervieni su).<br><br> ![Selezione dell'azione di intervento dai risultati della ricerca](./media/take-action/log-search-takeaction-menuoption.png) 
6. Selezionare **Esegui un runbook** e quindi un runbook da eseguire.  È possibile selezionare qualsiasi runbook disponibile nell'account di automazione associato all'area di lavoro Log Analytics.  Tenere presente quanto segue:

    * I runbook sono organizzati per tag
    * I valori dei parametri di input dei runbook possono essere selezionati direttamente dai campi dei risultati della ricerca.  Viene visualizzato un elenco a discesa con tutti i campi disponibili nella pagina dei risultati tra cui scegliere.  
    * È possibile scegliere di eseguire il runbook su un [ruolo di lavoro ibrido per runbook](../../automation/automation-hybrid-runbook-worker.md) installato nel computer in cui si è verificato il problema, se si dispone di un gruppo di ruoli di lavoro ibridi corrispondente in cui sia contenuto solo questo computer come membro.  Se il nome del gruppo di ruoli di lavoro ibridi corrisponde al nome del computer presente nei risultati della ricerca log, il gruppo viene selezionato automaticamente.    

6. Dopo aver fatto clic su **Esegui** viene visualizzata la pagina del processo di runbook, in cui è possibile esaminare lo stato del processo.   

Se si seleziona un runbook configurato per essere [chiamato da un avviso di Log Analytics](../../automation/automation-create-alert-triggered-runbook.md), presenta un parametro di input denominato **WebhookData** di tipo **Oggetto**.  Se il parametro di input è obbligatorio, è necessario passare i risultati della ricerca al runbook, in modo che possa convertire la stringa in formato JSON in un tipo di oggetto che consenta di filtrare in base a elementi specifici a cui si farà riferimento nelle attività del runbook.  A questo scopo, è necessario scegliere **Search result (Object)** (Risultati ricerca (Oggetto)) dall'elenco a discesa.<br><br> ![Selezionare l'oggetto dati Webhook per il parametro del runbook](media/take-action/select-runbook-and-properties.png)   
    
## <a name="next-steps"></a>Passaggi successivi

* Vedere il [riferimento alla ricerca nei log di Log Analytics](../../azure-monitor/log-query/log-query-overview.md) per visualizzare tutti i campi di ricerca e i facet disponibili in Log Analytics.
* Per informazioni su come richiamare automaticamente un runbook di Automazione, vedere [Chiamare un runbook di Automazione di Azure da un avviso di Log Analytics](../../automation/automation-create-alert-triggered-runbook.md).  
