---
title: Monitorare i log di accesso e delle prestazioni, l'integrità back-end e le metriche per il gateway applicazione
description: Informazioni su come abilitare e gestire i log di accesso e delle prestazioni per il gateway applicazione
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 1/11/2019
ms.author: amitsriva
ms.openlocfilehash: 14b99f648bb1d7e1926aa9d5dd9926e267ba9709
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57309129"
---
# <a name="back-end-health-diagnostic-logs-and-metrics-for-application-gateway"></a>Integrità back-end, log di diagnostica e metriche per il gateway applicazione

Il gateway applicazione di Azure consente di monitorare le risorse nei modi seguenti:

* [Integrità back-end](#back-end-health): il gateway applicazione consente monitorare l'integrità dei server nei pool di back-end tramite il portale di Azure e PowerShell. L'integrità dei pool back-end è disponibile anche tramite i log di diagnostica delle prestazioni.

* [Log](#diagnostic-logging): i log consentono di salvare o usare i dati delle prestazioni, di accesso e di altro tipo relativi a una risorsa per scopi di monitoraggio.

* [Metriche](#metrics): il gateway applicazione dispone attualmente di sette metriche per visualizzare i contatori delle prestazioni.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="back-end-health"></a>Integrità back-end

Il gateway applicazione consente di monitorare l'integrità dei singoli membri dei pool back-end tramite il portale, PowerShell e l'interfaccia della riga di comando (CLI). Le informazioni di riepilogo dell'integrità dei pool back-end sono disponibili anche tramite i log di diagnostica delle prestazioni. 

Il report sull'integrità back-end riflette l'output del probe di integrità del gateway applicazione nelle istanze back-end. Quando l'esecuzione del probe ha esito positivo e il back-end può ricevere il traffico, il membro è considerato integro. In caso contrario, viene considerato non integro.

> [!IMPORTANT]
> Se nella subnet del gateway applicazione è presente un gruppo di sicurezza di rete, aprire gli intervalli di porte 65503-65534 nella subnet del gateway applicazione per il traffico in ingresso. Questo intervallo di porte è necessario per la comunicazione di infrastruttura di Azure. Sono protette (bloccate) dai certificati di Azure. Senza certificati appropriati, le entità esterne, compresi i clienti di questi gateway, non saranno in grado di avviare alcuna modifica su tali endpoint.


### <a name="view-back-end-health-through-the-portal"></a>Visualizzare l'integrità back-end tramite il portale

Nel portale l'integrità back-end viene visualizzata automaticamente. In un gateway applicazione esistente selezionare **Monitoraggio** > **Integrità back-end**. 

Ogni membro del pool back-end è elencato in questa pagina, indipendentemente dal fatto che si tratti di schede di interfaccia di rete, IP o FQDN. Vengono visualizzati il nome del pool back-end, la porta, il nome delle impostazioni HTTP back-end e lo stato di integrità. I valori validi per lo stato di integrità sono **Integro**, **Danneggiato** e **Sconosciuto**.

> [!NOTE]
> Se viene visualizzato lo stato di integrità back-end **Sconosciuto**, assicurarsi che l'accesso al back-end non sia bloccato da una regola del gruppo di sicurezza di rete, da una route definita dall'utente (UDR) o da un DNS personalizzato nella rete virtuale.

![Integrità back-end][10]

### <a name="view-back-end-health-through-powershell"></a>Visualizzare l'integrità back-end tramite PowerShell

Il codice PowerShell seguente illustra come visualizzare l'integrità back-end usando il cmdlet `Get-AzApplicationGatewayBackendHealth`:

```powershell
Get-AzApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli"></a>Visualizzare l'integrità back-end tramite l'interfaccia della riga di comando di Azure

```azurecli
az network application-gateway show-backend-health --resource-group AdatumAppGatewayRG --name AdatumAppGateway
```

### <a name="results"></a>Risultati

Il frammento seguente illustra un esempio della risposta:

```json
{
"BackendAddressPool": {
    "Id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/appGatewayBackendPool"
},
"BackendHttpSettingsCollection": [
    {
    "BackendHttpSettings": {
        "Id": "/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
    },
    "Servers": [
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        },
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        }
    ]
    }
]
}
```

## <a name="diagnostic-logging"></a>Registri di diagnostica

In Azure è possibile usare diversi tipi di log per gestire e risolvere i problemi dei gateway applicazione. Alcuni di questi log sono accessibili tramite il portale. Tutti i log possono essere estratti dall'archiviazione BLOB di Azure e visualizzati in strumenti diversi, ad esempio i [log di Monitoraggio di Azure](../azure-monitor/insights/azure-networking-analytics.md), Excel e Power BI. L'elenco seguente contiene altre informazioni sui diversi tipi di log:

* **Log attività**: è possibile usare i [log attività di Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md), chiamati in precedenza log operativi e log di controllo, per visualizzare tutte le operazioni inviate alla sottoscrizione di Azure e il relativo stato. Le voci dei log attività vengono raccolte per impostazione predefinita e possono essere visualizzate nel portale di Azure.
* **Log di accesso**: È possibile usare questo log per visualizzare i modelli di accesso al gateway applicazione e analizzare informazioni importanti. I dati includono indirizzo IP del chiamante, URL richiesto, latenza della risposta, codice restituito, byte in ingresso e in uscita. Il log di accesso viene raccolto ogni 300 secondi. Il log contiene un record per ogni istanza del gateway applicazione. L'istanza del gateway applicazione può essere identificata dalla proprietà instanceId.
* **Log delle prestazioni**: è possibile usare questo log per visualizzare le prestazioni delle istanze del gateway applicazione. Questo log acquisisce le informazioni sulle prestazioni di ogni istanza, inclusi il totale delle richieste servite, la velocità effettiva in byte, il totale delle richieste non riuscite e il numero delle istanze back-end integre e non integre. Il log delle prestazioni viene raccolto ogni 60 secondi.
* **Log del firewall**: è possibile usare questo log per visualizzare le richieste registrate tramite la modalità di rilevamento o prevenzione di un gateway applicazione configurato con il web application firewall.

> [!NOTE]
> I log sono disponibili solo per le risorse distribuite nel modello di distribuzione di Azure Resource Manager. Non è possibile usare i log per le risorse nel modello di distribuzione classica. Per altre informazioni sui due modelli, vedere le [informazioni sulla distribuzione di Resource Manager e la distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md).

Sono disponibili tre opzioni di archiviazione dei log:

* **Account di archiviazione**: sono la soluzione ideale per i log quando questi vengono archiviati per un periodo più lungo ed esaminati quando necessario.
* **Hub eventi**: ottima opzione per l'integrazione con altri strumenti di gestione degli eventi e delle informazioni di sicurezza (SEIM) per ricevere avvisi sulle risorse.
* **Log di Monitoraggio di Azure**: i log di Monitoraggio di Azure sono ideali per il monitoraggio generale in tempo reale dell'applicazione o per l'analisi delle tendenze.

### <a name="enable-logging-through-powershell"></a>Abilitare la registrazione tramite PowerShell

Registrazione attività viene abilitata automaticamente per tutte le risorse di Resource Manager. È necessario abilitare la registrazione degli accessi e delle prestazioni per iniziare a raccogliere i dati disponibili tramite tali log. Per abilitare la registrazione, seguire questa procedura:

1. Prendere nota dell'ID risorsa dell'account di archiviazione in cui vengono archiviati i dati dei log. Il valore ha il formato seguente: /subscriptions/\<subscriptionId\>/resourceGroups/\<nome gruppo di risorse\>/providers/Microsoft.Storage/storageAccounts/\<nome account archiviazione\>. È possibile usare qualsiasi account di archiviazione della sottoscrizione. Per trovare queste informazioni è possibile usare il portale di Azure.

    ![Portale: ID risorsa dell'account di archiviazione](./media/application-gateway-diagnostics/diagnostics1.png)

2. Prendere nota dell'ID risorsa del gateway applicazione per cui è abilitata la registrazione. Il valore ha il formato seguente: /subscriptions/\<subscriptionId\>/resourceGroups/\<nome gruppo di risorse\>/providers/Microsoft.Network/applicationGateways/\<nome gateway applicazione\>. Per trovare queste informazioni è possibile usare il portale.

    ![Portale: ID risorsa del gateway applicazione](./media/application-gateway-diagnostics/diagnostics2.png)

3. Abilitare la registrazione diagnostica usando il cmdlet di PowerShell seguente:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>I log attività non richiedono un account di archiviazione separato. Per l'uso del servizio di archiviazione per la registrazione degli accessi e delle prestazioni è previsto un addebito.

### <a name="enable-logging-through-the-azure-portal"></a>Abilitare la registrazione tramite il portale di Azure

1. Nel portale di Azure trovare la risorsa e fare clic su **Log di diagnostica**.

   Per il gateway applicazione sono disponibili tre log:

   * Log di accesso
   * Log delle prestazioni
   * Log del firewall

2. Fare clic su **Abilita diagnostica** per avviare la raccolta dei dati.

   ![Attivare la diagnostica][1]

3. Il pannello **Impostazioni di diagnostica** include le impostazioni per i log di diagnostica. In questo esempio viene usato Log Analytics per archiviare i log. Fare clic su **Configura** in **Log Analytics** per configurare l'area di lavoro. Per salvare i log di diagnostica è possibile anche usare l'hub eventi e un account di archiviazione.

   ![Avvio del processo di configurazione][2]

4. Scegliere un'area di lavoro di Log Analytics esistente oppure crearne una nuova. Questo esempio usa un'area di lavoro esistente.

   ![Opzioni disponibili per le aree di lavoro di Log Analytics][3]

5. Confermare le impostazioni e fare clic su **Salva**.

   ![Pannello delle impostazioni di diagnostica con le selezioni][4]

### <a name="activity-log"></a>Log attività

Azure genera il log attività per impostazione predefinita. I log vengono conservati per 90 giorni nell'archivio dei log eventi di Azure. Per altre informazioni sui log, leggere l'articolo [Visualizzare eventi e log attività](../monitoring-and-diagnostics/insights-debugging-with-events.md).

### <a name="access-log"></a>Log di accesso

Il log di accesso viene generato solo se è stato abilitato in ogni istanza del gateway applicazione, come descritto nei passaggi precedenti. I dati vengono archiviati nell'account di archiviazione specificato quando è stata abilitata la registrazione. Ogni accesso del gateway applicazione viene registrato in formato JSON, come illustrato nell'esempio seguente:


|Valore  |DESCRIZIONE  |
|---------|---------|
|instanceId     | Istanza del gateway applicazione che ha gestito la richiesta.        |
|clientIP     | IP di origine della richiesta.        |
|clientPort     | Porta di origine della richiesta.       |
|httpMethod     | Metodo HTTP usato dalla richiesta.       |
|requestUri     | URI della richiesta ricevuta.        |
|RequestQuery     | **Server-Routed**: istanza del pool back-end a cui è stata inviata la richiesta.</br>**X-AzureApplicationGateway-LOG-ID**: ID correlazione usato per la richiesta. Può essere usato per risolvere i problemi di traffico nei server back-end. </br>**SERVER-STATUS**: codice di risposta HTTP che il gateway applicazione ha ricevuto dal back-end.       |
|UserAgent     | Agente utente dell'intestazione della richiesta HTTP.        |
|httpStatus     | Codice di stato HTTP restituito al client dal gateway applicazione.       |
|httpVersion     | Versione HTTP della richiesta.        |
|receivedBytes     | Dimensione del pacchetto ricevuto, espressa in byte.        |
|sentBytes| Dimensione del pacchetto inviato, espressa in byte.|
|timeTaken| Periodo di tempo in millisecondi impiegato per l'elaborazione di una richiesta e l'invio della risposta. Questo valore corrisponde all'intervallo di tempo intercorso dal momento in cui il gateway applicazione riceve il primo byte di una richiesta HTTP al termine dell'operazione di invio della risposta. È importante notare che il campo Tempo impiegato include in genere il tempo della trasmissione in rete dei pacchetti di richiesta e risposta. |
|sslEnabled| Indica se la comunicazione con i pool back-end ha usato SSL. I valori validi sono on e off.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off"
    }
}
```

### <a name="performance-log"></a>Log delle prestazioni

Il log delle prestazioni viene generato solo se è stato abilitato in ogni istanza del gateway applicazione, come descritto nei passaggi precedenti. I dati vengono archiviati nell'account di archiviazione specificato quando è stata abilitata la registrazione. I dati del log delle prestazioni vengono generati a intervalli di un minuto. Vengono registrati i dati seguenti:


|Valore  |DESCRIZIONE  |
|---------|---------|
|instanceId     |  Istanza del gateway applicazione per cui vengono generati i dati delle prestazioni. Per un gateway applicazione a più istanze viene visualizzata una riga per ogni istanza.        |
|healthyHostCount     | Numero di host integri nel pool back-end.        |
|unHealthyHostCount     | Numero di host non integri nel pool back-end.        |
|requestCount     | Numero di richieste gestite.        |
|latency | Latenza media in millisecondi delle richieste dall'istanza al back-end che gestisce le richieste. |
|failedRequestCount| Numero di richieste non riuscite.|
|throughput| Velocità effettiva media dall'ultimo log, misurata in byte al secondo.|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> La latenza è calcolata dal momento in cui viene ricevuto il primo byte della richiesta HTTP al momento in cui viene inviato l'ultimo byte della risposta HTTP. Si tratta della somma del tempo di elaborazione del gateway applicazione, del costo di rete per il back-end e del tempo impiegato dal back-end per elaborare la richiesta.

### <a name="firewall-log"></a>Log del firewall

Il log del firewall viene generato solo se è stato abilitato in ogni gateway applicazione, come descritto nei passaggi precedenti. Questo log richiede anche che il web application firewall sia configurato in un gateway applicazione. I dati vengono archiviati nell'account di archiviazione specificato quando è stata abilitata la registrazione. Vengono registrati i dati seguenti:


|Valore  |DESCRIZIONE  |
|---------|---------|
|instanceId     | Istanza del gateway applicazione per cui vengono generati i dati del firewall. Per un gateway applicazione a più istanze viene visualizzata una riga per ogni istanza.         |
|clientIp     |   IP di origine della richiesta.      |
|clientPort     |  Porta di origine della richiesta.       |
|requestUri     | URL della richiesta ricevuta.       |
|ruleSetType     | Tipo di set di regole. Il valore disponibile è OWASP.        |
|ruleSetVersion     | Versione del set di regole usata. I valori disponibili sono 2.2.9 e 3.0.     |
|ruleId     | ID regola dell'evento di attivazione.        |
|Message     | Messaggio descrittivo dell'evento di attivazione. Altre informazioni sono disponibili nella sezione dei dettagli.        |
|action     |  Azione eseguita sulla richiesta. I valori disponibili sono Blocked e Allowed.      |
|site     | Sito per cui è stato generato il log. Attualmente viene visualizzato solo Global poiché le regole sono globali.|
|informazioni dettagliate     | Dettagli dell'evento di attivazione.        |
|details.message     | Descrizione della regola.        |
|details.data     | Dati specifici individuati nella richiesta corrispondente alla regola.         |
|details.file     | File di configurazione che conteneva la regola.        |
|details.line     | Numero di riga nel file di configurazione che ha attivato l'evento.       |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
  }
} 

