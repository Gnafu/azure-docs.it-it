---
title: Inviare gli avvisi di integrità servizio di Azure con PagerDuty usando i webhook
description: Ricevere notifiche personalizzate sugli eventi di integrità del servizio nell'istanza di PagerDuty.
author: stephbaron
ms.author: stbaron
ms.topic: article
ms.service: service-health
ms.date: 06/10/2019
ms.openlocfilehash: ab3bcffb6453b284c3c8bb0d0373c7155fe8ef23
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067144"
---
# <a name="send-azure-service-health-alerts-with-pagerduty-using-webhooks"></a>Inviare gli avvisi di integrità servizio di Azure con PagerDuty usando i webhook

Questo articolo illustra come configurare le notifiche di integrità dei servizi di Azure tramite PagerDuty usando un webhook. Con il tipo di integrazione personalizzata Microsoft Azure di [PagerDuty](https://www.pagerduty.com/) è possibile aggiungere facilmente gli avvisi di integrità dei servizi ai servizi nuovi o esistenti di PagerDuty.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Creazione di un URL di integrazione dell'integrità dei servizi in PagerDuty
1.  Assicurarsi di avere eseguito la registrazione e l'accesso all'account a [PagerDuty](https://www.pagerduty.com/).

1.  Passare alla sezione **Services** (Servizi) in PagerDuty.

    ![La sezione relativa ai servizi in PagerDuty](./media/webhook-alerts/pagerduty-services-section.png)

1.  Selezionare **Add New Service** (Aggiungi nuovo servizio) o aprire un servizio esistente che è stato configurato.

1.  In **Integration Settings** (Impostazioni di integrazione) selezionare le opzioni seguenti:

    a. **Tipo integrazione**: Microsoft Azure

    b. **Nome integrazione**: \<Nome\>

    ![Le impostazioni di integrazione in PagerDuty](./media/webhook-alerts/pagerduty-integration-settings.png)

1.  Compilare tutti gli altri campi necessari e selezionare **Add** (Aggiungi).

1.  Aprire questa nuova integrazione e copiare e salvare l'**URL di integrazione**.

    ![L'URL di integrazione in PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Creare un avviso usando PagerDuty nel portale di Azure
### <a name="for-a-new-action-group"></a>Per un nuovo gruppo di azioni:
1. Seguire i passaggi da 1 a 8 in [Creare un avviso per una notifica sull'integrità dei servizi per un nuovo gruppo di azioni usando il portale di Azure](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

1. Definire l'elenco di **azioni**:

    a. **Tipo di azione:** *Webhook*

    b. **Dettagli:** l**URL di integrazione** di PagerDuty salvato in precedenza.

    c. **Nome:** nome, alias o identificatore del webhook.

1. Al termine fare clic su **Salva** per creare l'avviso.

### <a name="for-an-existing-action-group"></a>Per un gruppo di azioni esistente:
1. Nel [portale di Azure](https://portal.azure.com/) selezionare **Monitoraggio**.

1. Nella sezione **Impostazioni** selezionare **Gruppi di azioni**.

1. Individuare e selezionare il gruppo di azioni da modificare.

1. Aggiungere all'elenco di **azioni**:

    a. **Tipo di azione:** *Webhook*

    b. **Dettagli:** l**URL di integrazione** di PagerDuty salvato in precedenza.

    c. **Nome:** nome, alias o identificatore del webhook.

1. Al termine fare clic su **Salva** per aggiornare il gruppo di azioni.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Test dell'integrazione del webhook tramite una richiesta HTTP POST
1. Creare il payload di integrità del servizio che si vuole inviare. È possibile trovare un payload di esempio del webhook di integrità del servizio in [Webhook per gli avvisi del log attività di Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Creare una richiesta HTTP POST nel modo seguente:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Verrà visualizzato un codice `202 Accepted` con un messaggio contenente l'"ID evento".

1. Passare a [PagerDuty](https://www.pagerduty.com/) per confermare che l'integrazione è stata configurata correttamente.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [configurare le notifiche di webhook per i sistemi di gestione dei problemi esistenti](service-health-alert-webhook-guide.md).
- Esaminare lo [schema webhook degli avvisi del log attività](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Informazioni sulle [notifiche per l'integrità del servizio](../azure-monitor/platform/service-notifications.md).
- Altre informazioni sui [gruppi di azione](../azure-monitor/platform/action-groups.md).