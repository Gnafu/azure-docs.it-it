---
title: Monitorare operazioni, eventi e contatori per Load Balancer Basic pubblico
titlesuffix: Azure Load Balancer
description: Informazioni su come abilitare gli eventi di avviso e la registrazione dello stato di integrità del probe per Load Balancer Basic pubblico
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/10/2018
ms.author: allensu
ms.openlocfilehash: 1995ad5e8179fdee11e960c2ad0e7c03602ebd31
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274808"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Log di monitoraggio di Azure per Load Balancer di base pubblici

>[!IMPORTANT] 
>Azure Load Balancer supporta due tipi diversi: Di base e standard. Questo articolo illustra Load Balancer Basic. Per ulteriori informazioni su Load Balancer Standard, vedere [Panoramica di Load Balancer Standard](load-balancer-standard-overview.md) che espone i dati di telemetria tramite le metriche multidimensionali in Monitoraggio di Azure.

È possibile usare diversi tipi di log in Azure per gestire e risolvere i problemi di Load Balancer Basic. Alcuni di questi log sono accessibili tramite il portale Tutti i log possono essere estratti da Archiviazione BLOB di Azure e visualizzati in strumenti differenti, ad esempio Excel e PowerBI. L'elenco seguente contiene altre informazioni sui diversi tipi di log.

