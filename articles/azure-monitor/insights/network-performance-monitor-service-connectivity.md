---
title: Soluzione di monitoraggio delle prestazioni di rete in Azure Log Analytics | Microsoft Docs
description: Usare la funzionalità Monitoraggio connettività servizio in Monitoraggio prestazioni rete per monitorare la connettività di rete a qualsiasi endpoint con una porta TCP aperta.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: absha
ms.openlocfilehash: c5285ac95a2f5813949f22aae3849fd7f55b1ada
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052097"
---
# <a name="service-connectivity-monitor"></a>Monitoraggio connettività servizio

È possibile usare la funzionalità Monitoraggio connettività servizio in [Monitoraggio prestazioni rete](network-performance-monitor.md) per monitorare la connettività di rete a qualsiasi endpoint con una porta TCP aperta. Tali endpoint includono siti Web, applicazioni SaaS, applicazioni PaaS e database SQL. 

Con Monitoraggio connettività servizio è possibile eseguire le funzioni seguenti: 

- Monitorare la connettività di rete alle applicazioni e ai servizi di rete da più succursali o località. Le applicazioni e i servizi di rete includono Office 365, Dynamics CRM, applicazioni line-of-business interne e database SQL.
- Usare test predefiniti per monitorare la connettività di rete agli endpoint di Office365 e Dynamics365. 
- Determinare il tempo di risposta, la latenza di rete e la perdita di pacchetti riscontrati durante la connessione all'endpoint.
- Determinare se le prestazioni dell'applicazione non soddisfacenti sono dovute alla rete o a qualche problema da parte del provider dell'applicazione.
- Identificare le aree sensibili della rete che potrebbero essere la causa di prestazioni dell'applicazione non soddisfacenti, visualizzando la latenza generata da ogni hop in una mappa della topologia.


![Monitoraggio connettività servizio](media/network-performance-monitor-service-endpoint/service-endpoint-intro.png)


## <a name="configuration"></a>Configurazione 
Per aprire la configurazione per Monitoraggio prestazioni rete, aprire la [soluzione Monitoraggio prestazioni rete](network-performance-monitor.md) e selezionare **Configura**.

![Configurare Monitoraggio prestazioni rete](media/network-performance-monitor-service-endpoint/npm-configure-button.png)


### <a name="configure-log-analytics-agents-for-monitoring"></a>Configurare gli agenti di Log Analytics per il monitoraggio
Abilitare le regole del firewall seguenti nei nodi usati per il monitoraggio in modo che la soluzione possa individuare la topologia dai nodi all'endpoint di servizio: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```

### <a name="create-service-connectivity-monitor-tests"></a>Creare test di Monitoraggio connettività servizio 

Iniziare a creare i test per monitorare la connettività di rete agli endpoint di servizio.

