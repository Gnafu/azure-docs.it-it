---
title: Integrazione di Monitoraggio di Azure per le macchine virtuali con System Center Operations Manager | Microsoft Docs
description: Monitoraggio di Azure per le macchine virtuali individua automaticamente i componenti delle applicazioni nei sistemi Windows e Linux ed esegue il mapping delle comunicazioni tra i servizi. Questo articolo illustra l'uso della funzionalità mappa per creare automaticamente diagrammi applicazioni distribuite in Operations Manager.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: e8614a5a-9cf8-4c81-8931-896d358ad2cb
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: b16505eb2c12819532b8675472cf0e6f4177f7bf
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489711"
---
# <a name="system-center-operations-manager-integration-with-azure-monitor-for-vms-map-feature"></a>Integrazione di System Center Operations Manager con la funzionalità mappa Monitoraggio di Azure per le macchine virtuali

In Monitoraggio di Azure per le macchine virtuali, è possibile visualizzare i componenti dell'applicazione individuati in macchine virtuali Windows e Linux in esecuzione in Azure o nell'ambiente in uso. Grazie a questa integrazione tra la funzionalità map e System Center Operations Manager, è possibile creare automaticamente diagrammi applicazioni distribuite in Operations Manager basati sulle mappe delle dipendenze dinamiche in Monitoraggio di Azure per le macchine virtuali. 

>[!NOTE]
>Se è già stata eseguita la distribuzione di Mapping dei servizi, è possibile visualizzare le mappe in Monitoraggio di Azure per le macchine virtuali, che include funzionalità aggiuntive per monitorare l'integrità e le prestazioni delle macchine virtuali. La funzionalità di mapping di Monitoraggio di Azure per le macchine virtuali è progettata per sostituire la soluzione Mapping dei servizi autonoma. Per altre informazioni, vedere [Descrizione di Monitoraggio di Azure per le macchine virtuali](vminsights-overview.md).

## <a name="prerequisites"></a>Prerequisiti

