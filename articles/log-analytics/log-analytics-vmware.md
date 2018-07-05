---
title: Soluzione Monitoraggio VMware in Log Analytics | Microsoft Docs
description: Informazioni su come la soluzione di monitoraggio VMware è in grado di gestire i log e monitorare gli host ESXi.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 16516639-cc1e-465c-a22f-022f3be297f1
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: magoedte
ms.openlocfilehash: c07cc3e434a178a5f6a1ea10f7dc630c3d0d548d
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37019290"
---
# <a name="vmware-monitoring-preview-solution-in-log-analytics"></a>Soluzione di monitoraggio VMware (anteprima) in Log Analytics

![Simbolo di VMware](./media/log-analytics-vmware/vmware-symbol.png)

> [!NOTE]
> La soluzione Monitoraggio VMware è stata deprecata.  I clienti che hanno già installato la soluzione possono continuare a usarla, ma Monitoraggio VMware non potrà essere aggiunto alle nuove aree di lavoro.

La soluzione di monitoraggio VMware di Log Analytics è una soluzione che aiuta a creare un approccio centralizzato a log e monitoraggio per log VMware di grandi dimensioni. In questo articolo viene descritto come è possibile risolvere i problemi, acquisire e gestire gli host ESXi in un'unica posizione usando la soluzione. Con la soluzione, è possibile visualizzare dati dettagliati per tutti gli host ESXi in un'unica posizione. È possibile visualizzare i dati principali su numero, stato e tendenze degli eventi di host ESXi e VM attraverso i log di host ESXi. È possibile risolvere il problema visualizzando e cercando i log di host ESXi centralizzati. Inoltre, è possibile creare avvisi basati sulle query di ricerca nei log.

La soluzione usa la funzionalità del SysLog nativo dell'host ESXi per eseguire il pushing dei dati in una VM con agente OMS. Tuttavia, la soluzione non scrive file nel SysLog all'interno della VM di destinazione. L'agente OMS apre la porta 1514 e vi rimane in ascolto. Una volta ricevuti i dati, l'agente OMS ne esegue il pushing in Log Analytics.

## <a name="install-and-configure-the-solution"></a>Installare e configurare la soluzione
Usare le informazioni seguenti per installare e configurare la soluzione.

