---
title: Risoluzione dei problemi del controllo degli accessi in base al ruolo di Azure (RBAC) | Microsoft Docs
description: Assistenza per problemi o domande sulle risorse del controllo degli accessi in base al ruolo.
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: seohack1
ms.openlocfilehash: d8dce26fb3f84dd3d7bd2c11972e3e440843bb75
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="troubleshooting-azure-role-based-access-control"></a>Risoluzione dei problemi del controllo degli accessi in base al ruolo di Azure 

Questo articolo contiene le risposte alle domande comuni sui diritti di accesso specifici concessi ai ruoli, in modo da sapere cosa accade quando si usano i ruoli nel Portale di Azure e risolvere i problemi di accesso. Questi tre ruoli coprono tutti i tipi di risorsa:

* Proprietario  
* Collaboratore  
* Reader  

Proprietari e collaboratori hanno accesso completo all'esperienza di gestione, ma il collaboratore non può concedere l'accesso ad altri utenti o gruppi. Il ruolo di lettore è maggiormente articolato e verrà quindi esaminato in maniera più approfondita. Per informazioni dettagliate su come concedere l'accesso, vedere [Controllo degli accessi in base al ruolo di Azure](role-assignments-portal.md) .

## <a name="app-service"></a>Servizio app
### <a name="write-access-capabilities"></a>Funzionalità di accesso in scrittura
Se si concede a un utente l'accesso in sola lettura a un'unica app Web, sono disabilitate alcune funzionalità non prevedibili. Per le funzionalità di gestione seguenti è necessario avere accesso **in scrittura** a un'app Web, come Collaboratore o Proprietario. Tali funzionalità non saranno disponibili in uno scenario di sola lettura.

* Comandi (quali avvio, interruzione e così via)
* Modifica di impostazioni quali la configurazione generale, le impostazioni di scalabilità, di backup e di monitoraggio.
* Accesso a credenziali di pubblicazione e altri segreti, quali le impostazioni delle app e le stringhe di connessione.
* Streaming dei log
* Configurazione dei log di diagnostica
* Console (prompt dei comandi)
* Distribuzioni attive e recenti, per la distribuzione continua del Git locale
* Spesa prevista
* Test Web
* Rete virtuale, visibile per un lettore solo se in precedenza era già stata configurata una rete virtuale da un utente con accesso in scrittura.

Se non è possibile accedere a nessuno di questi titoli, è necessario richiedere all'amministratore l'accesso come Collaboratore per l'app Web.

### <a name="dealing-with-related-resources"></a>Gestione delle risorse correlate
La complessità delle app Web è accentuata dalle interazioni tra alcune risorse diverse. Di seguito è illustrato un tipico gruppo di risorse con alcuni siti Web:

![Gruppo di risorse per app Web](./media/troubleshooting/website-resource-model.png)

Quindi, se si concede l'accesso solo all'app Web, la maggior parte delle funzionalità del pannello del sito Web nel portale di Azure viene disabilitata.

Questi elementi richiedono accesso **in scrittura** al **piano di servizio App** che corrisponde al sito Web:  

* Visualizzazione del piano tariffario dell'app Web, che può essere Free o Standard  
* Configurazione di scalabilità, ossia numero di istanze, dimensione della macchina virtuale, impostazioni di scalabilità automatica  
* Quote, ad esempio archiviazione, larghezza di banda e CPU  

Gli elementi seguenti richiedono accesso **in scrittura** all'intero **gruppo di risorse** che contiene il sito Web:  

* Certificati e associazioni SSL. I certificati SSL possono essere condivisi tra siti appartenenti allo stesso gruppo di risorse e area geografica.  
* Regole di avviso  
* Impostazioni di scalabilità automatica  
* Componenti di Application Insights  
* Test Web  

## <a name="azure-functions"></a>Funzioni di Azure
Alcune funzionalità di [Funzioni di Azure](../azure-functions/functions-overview.md) richiedono l'accesso in scrittura. Ad esempio un utente, a cui è stato assegnato il ruolo di lettore, non sarà in grado di visualizzare le funzioni all'interno di un'app per le funzioni. Sul portale verrà indicato **(Nessun accesso)**.

![Nessun accesso alle app per le funzioni](./media/troubleshooting/functionapps-noaccess.png)

Un lettore può fare clic sulla scheda **Funzionalità della piattaforma** e quindi su **Tutte le impostazioni** per visualizzare alcune impostazioni correlate a un'app per le funzioni (in modo simile a un'app Web), ma non può modificare nessuna impostazione.

## <a name="virtual-machine"></a>Macchina virtuale
Analogamente a quanto accade con le app Web, alcune funzionalità del blade della macchina virtuale richiedono l'accesso in scrittura alla macchina virtuale o ad altre risorse del gruppo di risorse.

Le macchine virtuali sono correlate a nomi di dominio, reti virtuali, account di archiviazione e regole di avviso.

Gli elementi seguenti richiedono accesso **in scrittura** alla **macchina virtuale**:

* Endpoint  
* Indirizzi IP  
* Dischi  
* Estensioni  

Gli elementi seguenti richiedono accesso **in scrittura** sia alla **macchina virtuale** che al **gruppo di risorse**, così come al nome di dominio a cui appartiene:  

* Set di disponibilità  
* Set con carico bilanciato  
* Regole di avviso  

Se non è possibile accedere a nessuno di questi riquadri, richiedere all'amministratore l'accesso come Collaboratore al gruppo di risorse.

## <a name="see-more"></a>Altro
* [Controllo degli accessi in base al ruolo](role-assignments-portal.md): introduzione al controllo degli accessi in base al ruolo nel portale di Azure.
* [Ruoli predefiniti](built-in-roles.md): informazioni dettagliate sui ruoli predefiniti del controllo degli accessi in base al ruolo.
* [Ruoli personalizzati nel controllo degli accessi in base al ruolo di Azure](custom-roles.md): informazioni su come creare ruoli personalizzati per esigenze di accesso specifiche.
* [Creare un report della cronologia delle modifiche relative all'accesso](change-history-report.md): monitoraggio delle modifiche nelle assegnazioni dei ruoli nel controllo degli accessi in base al ruolo.