1. Selezionare la scheda **Monitoraggio connettività servizio**.
2. Selezionare **Aggiungi test** e immettere nome e descrizione del test. È possibile creare test di 450 massimo per ogni area di lavoro. 
3. Selezionare il tipo di test:<br>

    * Selezionare **Web** per monitorare la connettività a un servizio che risponde a richieste HTTP/S, ad esempio outlook.office365.com o bing.com.<br>
    * Selezionare **Rete** per monitorare la connettività a un servizio che risponde a una richiesta TCP, ma non risponde a richieste HTTP/S, ad esempio un server SQL, un server FTP o una porta SSH. 
    * Ad esempio:  Per creare un test web in un account di archiviazione blob, selezionare **Web** e immettere destinazione perché *yourstorageaccount*. blob.core.windows.net. Analogamente è possibile creare test per altri archiviazione tabelle, archiviazione code e file di Azure usando [questo collegamento.](https://docs.microsoft.com/azure/storage/common/storage-account-overview#storage-account-endpoints)
4. Se non si vuole eseguire misure di rete, ad esempio latenza di rete, perdita di pacchetti e individuazione della topologia, deselezionare la casella di controllo **Esegui misure di rete**. Per sfruttare al meglio la funzionalità, lasciare questa opzione selezionata. 
5. In **Destinazione** immettere l'indirizzo URL/FQDN/IP per cui si vuole monitorare la connettività di rete.
6. In **Numero di porta** immettere il numero di porta del servizio di destinazione. 
7. In **Frequenza test** immettere un valore per la frequenza con cui si vuole eseguire il test. 
8. Selezionare i nodi da cui si vuole monitorare la connettività della rete al servizio. Assicurarsi che il numero di agenti aggiunti al test sia inferiore a 150. Tutti gli agenti possono massimi 150 endpoint/gli agenti di test.

    >[!NOTE]
    > Per i nodi basati su server Windows, la funzionalità usa richieste basate su TCP per eseguire le misure di rete. Per i nodi basati su client Windows, la funzionalità usa richieste basate su ICMP per eseguire le misure di rete. In alcuni casi, l'applicazione di destinazione blocca le richieste basate su ICMP in ingresso quando i nodi sono basati su client Windows. In questo caso, la soluzione non riesce a eseguire le misure di rete. In tali casi è consigliabile usare nodi basati su server Windows. 

9. Se non si vuole creare eventi di integrità per gli elementi selezionati, deselezionare **Abilita il monitoraggio dell'integrità nelle destinazioni incluse in questo test**. 
10. Scegliere le condizioni di monitoraggio. È possibile impostare soglie personalizzate per la generazione di eventi di integrità immettendo valori di soglia. Ogni volta che il valore della condizione supera la soglia selezionata per la coppia di rete/subnet selezionata, viene generato un evento di integrità. 
11. Selezionare **Salva** per salvare la configurazione. 

    ![Configurazioni di test di Monitoraggio connettività servizio](media/network-performance-monitor-service-endpoint/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Procedura dettagliata 

Passare alla vista dashboard di Monitoraggio prestazioni rete. Per un riepilogo dell'integrità per i diversi test creati, vedere la pagina **Monitoraggio connettività servizio**. 

![Pagina Monitoraggio connettività servizio](media/network-performance-monitor-service-endpoint/service-endpoint-blade.png)

Selezionare il riquadro per visualizzare i dettagli dei test nella pagina **Test**. Nella tabella a sinistra è possibile visualizzare l'integrità in un determinato momento e il valore del tempo di risposta, della latenza di rete e della perdita di pacchetti del servizio per tutti i test. Usare il controllo di registrazione dello stato di rete per visualizzare lo snapshot della rete in un altro momento nel passato. Selezionare il test nella tabella che si vuole esaminare. Nei grafici nel riquadro a destra è possibile visualizzare la tendenza cronologica relativa ai valori di perdita, latenza e tempo di risposta. Selezionare il collegamento **Dettagli test** per visualizzare le prestazioni di ogni nodo.

![Test di Monitoraggio connettività servizio](media/network-performance-monitor-service-endpoint/service-endpoint-tests.png)

Nella visualizzazione **Nodi test** è possibile osservare la connettività di rete da ogni nodo. Selezionare il nodo che presenta una riduzione del livello delle prestazioni. Si tratta del nodo in cui si osserva il rallentamento dell'esecuzione dell'applicazione.

Determinare se le prestazioni dell'applicazione non soddisfacenti sono dovute alla rete o a qualche problema da parte del provider dell'applicazione osservando la correlazione tra il tempo di risposta dell'applicazione e la latenza di rete. 

* **Problema dell'applicazione:** se si verifica un picco nel tempo di risposta, ma la coerenza nella latenza di rete indica che la rete funziona correttamente, il problema potrebbe essere dovuto all'applicazione. 

    ![Problema dell'applicazione in Monitoraggio connettività servizio](media/network-performance-monitor-service-endpoint/service-endpoint-application-issue.png)

* **Problema di rete:** un picco nel tempo di risposta con un picco corrispondente nella latenza di rete indica che l'aumento nel tempo di risposta potrebbe essere dovuto a un aumento nella latenza di rete. 

    ![Problema di rete in Monitoraggio connettività servizio](media/network-performance-monitor-service-endpoint/service-endpoint-network-issue.png)

Dopo avere determinato che il problema è causato dalla rete, selezionare il collegamento della visualizzazione **Topologia** per identificare l'hop problematico nella mappa della topologia. Nell'immagine seguente è illustrato un esempio. Dei 105 ms di latenza totale tra il nodo e l'endpoint applicazione, 96 ms sono dovuti all'hop contrassegnato in rosso. Dopo avere identificato l'hop problematico, è possibile adottare un'azione correttiva. 

![Test di Monitoraggio connettività servizio](media/network-performance-monitor-service-endpoint/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnostica 

Se si osserva un'anomalia, seguire questa procedura:

* Se il tempo di risposta, la perdita di rete e la latenza del servizio vengono visualizzati come non disponibili, i motivi possono essere uno o più dei seguenti:

    - L'applicazione è inattiva.
    - Il nodo usato per controllare la connettività di rete al servizio è inattivo.
    - La destinazione immessa nella configurazione di test non è corretta.
    - Il nodo non ha connettività di rete.

* Se viene visualizzato un tempo di risposta del servizio valido, ma la perdita di rete e la latenza vengono visualizzate come non disponibili, i motivi possono essere uno o più dei seguenti:

    - Se il nodo usato per controllare la connettività di rete al servizio è un computer client Windows, il servizio di destinazione sta bloccando le richieste ICMP o un firewall di rete sta bloccando le richieste ICMP provenienti dal nodo.
    - La casella di controllo **Esegui misure di rete** è stata deselezionata nella configurazione di test. 

* Se il tempo di risposta del servizio non è disponibile, ma la perdita di rete e la latenza sono valide, il servizio di destinazione potrebbe non essere un'applicazione Web. Modificare la configurazione di test e scegliere il tipo di test **Rete** invece di **Web**. 

* Se l'esecuzione dell'applicazione risulta rallentata, determinare se le prestazioni dell'applicazione non soddisfacenti sono dovute alla rete o a qualche problema da parte del provider dell'applicazione.

## <a name="gcc-office-urls-for-us-government-customers"></a>GCC Office URL per i clienti US Government
Per un'area governo Virginia, solo gli URL DOD sono incorporati NPM. I clienti che usano GCC URL necessario creare test personalizzati e aggiungervi singolarmente ogni URL.

| Campo | GCC |
|:---   |:--- |
| Portale di Office 365 e condivisi | portal.apps.mil |
| Identità e autenticazione di office 365 | * login.microsoftonline.us <br> * api.login.microsoftonline.com <br> * clientconfig.microsoftonline-p.net <br> * login.microsoftonline.com <br> * login.microsoftonline-p.com <br> * login.windows.net <br> * loginex.microsoftonline.com <br> * login-us.microsoftonline.com <br> * nexus.microsoftonline-p.com <br> * mscrl.microsoft.com <br> * secure.aadcdn.microsoftonline-p.com |
| Office Online | * adminwebservice.gov.us.microsoftonline.com <br>  * adminwebservice-s1-bn1a.microsoftonline.com <br> * adminwebservice-s1-dm2a.microsoftonline.com <br> * becws.gov.us.microsoftonline.com <br> * provisioningapi.gov.us.microsoftonline.com <br> * officehome.msocdn.us <br> * prod.msocdn.us <br> * portal.office365.us <br> * webshell.suite.office365.us <br> * www .office365.us <br> * activation.sls.microsoft.com <br> * crl.microsoft.com <br> * go.microsoft.com <br> * insertmedia.bing.office.net <br> * ocsa.officeapps.live.com <br> * ocsredir.officeapps.live.com <br> * ocws.officeapps.live.com <br> * office15client.microsoft.com <br>* officecdn.microsoft.com <br> * officecdn.microsoft.com.edgesuite.net <br> * officepreviewredir.microsoft.com <br> * officeredir.microsoft.com <br> * ols.officeapps.live.com  <br> * r.office.microsoft.com <br> * cdn.odc.officeapps.live.com <br> * odc.officeapps.live.com <br> * officeclient.microsoft.com |
| Exchange Online | * outlook.office365.us <br> * attachments.office365-net.us <br> * autodiscover-s.office365.us <br> * manage.office365.us <br> * scc.office365.us |
| MS Teams | gov.teams.microsoft.us | 

## <a name="next-steps"></a>Passaggi successivi
[Effettuare una ricerca nei log](../../azure-monitor/log-query/log-query-overview.md) per visualizzare i record dettagliati dei dati delle prestazioni di rete.
