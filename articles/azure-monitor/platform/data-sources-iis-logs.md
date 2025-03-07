---
title: Log di IIS in Monitoraggio di Azure | Microsoft Docs
description: Internet Information Services (IIS) archivia le attività utente in file di log che possono essere raccolti da Monitoraggio di Azure.  Questo articolo descrive come configurare una raccolta di log di IIS e i dettagli dei record creati in Monitoraggio di Azure.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: cec5ff0a-01f5-4262-b2e8-e3db7b7467d2
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: bwren
ms.openlocfilehash: cc0fcbb2005ce2aaa70c9e1d2a9993d341169209
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814231"
---
# <a name="collect-iis-logs-in-azure-monitor"></a>Raccogliere i log di IIS in Monitoraggio di Azure
Internet Information Services (IIS) archivia le attività utente in file di log che possono essere raccolti da Monitoraggio di Azure e archiviati come [dati di log](data-platform.md).

![Log di IIS](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Configurazione dei log di IIS
Poiché Monitoraggio di Azure raccoglie le voci dai file di log creati da IIS, è necessario [configurare la registrazione in IIS](https://technet.microsoft.com/library/hh831775.aspx).

Monitoraggio di Azure supporta solo i file di log di IIS archiviati in formato W3C, mentre non supporta campi personalizzati o IIS Advanced Logging. Non raccoglie log in formato nativo NCSA o IIS.

Configurare i log di IIS in Monitoraggio di Azure dal [menu Impostazioni avanzate](agent-data-sources.md#configuring-data-sources).  Non occorre selezionare nessuna impostazione oltre a **Raccogli i file di log IIS in formato W3C**.


## <a name="data-collection"></a>Raccolta dei dati
Monitoraggio di Azure raccoglie le voci di log di IIS da ogni agente ogni volta che viene modificato il timestamp del log. Il log viene letto ogni **5 minuti**. Se per qualsiasi motivo IIS non aggiorna il timestamp prima dell'ora di rollover quando viene creato un nuovo file, le voci verranno raccolte in seguito alla creazione del nuovo file. La frequenza di creazione di nuovi file è controllata dall'impostazione della **pianificazione del rollover dei file di log** per il sito IIS, che è una volta al giorno per impostazione predefinita. Se l'impostazione è ogni **ora**, monitoraggio di Azure raccoglie il log ogni ora. Se l'impostazione è **giornaliera**, monitoraggio di Azure raccoglie il log ogni 24 ore.


## <a name="iis-log-record-properties"></a>Proprietà dei record del log di IIS
I record dei log di IIS sono di tipo **W3CIISLog**; la tabella seguente descrive le loro proprietà:

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| Computer |Nome del computer da cui è stato raccolto l'evento. |
| cIP |Indirizzo IP del client. |
| csMethod |Metodo della richiesta, ad esempio GET o POST. |
| csReferer |Sito dal quale l'utente è arrivato al sito corrente tramite un collegamento. |
| csUserAgent |Tipo di browser del client. |
| csUserName |Nome dell'utente autenticato che ha eseguito l'accesso al server. Gli utenti anonimi sono indicati da un trattino. |
| csUriStem |Destinazione della richiesta, ad esempio una pagina Web. |
| csUriQuery |Eventuale query che il client ha tentato di eseguire. |
| ManagementGroupName |Nome del gruppo di gestione per gli agenti di Operations Manager.  Per gli altri agenti, corrisponde ad AOI-\<ID area di lavoro\> |
| RemoteIPCountry |Paese/area geografica dell'indirizzo IP del client. |
| RemoteIPLatitude |Latitudine dell'indirizzo IP del client. |
| RemoteIPLongitude |Longitudine dell'indirizzo IP del client. |
| scStatus |Codice stato HTTP. |
| scSubStatus |Codice errore dello stato secondario. |
| scWin32Status |Codice di stato Windows. |
| sIP |Indirizzo IP del server Web. |
| SourceSystem |OpsMgr |
| sPort |Porta del server a cui è connesso il client. |
| sSiteName |Nome del sito IIS. |
| TimeGenerated |Data e ora che in cui la voce è stata registrata. |
| TimeTaken |Tempo impiegato per l'elaborazione della richiesta in millisecondi. |

## <a name="log-queries-with-iis-logs"></a>Query di log con i log di IIS
La tabella seguente mostra alcuni esempi di query nei log che recuperano i record dei log di IIS.

| Query | DESCRIZIONE |
|:--- |:--- |
| W3CIISLog |Tutti i record del log di IIS. |
| W3CIISLog &#124; where scStatus==500 |Tutti i record del log IIS con stato restituito pari a 500. |
| W3CIISLog &#124; summarize count() by cIP |Numero di voci del log di IIS in base all'indirizzo IP del client. |
| W3CIISLog &#124; where csHost = = "www\.contoso.com" &#124; riepiloga Count () by csUriStem |Numero di voci del log di IIS per URL per l'\.host www contoso.com. |
| W3CIISLog &#124; summarize sum(csBytes) by Computer &#124; take 500000 |Numero totale di byte ricevuti da ogni computer che esegue IIS. |

## <a name="next-steps"></a>Passaggi successivi
* Configurare Monitoraggio di Azure per raccogliere altre [origini dati](agent-data-sources.md) per l'analisi.
* Altre informazioni sulle [query di log](../log-query/log-query-overview.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni.