```

### <a name="view-and-analyze-the-activity-log"></a>Visualizzare e analizzare Log attività

È possibile visualizzare e analizzare i dati del log attività usando uno dei metodi seguenti:

* **Strumenti di Azure**: consente di recuperare informazioni dal log attività con Azure PowerShell, l'interfaccia della riga di comando di Azure, l'API REST di Azure o il portale di Azure. Le istruzioni dettagliate di ciascun metodo sono fornite nell'articolo [Operazioni attività con Resource Manager](../azure-resource-manager/resource-group-audit.md).
* **Power BI**: se non esiste ancora un account [Power BI](https://powerbi.microsoft.com/pricing), è possibile crearne uno di prova gratuitamente. Usando il [pacchetto di contenuto dei log attività di Azure per Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) è possibile analizzare i dati con i dashboard preconfigurati che possono anche essere personalizzati.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Visualizzare e analizzare i log di accesso, delle prestazioni e del firewall

I [log di Monitoraggio di Azure](../azure-monitor/insights/azure-networking-analytics.md) consentono di raccogliere i file dei log contatori ed eventi dall'account di archiviazione BLOB. Include visualizzazioni e funzionalità di ricerca avanzate per analizzare i log.

È anche possibile connettersi all'account di archiviazione e recuperare le voci del log JSON per i log di accesso e delle prestazioni. Dopo avere scaricato i file JSON, è possibile convertirli in CSV e visualizzarli in Excel, PowerBI o un altro strumento di visualizzazione dei dati.

> [!TIP]
> Se si ha familiarità con Visual Studio e i concetti di base della modifica dei valori di costanti e variabili in C#, è possibile usare i [convertitori di log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibili in GitHub.
> 
> 

#### <a name="analyzing-access-logs-through-goaccess"></a>Analisi dei log di accesso con GoAccess

È stato pubblicato un modello di Resource Manager che installa ed esegue il diffuso analizzatore di log [GoAccess](https://goaccess.io/) per i log di accesso del gateway applicazione. GoAccess offre utili statistiche sul traffico HTTP, come ad esempio visitatori univoci, file richiesti, host, sistemi operativi, browser, codici di stato HTTP e altro ancora. Per altre informazioni, vedere il [file Readme nella cartella del modello di Resource Manager in GitHub](https://aka.ms/appgwgoaccessreadme).

## <a name="metrics"></a>Metriche

Le metriche sono una funzionalità di alcune risorse di Azure che consente di visualizzare i contatori delle prestazioni nel portale. Per il gateway applicazione sono disponibili le metriche seguenti:

- **Connessioni correnti**
- **Richieste non riuscite**
- **Numero di host integri**

   È possibile filtrare per pool back-end per visualizzare gli host integri o non integri in un pool back-end specifico.


- **Stato della risposta**

   La distribuzione del codice di stato della risposta può essere ulteriormente classificata per visualizzare le risposte nelle categorie 2xx, 3xx, 4xx e 5xx.

- **Velocità effettiva**
- **Richieste totali**
- **Numero di host non integri**

   È possibile filtrare in base al pool back-end per visualizzare gli host integri o non integri in un pool back-end specifico.

Passare a un gateway applicazione e in **Monitoraggio** fare clic su **Metriche**. Per visualizzare i valori disponibili, selezionare l'elenco a discesa **METRICA**.

Nella figura seguente è illustrato un esempio con tre metriche visualizzate per gli ultimi 30 minuti:

[![](media/application-gateway-diagnostics/figure5.png "Visualizzazione delle metriche")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Per un elenco delle metriche correnti, vedere [Metriche supportate con il monitoraggio di Azure](../azure-monitor/platform/metrics-supported.md).

### <a name="alert-rules"></a>Regole di avviso

È possibile avviare le regole di avviso in base alle metriche per una risorsa. Ad esempio, un avviso può chiamare un webhook o inviare un messaggio di posta elettronica a un amministratore se la velocità effettiva del gateway applicazione è al di sopra, al di sotto o corrisponde alla soglia per un periodo di tempo specificato.

L'esempio seguente illustra la creazione di una regola di avviso per l'invio di un messaggio di posta elettronica a un amministratore al superamento della soglia della velocità effettiva:

1. Fare clic su **Aggiungi avviso per la metrica** per aprire il pannello **Aggiungi regola**. È possibile visualizzare questo pannello anche dal pannello delle metriche.

   ![Pulsante "Aggiungi avviso per la metrica"][6]

2. Nel pannello **Aggiungi regola** compilare le sezioni relative a nome, condizione e notifica e fare clic su **OK**.

   * Nel selettore **Condizione** selezionare uno dei quattro valori seguenti: **Maggiore di**, **Maggiore di o uguale a**, **Minore di** o **Minore o uguale a**.

   * Nel selettore **Periodo** selezionare un periodo compreso tra cinque minuti e sei ore.

   * Se si seleziona **Invia messaggio di posta elettronica a proprietari, collaboratori e lettori**, il messaggio di posta elettronica può essere dinamico basato sugli utenti che hanno accesso alla risorsa. In alternativa, è possibile inserire un elenco di utenti separato da virgole nella casella **Indirizzi di posta elettronica aggiuntivi dell'amministratore**.

   ![Aggiungere il pannello delle regole][7]

Se la soglia viene superata, l'utente riceve un messaggio di posta elettronica simile al seguente:

![Messaggio di posta elettronica per il superamento della soglia][8]

Dopo aver creato un avviso per la metrica, viene visualizzato un elenco di avvisi. L'elenco offre una panoramica di tutte le regole di avviso.

![Elenco di avvisi e regole][9]

Per altre informazioni sulle notifiche di avviso, vedere [Ricevere notifiche di avviso](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Per altre informazioni sui webhook e su come usarli con gli avvisi, vedere [Configurare un webhook in un avviso relativo alle metriche di Azure](../azure-monitor/platform/alerts-webhooks.md).

## <a name="next-steps"></a>Passaggi successivi

* Visualizzare i log contatori ed eventi usando i [log di Monitoraggio di Azure](../azure-monitor/insights/azure-networking-analytics.md).
* Post di blog [Visualize your Azure activity log with Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) (Visualizzare il log attività di Azure con Power BI).
* Post di blog [View and analyze Azure activity logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) (Visualizzare e analizzare i log attività di Azure in Power BI e altre opzioni).

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