* Aggiungere la soluzione di monitoraggio VMware alla sottoscrizione tramite il processo descritto in [Aggiungere una soluzione di gestione](log-analytics-add-solutions.md#add-a-management-solution).

#### <a name="supported-vmware-esxi-hosts"></a>Host ESXi VMware supportati
vSphere ESXi Host 5.5, 6.0 e 6.5

#### <a name="prepare-a-linux-server"></a>Preparazione di un server Linux
Creare una VM del sistema operativo Linux per ricevere tutti i dati di Syslog dagli host ESXi. L'[agente Linux OMS](log-analytics-linux-agents.md) è il punto di raccolta di tutti i dati di Syslog dell'host ESXi. È possibile usare più host ESXi per inoltrare i log a un singolo server Linux, come nell'esempio seguente.  

   ![flusso Syslog](./media/log-analytics-vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Configurazione della raccolta di Syslog
1. Configurare l'inoltro di Syslog per VSphere. Per informazioni dettagliate utili a configurare l'inoltro di Syslog, consultare [Configurazione di Syslog su ESXi 5.0 e versioni successive (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Accedere a **Configurazione host ESXi** > **Software** > **Impostazioni avanzate** > **Syslog**.
   ![vsphereconfig](./media/log-analytics-vmware/vsphere1.png)  
2. Nel campo *Syslog.global.logHost*, aggiungere il server Linux e il numero di porta *1514*. Ad esempio, `tcp://hostname:1514` o `tcp://123.456.789.101:1514`
3. Aprire il firewall dell'host ESXi per Syslog. **Configurazione host ESXi** > **Software** > **Profilo di sicurezza** > **Firewall** e aprire **Proprietà**.  

    ![vspherefw](./media/log-analytics-vmware/vsphere2.png)  

    ![vspherefwproperties](./media/log-analytics-vmware/vsphere3.png)  
4. Controllare vSphere Console per verificare che Syslog sia configurato correttamente. Verificare che nell'host ESXI sia configurata la porta **1514**.
5. Scaricare e installare l'agente OMS per Linux sul server Linux. Per maggiori informazioni, vedere la [documentazione relativa all'agente OMS per Linux](https://github.com/Microsoft/OMS-Agent-for-Linux).
6. Dopo l'installazione dell'agente OMS per Linux, passare alla directory /etc/opt/microsoft/omsagent/sysconf/omsagent.d e copiare il file vmware_esxi.conf nella directory /etc/opt/microsoft/omsagent/conf/omsagent.d, quindi modificare il proprietario/il gruppo e le autorizzazioni del file. Ad esempio: 

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
7. Riavviare l'agente OMS per Linux eseguendo `sudo /opt/microsoft/omsagent/bin/service_control restart`.
8. Verificare la connettività tra il server Linux e l'host ESXi usando il `nc` comando nell'host ESXi. Ad esempio: 

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

9. Nel portale di Azure, cercare `VMware_CL` nei log. Quando Log Analytics raccoglie i dati di Syslog, viene mantenuto il formato di Syslog. Nel portale vengono acquisiti alcuni campi specifici, come *Hostname* e *ProcessName*.  

    ![type](./media/log-analytics-vmware/type.png)  

    Se i risultati della ricerca nei log sono simili all'immagine sopra, è tutto pronto per usare il dashboard della soluzione di monitoraggio VMware.  

## <a name="vmware-data-collection-details"></a>Informazioni dettagliate sulla raccolta di dati VMware
La soluzione di monitoraggio VMware raccoglie le varie metriche delle prestazioni e i vari dati di log dagli host ESXi usando gli agenti OMS per Linux che sono stati abilitati.

La tabella seguente mostra i metodi di raccolta di dati e altre informazioni dettagliate sul modo in cui vengono raccolti i dati.

| Piattaforma | Agente OMS per Linux | Agente SCOM | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati con il gruppo di gestione | Frequenza della raccolta |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |ogni 3 minuti |

Nella tabella seguente sono riportati esempi di campi di dati raccolti dalla soluzione di monitoraggio VMware:

| nome campo | Descrizione |
| --- | --- |
| Device_s |Dispositivi di archiviazione VMware |
| ESXIFailure_s |tipi di errore |
| EventTime_t |quando si è verificato l'evento |
| HostName_s |nome dell'host ESXi |
| Operation_s |creazione o eliminazione di una VM |
| ProcessName_s |nome dell'evento |
| ResourceId_s |nome dell'host VMware |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |stato SCSI VMware |
| SyslogMessage_s |dati Syslog |
| UserName_s |utente che ha creato o eliminato la VM |
| VMName_s |Nome della VM. |
| Computer |computer host |
| TimeGenerated |quando sono stati generati i dati |
| DataCenter_s |data center di VMware |
| StorageLatency_s |latenza di archiviazione (ms) |

## <a name="vmware-monitoring-solution-overview"></a>Panoramica della soluzione di monitoraggio VMware
Il riquadro VMware viene visualizzato nell'area di lavoro di Log Analytics. che fornisce una visualizzazione dettagliata degli errori. Quando si fa clic sul riquadro, si accede alla vista dashboard.

![riquadro](./media/log-analytics-vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Accedere alla vista dashboard
Nella vista dashboard di **VMware** i pannelli sono organizzati in base a questi criteri:

* Conteggio stato di errore
* Host principali per conteggio degli eventi
* Conteggi eventi principali
* Attività macchine virtuali
* Eventi dischi host ESXi

![solution1](./media/log-analytics-vmware/solutionview1-1.png)

![solution2](./media/log-analytics-vmware/solutionview1-2.png)

Fare clic su qualsiasi pannello per aprire il riquadro di ricerca di Log Analytics che mostra informazioni dettagliate specifiche per il pannello.

A questo punto, è possibile modificare la query di ricerca in qualcosa di specifico. Per informazioni dettagliate sulla creazione di ricerche nei log, vedere [Trovare dati con ricerche nei log in Log Analytics](log-analytics-log-searches.md).

#### <a name="find-esxi-host-events"></a>Individuazione degli eventi host ESXi
Un singolo host ESXi genera più log, in base ai loro processi. La soluzione di monitoraggio VMware li centralizza e riepiloga il conteggio degli eventi. Questa vista centralizzata aiuta a comprendere quale host ESXi ha un volume elevato di eventi e quali eventi si verificano più di frequente nell'ambiente.

![event](./media/log-analytics-vmware/events.png)

È possibile approfondire più nel dettaglio facendo clic su un host ESXi o un tipo di evento.

Quando si fa clic su un nome host ESXi, vengono visualizzate informazioni da tale host ESXi. Se si desidera restringere i risultati con il tipo di evento, aggiungere `“ProcessName_s=EVENT TYPE”` nella query di ricerca. È possibile selezionare **ProcessName** nel filtro di ricerca. In questo modo vengono ristrette le informazioni.

![drill](./media/log-analytics-vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Individuazione di elevate attività delle VM
È possibile creare ed eliminare una macchina virtuale su qualsiasi host ESXi. È utile per gli amministratori identificare il numero di VM create da un host ESXi. A sua volta, questo aiuta a comprendere la pianificazione delle prestazioni e della capacità. Quando si gestisce un ambiente, è indispensabile tenere traccia degli eventi di attività delle VM.

![drill](./media/log-analytics-vmware/vmactivities1.png)

Se si desidera visualizzare dati aggiuntivi sulla creazione di VM dell'host ESXi, fare clic sul nome di un host ESXi.

![drill](./media/log-analytics-vmware/createvm.png)

#### <a name="common-search-queries"></a>Query di ricerca comuni
La soluzione include altre query utili che aiutano a gestire gli host ESXi, ad esempio elevate quantità di spazio di archiviazione, latenza di archiviazione ed errori di percorso.

[!INCLUDE[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![query](./media/log-analytics-vmware/queries.png)


#### <a name="save-queries"></a>Salvataggio delle query
Il salvataggio delle query di ricerca è una funzionalità standard di Log Analytics e può aiutare a conservare eventuali query utili. Dopo aver creato una query che si ritiene utile, salvarla facendo clic su **Preferiti**. Salvando una query, è possibile usarla di nuovo in un secondo momento dalla pagina [Dashboard personale](log-analytics-dashboards.md) in cui è possibile creare i dashboard personalizzati.

![DockerDashboardView](./media/log-analytics-vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Creazione di avvisi da query
Dopo aver creato le query, è possibile usarle come avvisi nel caso in cui si verifichino eventi specifici. Per informazioni su come creare avvisi, vedere [Avvisi in Log Analytics](log-analytics-alerts.md). Per esempi di query di avviso e di altro tipo, vedere il post di blog [Monitor VMware using Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) (Monitoraggio di VMware con Log Analytics).

## <a name="frequently-asked-questions"></a>Domande frequenti
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>Cosa bisogna fare con l'impostazione esistente dell'host ESXi? Quali saranno le conseguenze per l'ambiente esistente?
La soluzione usa il meccanismo di inoltro del SysLog dell'host ESXi nativo. Non è necessario aggiungere software Microsoft nell'host ESXi per acquisire log. Non dovrebbero esserci conseguenze di grande entità per l'ambiente esistente. Tuttavia, è necessario impostare l'inoltro SysLog, una funzionalità ESXi.

### <a name="do-i-need-to-restart-my-esxi-host"></a>È necessario riavviare l'host ESXi?
No. Questo processo non richiede un riavvio. Talvolta, vSphere non aggiorna correttamente il SysLog. In tal caso, accedere all'host ESXi e ricaricare il SysLog. Anche qui, non è necessario riavviare l'host, pertanto questo processo non risulta problematico per l'ambiente.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>È possibile aumentare o diminuire il volume dei dati di log inviati a Log Analytics?
Sì. È possibile usare le impostazioni a livello di log dell'host ESXi in vSphere. La raccolta dei log è basata sul livello *info*. Pertanto, se si desidera controllare la creazione o l'eliminazione di VM, è necessario mantenere il livello *info* in HostId. Per altre informazioni, consultare la [Knowledge Base di VMware](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Perché HostId non fornisce dati a Log Analytics? Il registro è impostato su info.
Si è verificato un bug dell'host ESXi relativo al timestamp del SysLog. Per altre informazioni, consultare la [Knowledge Base di VMware](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). Dopo aver applicato la soluzione, HostId dovrebbe funzionare normalmente.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>È possibile avere più host ESXi che inoltrano dati syslog a una sola VM con agente OMS?
Sì. È possibile avere più host ESXi che inoltrano a una sola VM con agente OMS.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Perché non sono visualizzati dati che pervengono a Log Analytics?
Possono esserci diversi motivi:

* L'host ESXi non esegue correttamente il pushing dei dati della VM che esegue l'agente OMS. Per eseguire un test, seguire questa procedura:

  1. Per confermare, accedere all'host ESXi tramite connessione SSH ed eseguire il comando seguente: `nc -z ipaddressofVM 1514`

      Se il problema persiste, probabilmente le impostazioni di vSphere nella configurazione avanzata non sono corrette. Per informazioni sulla configurazione dell'host ESXi per l'inoltro nel SysLog, vedere come [configurare la raccolta nel SysLog](#configure-syslog-collection).
  2. Se la porta SysLog è connessa, ma non vengono comunque visualizzati dati, ricaricare il SysLog dell'host ESXi usando la connessione SSH per eseguire il comando seguente: ` esxcli system syslog reload`
* La VM con l'agente OMS agente non è impostata correttamente. Per effettuare un test, eseguire i passaggi seguenti:

  1. Log Analytics è in ascolto sulla porta 1514. Per verificare che sia aperta, usare il comando seguente: `netstat -a | grep 1514`
  2. La porta `1514/tcp` dovrebbe risultare aperta. In caso contrario, verificare che l'agente OMS sia installato correttamente. Se non è possibile visualizzare le informazioni sulla porta SysLog nella VM, significa che non è aperta.

    a. Verificare che l'agente OMS sia in esecuzione usando `ps -ef | grep oms`. Se non è in esecuzione, avviare il processo eseguendo il comando ` sudo /opt/microsoft/omsagent/bin/service_control start`

    b. Aprire il file `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf` .

    c. Verificare che l'impostazione di gruppo e utente sia valida e appropriata, come nella stringa seguente: `-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

    d. Se il file non esiste o l'impostazione di utente e gruppo non è corretta, provvedere all'azione correttiva come descritto in [Preparazione di un server Linux](#prepare-a-linux-server).

## <a name="next-steps"></a>Passaggi successivi
* Usare le [ricerche nei log in Log Analytics](log-analytics-log-searches.md) per visualizzare i dati dettagliati di VMware.
* [Creare dashboard personalizzati](log-analytics-dashboards.md) che mostrino i dati dell'host VMware.
* [Creare avvisi](log-analytics-alerts.md) quando si verificano eventi specifici relativi all'host VMware.