* Un gruppo di gestione di System Center Operations Manager (2012 R2 o versione successiva).
* Area di lavoro Log Analytics configurata per supportare Monitoraggio di Azure per le macchine virtuali.
* Una o più macchine virtuali Windows e Linux o computer fisici monitorati da Operations Manager e che inviano dati all'area di lavoro di Log Analytics. I server Linux che inviano report a un gruppo di gestione di Operations Manager devono essere configurati per la connessione diretta a monitoraggio di Azure. Per altre informazioni, vedere la panoramica in [raccogliere dati di log con l'agente di log Analytics](../platform/log-analytics-agent.md).
* Un'entità servizio con accesso alla sottoscrizione di Azure associata all'area di lavoro Log Analytics. Per altre informazioni, vedere [Creare un'entità servizio](#create-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Installare il management pack di Elenco dei servizi

Per abilitare l'integrazione tra Operations Manager e la funzionalità di mapping, è possibile importare il bundle Microsoft. SystemCenter. ServiceMap Management Pack (Microsoft. SystemCenter. ServiceMap. MPB). È possibile scaricare il bundle del Management Pack da [Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=55763). Il bundle contiene i management pack seguenti:

* Microsoft Service Map Application Views
* Microsoft System Center Service Map Internal
* Microsoft System Center Service Map Overrides
* Microsoft System Center Service Map

## <a name="configure-integration"></a>Configurare l'integrazione

Dopo aver installato il Management Pack di Mapping dei servizi, viene visualizzato un nuovo nodo, **mapping dei servizi**, in **Operations Management Suite** nel riquadro **Amministrazione** della console operatore Operations Manager.

>[!NOTE]
>[Operations Management Suite è una raccolta di servizi](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) che includono log Analytics, fa ora parte di [monitoraggio di Azure](../overview.md).

Per configurare l'integrazione della mappa Monitoraggio di Azure per le macchine virtuali, procedere come segue:

1. Per aprire la configurazione guidata, fare clic su **Add workspace** (Aggiungi area di lavoro) nel riquadro **Service Map Overview** (Panoramica di Elenco dei servizi) .  

    ![Riquadro Service Map Overview (Panoramica di Elenco dei servizi)](media/service-map-scom/scom-configuration.png)

2. Nella finestra **Configurazione di connessione** immettere l'ID o il nome del tenant, l'ID applicazione (noto anche come nome utente o ClientID) e la password dell'entità servizio, quindi fare clic su **Avanti**. Per altre informazioni, consultare Creare un'entità servizio.

    ![Finestra di configurazione della connessione](media/service-map-scom/scom-config-spn.png)

3. Nella finestra **Selezione sottoscrizione**, selezionare la sottoscrizione di Azure, il gruppo di risorse di Azure contenente l'area di lavoro Log Analytics e infine l'area di lavoro stessa, quindi fare clic su **Avanti**.

    ![Area di lavoro di configurazione di Operations Manager](media/service-map-scom/scom-config-workspace.png)

4. Nella finestra **Machine Group Selection** (Selezione gruppi di computer) è possibile scegliere i gruppi di computer di Mapping dei servizi da sincronizzare con Operations Manager. Fare clic su **Add/Remove Machine Groups** (Aggiungi/Rimuovi gruppi di computer), scegliere i gruppi nell'elenco **Available Machine Groups** (Gruppi di computer disponibili) e fare clic su **Aggiungi**.  Dopo avere completato la selezione dei gruppi, fare clic su **OK** per terminare.

    ![Gruppi di computer di configurazione di Operations Manager](media/service-map-scom/scom-config-machine-groups.png)

5. Nella finestra **Selezione server** è possibile configurare il gruppo server mapping dei servizi con i server che si desidera sincronizzare tra Operations Manager e la funzionalità mappa. Fare clic su **Aggiungi/Rimuovi server**.

    Perché l'integrazione crei un diagramma applicazioni distribuite per un server, quest'ultimo deve essere:

   * Monitorato da Operations Manager
   * Configurato per la segnalazione all'area di lavoro Log Analytics configurata con Monitoraggio di Azure per le macchine virtuali
   * Elencato nel gruppo di server di Mapping dei servizi

     ![Gruppo di configurazione di Operations Manager](media/service-map-scom/scom-config-group.png)

6. Facoltativo: Selezionare il pool di risorse tutti i server di gestione per comunicare con Log Analytics, quindi fare clic su **Aggiungi area di lavoro**.

    ![Pool di risorse di configurazione di Operations Manager](media/service-map-scom/scom-config-pool.png)

    Per configurare e registrare l'area di lavoro Log Analytics potrebbero essere necessari alcuni minuti. Dopo la configurazione, Operations Manager avvia la prima sincronizzazione della mappa.

    ![Pool di risorse di configurazione di Operations Manager](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>Monitorare l'integrazione

Una volta connessa l'area di lavoro Log Analytics, nel riquadro **monitoraggio** della console operatore Operations Manager verrà visualizzata una nuova cartella mapping dei servizi.

![Riquadro Monitoraggio di Operations Manager](media/service-map-scom/scom-monitoring.png)

La cartella Mapping dei servizi ha quattro nodi:

* **Avvisi attivi**: Elenca tutti gli avvisi attivi relativi alla comunicazione tra Operations Manager e monitoraggio di Azure.  

  >[!NOTE]
  >Questi avvisi non sono Log Analytics gli avvisi sincronizzati con Operations Manager, vengono generati nel gruppo di gestione basato sui flussi di lavoro definiti nella Management Pack di Mapping dei servizi.

* **Server**: Elenca i server monitorati configurati per la sincronizzazione da Monitoraggio di Azure per le macchine virtuali funzionalità mappa.

    ![Riquadro Monitoraggio server di Operations Manager](media/service-map-scom/scom-monitoring-servers.png)

* **Machine Group Dependency Views** (Visualizzazioni dipendenze gruppi di computer): Elenca tutti i gruppi di computer sincronizzati dalla funzionalità mappa. È possibile fare clic su un gruppo per visualizzarne il diagramma applicazioni distribuite.

    ![Diagramma applicazioni distribuite di Operations Manager](media/service-map-scom/scom-group-dad.png)

* **Server Dependency Views** (Visualizzazioni dipendenze server): Elenca tutti i server sincronizzati dalla funzionalità mappa. È possibile fare clic su un server per visualizzarne il diagramma applicazioni distribuite.

    ![Diagramma applicazioni distribuite di Operations Manager](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Modificare o eliminare l'area di lavoro

È possibile modificare o eliminare l'area di lavoro configurata tramite il riquadro **Service Map Overview** (Panoramica di Elenco dei servizi): riquadro **Amministrazione** --> **Operations Management Suite** > **Elenco dei servizi**.

>[!NOTE]
>[Operations Management Suite era una raccolta di servizi](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand) che includeva Log Analytics, ora incluso in [Monitoraggio di Azure](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md).

Nella versione corrente è possibile configurare una sola area di lavoro Log Analytics.

![Riquadro Modifica area di lavoro di Operations Manager](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Configurare regole e override

Una regola, *Microsoft. SystemCenter. ServiceMapImport. Rule*, recupera periodicamente le informazioni dalla funzionalità di monitoraggio di Azure per le macchine virtuali mappa. Per modificare l'intervallo di sincronizzazione, è possibile eseguire l'override della regola e modificare il valore per il parametro **IntervalMinutes**.

![Finestra delle proprietà di override di Operations Manager](media/service-map-scom/scom-overrides.png)

* **Enabled**: abilita/disabilita gli aggiornamenti automatici.
* **IntervalMinutes**: Specifica l'intervallo di tempo tra gli aggiornamenti. L'intervallo predefinito è un'ora. Se si desidera sincronizzare le mappe con maggiore frequenza, è possibile modificare il valore.
* **TimeoutSeconds**: Specifica l'intervallo di tempo prima del timeout della richiesta.
* **TimeWindowMinutes**: Specifica l'intervallo di tempo per l'esecuzione di query sui dati. Il valore predefinito è 60 minuti, ovvero l'intervallo massimo consentito.

## <a name="known-issues-and-limitations"></a>Problemi noti e limitazioni

La progettazione attuale presenta i problemi e le limitazioni seguenti:

* È possibile connettersi a una sola area di lavoro Log Analytics.
* Anche se è possibile aggiungere manualmente server al gruppo di server di Mapping dei servizi tramite il riquadro **Creazione e modifica**, le mappe di tali server non vengono sincronizzate immediatamente. Verranno sincronizzati dalla funzionalità di Monitoraggio di Azure per le macchine virtuali mappa durante il ciclo di sincronizzazione successivo.
* Se si apportano modifiche ai diagrammi applicazione distribuiti creati dal Management Pack, tali modifiche verranno probabilmente sovrascritte alla successiva sincronizzazione con Monitoraggio di Azure per le macchine virtuali.

## <a name="create-a-service-principal"></a>Creare un'entità servizio

Per la documentazione ufficiale di Azure sulla creazione di un'entità servizio, vedere:

* [Create a service principal by using PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal) (Creare un'entità servizio usando PowerShell)
* [Create a service principal by using Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli) (Creare un'entità servizio usando Azure CLI)
* [Create a service principal by using the Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) (Creare un'entità servizio usando il portale di Azure)

### <a name="feedback"></a>Commenti e suggerimenti
Ci sono commenti e suggerimenti per l'integrazione con Monitoraggio di Azure per le macchine virtuali funzionalità mappa o questa documentazione? Vedere la [pagina per i suggerimenti degli utenti](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), in cui è possibile suggerire funzionalità o votare i suggerimenti esistenti.