* **Log di controllo**: è possibile usare i [log di controllo di Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (noti in precedenza come log operativi) per visualizzare tutte le operazioni da inviare alle sottoscrizioni di Azure e il relativo stato. I log di controllo sono abilitati per impostazione predefinita e possono essere visualizzati nel portale di Azure.
* **Log eventi di avviso:** è possibile usare questo log per visualizzare gli avvisi generati per il bilanciamento del carico. Le informazioni di stato per il bilanciamento del carico vengono raccolte ogni cinque minuti. Questo log viene scritto solo se viene generato un evento di avviso del bilanciamento del carico.
* **Log del probe di integrità:** è possibile usare questo registro per visualizzare i problemi rilevati dal probe di integrità, ad esempio il numero di istanze del pool di back-end che non stanno ricevendo richieste dal servizio di bilanciamento del carico a causa di problemi del probe di integrità. Questo log viene scritto quando si apporta una modifica nello stato del probe di integrità.

> [!IMPORTANT]
> I log di monitoraggio di Azure attualmente funzionano solo per i bilanciamenti del carico di base pubblici. I log sono disponibili solo per le risorse distribuite nel modello di distribuzione di Gestione risorse. Non è possibile usare i log per le risorse nel modello di distribuzione classica. Per altre informazioni su questi modelli di distribuzione, vedere l'articolo relativo alle [informazioni sulla distribuzione di Gestione risorse e sulla distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="enable-logging"></a>Abilitazione della registrazione

La registrazione di controllo viene abilitata automaticamente per ogni risorsa di Resource Manager. È necessario abilitare la registrazione di eventi e del probe di integrità per iniziare a raccogliere i dati disponibili in tali log. Eseguire questa procedura per abilitare la registrazione.

Accedere al [portale di Azure](https://portal.azure.com). Prima di procedere, [creare un servizio di bilanciamento del carico](load-balancer-get-started-internet-arm-ps.md) , se non se ne ha già uno.

1. Nel Portale di Azure fare clic su **Esplora**.
2. Selezionare **Bilanciamento del carico**.

    ![portale - bilanciamento del carico](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. Selezionare un bilanciamento del carico esistente >> **Tutte le impostazioni**.
4. Sul lato destro della finestra di dialogo sotto il nome del servizio di bilanciamento del carico, scorrere fino a **Monitoraggio** e fare clic su **Diagnostica**.

    ![portale - impostazioni di bilanciamento del carico](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. Nel riquadro **Diagnostica**, in **Stato**, selezionare **On**.
6. Fare clic su **Account di archiviazione**.
7. In **LOG** selezionare un account di archiviazione esistente o crearne uno nuovo. Usare il dispositivo di scorrimento per stabilire per quanti giorni i dati dell'evento verranno archiviati nei log eventi. 
8. Fare clic su **Save**.

I dati della diagnostica verranno salvati in archiviazione tabelle nell’account di archiviazione specificato. Se i log non vengono salvati, significa che non sono stati prodotti log rilevanti.

![Portale - Log di diagnostica](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

> [!NOTE]
> I log di controllo non richiedono un account di archiviazione separato. Per l'uso del servizio di archiviazione per la registrazione di eventi e del probe di integrità è previsto un addebito.

## <a name="audit-log"></a>Log di controllo

Il log di controllo viene generato per impostazione predefinita. I log vengono conservati per 90 giorni nell'archivio dei log eventi di Azure. Per altre informazioni su questi log, vedere l'articolo [Visualizzare eventi e log di controllo](../monitoring-and-diagnostics/insights-debugging-with-events.md) .

## <a name="alert-event-log"></a>Log eventi di avviso

Questo log viene generato solo se è stato abilitato per ogni bilanciamento del carico. Gli eventi vengono registrati in formato JSON e archiviati nell'account di archiviazione specificato quando è stata abilitata la registrazione. Di seguito è riportato un esempio di evento.

```json
{
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
}
```

L'output JSON mostra la proprietà *eventname* che descrive il motivo per cui il bilanciamento del carico ha creato un avviso. In questo caso, l'avviso è stato generato a causa dell'esaurimento delle porte TCP dovuto ai limiti NAT dell'IP di origine (SNAT).

## <a name="health-probe-log"></a>Log del probe di integrità

Questo log viene generato solo se è stato abilitato per ogni bilanciamento del carico, come indicato in precedenza. I dati vengono archiviati nell'account di archiviazione specificato quando è stata abilitata la registrazione. Viene creato un contenitore denominato 'insights-logs-loadbalancerprobehealthstatus' e vengono registrati i dati seguenti:

```json
{
    "records":[
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 1,
            "healthPercentage": 50.000000
        }
    },
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 0,
            "healthPercentage": 100.000000
        }
    }]
}
```

L'output JSON mostra nel campo proprietà le informazioni di base per lo stato di integrità del probe. La proprietà *dipDownCount* indica il numero totale di istanze nel back-end che non ricevono traffico di rete a causa di risposte del probe non riuscite.

## <a name="view-and-analyze-the-audit-log"></a>Visualizzare e analizzare il log di controllo

È possibile visualizzare e analizzare i dati del log di controllo con uno dei metodi seguenti:

* **Strumenti di Azure:** recuperare le informazioni dai log di controllo tramite Azure PowerShell, l'interfaccia della riga di comando di Azure, l'API REST di Azure o il portale di anteprima di Azure. Per istruzioni dettagliate per ogni metodo, vedere l'articolo [Operazioni di controllo con Gestione risorse](../azure-resource-manager/resource-group-audit.md) .
* **Power BI:** se non si ha ancora un account [Power BI](https://powerbi.microsoft.com/pricing), è possibile crearne uno di prova gratuitamente. Con il [pacchetto di contenuto dei log di controllo di Azure per Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs) è possibile analizzare i dati con dashboard preconfigurati oppure personalizzare le viste in base alle esigenze.

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>Visualizzare e analizzare il log eventi e del probe di integrità

È necessario connettersi all'account di archiviazione e recuperare le voci di log JSON per i log eventi e del probe di integrità. Dopo avere scaricato i file JSON, è possibile convertirli in CSV e visualizzarli in Excel, PowerBI o un altro strumento di visualizzazione dei dati.

> [!TIP]
> Se si ha familiarità con Visual Studio e i concetti di base della modifica dei valori di costanti e variabili in C#, è possibile usare i [convertitori di log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibili in GitHub.

## <a name="additional-resources"></a>Risorse aggiuntive

* [visualizzazione dei log di controllo di Azure con Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) .
* [visualizzazione e analisi dei log di controllo di Azure in Power BI e altri strumenti](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) .

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sui probe di bilanciamento del carico](load-balancer-custom-probe-overview.md)
