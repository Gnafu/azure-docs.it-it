---
title: Monitoraggio continuo della pipeline di versione di DevOps con Azure DevOps e Azure Application Insights | Microsoft Docs
description: Vengono fornite istruzioni per configurare rapidamente il monitoraggio continuo con Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/13/2017
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ecda8621640223f1c27f32834f2e4a098da4aba6
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2018
ms.locfileid: "44301626"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>Aggiungere un monitoraggio continuo alla pipeline di versione

Azure DevOps Services si integra con Azure Application Insights per consentire il monitoraggio continuo della pipeline di versione di DevOps in tutto il ciclo di vita di sviluppo del software. 

Azure DevOps Services supporta ora il monitoraggio continuo con cui le pipeline di versione possono incorporare i dati di monitoraggio da Application Insights e da altre risorse di Azure. Quando si rileva un avviso di Application Insights, la distribuzione può rimanere controllata o essere sottoposta a rollback fino alla risoluzione dell'avviso. Se si superano tutti i controlli, le distribuzioni possono passare automaticamente dal test alla produzione senza la necessità di interventi manuali. 

## <a name="configure-continuous-monitoring"></a>Configurare il monitoraggio continuo

1. Selezionare un progetto Azure DevOps Services.

2. Passare il mouse su **Compilazione e versione** > selezionare **Versioni** > fare clic sul **segno di addizione** > **Crea definizione di versione** > cercare **Monitoraggio** > **Azure App Service Deployment with Continuous Monitoring (Distribuzione del servizio app di Azure con monitoraggio continuo).**

   ![Nuova pipeline di versione di Azure DevOps Services](.\media\app-insights-continuous-monitoring\001.png)

3. Fare clic su **Apply** (Applica).

4. Accanto al punto esclamativo rosso selezionare il testo in blu, **Visualizza le attività dell'ambiente**.

   ![Visualizza le attività dell'ambiente](.\media\app-insights-continuous-monitoring\002.png)

   Verrà visualizzata una casella di configurazione. Usare la tabella seguente per completare i campi di input.

    | Parametro        | Valore |
   | ------------- |:-----|
   | **Nome ambiente**      | Nome che descrive l'ambiente della pipeline di versione |
   | **Sottoscrizione di Azure** | L'elenco a discesa si popola con le sottoscrizioni di Azure collegate all'organizzazione di Azure DevOps Services|
   | **Nome del servizio app** | In base alle altre selezioni, per questo campo potrebbe essere necessaria l'immissione manuale di un nuovo valore |
   | **Gruppo di risorse**    | L'elenco a discesa si popola con i gruppi di risorse disponibili |
   | **Nome risorsa di Application Insights** | L'elenco a discesa si popola con tutte le risorse di Application Insights che corrispondono al gruppo di risorse selezionato in precedenza.

5. Selezionare **Configure Application Insights alerts** (Configura avvisi di Application Insights)

6. Per le regole di avviso predefinite, selezionare **Salva** > immettere un commento descrittivo > fare clic su **OK**

## <a name="modify-alert-rules"></a>Modificare le regole di avviso

1. Per modificare le impostazioni predefinite degli avvisi, fare clic sulla casella con i **puntini di sospensione ...**  a destra delle **Regole di avviso**.

   (Sono presenti quattro regole di avviso out-of-box: Disponibilità, Richieste non riuscite, Tempo di risposta del server, Eccezioni del server.)

2. Fare clic sul simbolo dell'elenco a discesa accanto a **Disponibilità.**

3. Modificare la **Soglia** di disponibilità per soddisfare i requisiti del livello di servizio.

   ![Modificare l'avviso](.\media\app-insights-continuous-monitoring\003.png)

4. Selezionare **OK** > **Salva** > immettere un commento descrittivo > fare clic su **OK.**

## <a name="add-deployment-conditions"></a>Aggiungere le condizioni di distribuzione

1. Fare clic su **Pipeline** > selezionare il simbolo di **Condizioni pre-distribuzione** o di **Condizioni post-distribuzione** a seconda della fase che richiede un controllo di monitoraggio continuo.

   ![Condizioni di pre-distribuzione](.\media\app-insights-continuous-monitoring\004.png)

2. Impostare **Gates** (Controlli) su **Enabled (Abilitati)** > **Approval gates (Controlli di approvazione)**>  fare clic su **Add (Aggiungi).**

3. Selezionare **Monitoraggio di Azure** (questa opzione offre la possibilità di accedere agli avvisi sia da Monitoraggio di Azure che da Application Insights)

    ![Monitoraggio di Azure](.\media\app-insights-continuous-monitoring\005.png)

4. Immettere un valore **Gates timeout**.

5. Immettere un **intervallo di campionamento.**

## <a name="deployment-gate-status-logs"></a>Log di stato dei controlli di distribuzione

Dopo aver aggiunto i controlli di distribuzione, un avviso in Application Insights che indica il superamento della soglia definita in precedenza evita alla distribuzione un innalzamento di livello indesiderato della versione. Dopo aver risolto l'avviso, la distribuzione può continuare automaticamente.

Per osservare questo comportamento, selezionare **Versioni** > fare clic con il pulsante destro del mouse su Nome versione **aprire** > **Log.**

![Log](.\media\app-insights-continuous-monitoring\006.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Pipelines consultare le seguenti [guide introduttive.](https://docs.microsoft.com/azure/devops/pipelines)
