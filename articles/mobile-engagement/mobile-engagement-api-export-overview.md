---
title: Panoramica dell'API di esportazione di Mobile Engagement
description: Questo documento illustra le nozioni di base sull'esportazione dei dati non elaborati generati da dispositivi dell'utente per l'uso in strumenti personalizzati.
services: mobile-engagement
documentationcenter: mobile
author: kpiteira
manager: erikre
editor: ''
ms.assetid: 9380d47b-d7fa-4d4c-888f-97e6482196bb
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 04/26/2016
ms.author: kapiteir
ms.openlocfilehash: c1dc8a34abf84be3d7f59d41c36d90c2a67e5d32
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2018
---
# <a name="mobile-engagement-export-api-overview"></a>Panoramica dell'API di esportazione di Mobile Engagement
> [!IMPORTANT]
> Azure Mobile Engagement verrà ritirato il 31/03/2018. Questa pagina verrà eliminata subito dopo.
> 

## <a name="introduction"></a>Introduzione
In questo documento verranno illustrate le nozioni di base sull'esportazione dei dati non elaborati generati da dispositivi dell'utente per l'uso in strumenti personalizzati.

## <a name="pre-requisites"></a>Prerequisiti
Per esportare i dati non elaborati da Mobile Engagement, è necessario:

* Impostare l'autenticazione delle API in modo da poterle usare. Vedere l'[installazione manuale per l'autenticazione](mobile-engagement-api-authentication-manual.md).
* Usare le API REST o [.NET SDK](mobile-engagement-dotnet-sdk-service-api.md),
* Un account dell'Archiviazione di Azure.

> [!NOTE]
> È anche consigliabile avvalersi di [Esplora archivi di Microsoft Azure](http://storageexplorer.com/), almeno durante la fase di sviluppo in quanto offre un'interfaccia utente facile da usare per interagire con Archiviazione di Azure.
> 
> 

## <a name="what-can-be-exported"></a>Cosa può essere esportato
Mobile Engagement consente agli utenti di raccogliere molti tipi di dati e pertanto offre diversi tipi di esportazione appropriati per questi tipi di dati diversi.
Esistono due tipi fondamentali di esportazione:

* Snapshot: usata in genere per esportare i dati che rappresentano uno stato e per cui Mobile Engagement non ha una cronologia. Tali dati includono tag (app-info), token o commenti e suggerimenti di campagne push. Di conseguenza questi tipi di esportazione non sono correlati a una data.
* Cronologica: questo tipo di esportazione viene usato per i dati che si accumulano nel tempo, ad esempio eventi o attività.

Nella tabella seguente vengono descritte con attenzione tutte le esportazioni possibili:

| Export Type | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| Snapshot |Push |Genera un'esportazione di commenti e suggerimenti di campagne push in base all'ID dispositivo o all'utente |
| Snapshot |Tag |Genera un'esportazione dei tag (app-info) associati a ogni dispositivo |
| Snapshot |Dispositivo |Genera un'esportazione della maggior parte dei dati sui dispositivi, ad esempio i dati tecnici quali modello, impostazioni locali, fuso orario e così via, i tag, la data di produzione e altri ancora. |
| Snapshot |token |Genera un'esportazione di tutti i token validi |
| Cronologica |Attività |Genera un'esportazione di tutte le attività per ogni dispositivo in un determinato periodo di tempo |
| Cronologica |Event |Genera un'esportazione di tutte le attività per ogni dispositivo in un determinato periodo di tempo |
| Cronologica |Processo |Genera un'esportazione di tutti i processi per ogni dispositivo in un determinato periodo di tempo |
| Cronologica |Tipi di errore |Genera un'esportazione di tutti gli errori per ogni dispositivo in un determinato periodo di tempo |

## <a name="how-does-it-work"></a>Come funziona?
Le esportazioni sono attività a esecuzione prolungata che potrebbero generare file di dati di grandi dimensioni. Per questo motivo, la loro chiamata non può restituire immediatamente un file da scaricare.
Per esportare i dati da Mobile Engagement, è necessario creare un **processo di esportazione** tramite API in cui specificare:

* Il tipo di esportazione (snapshot o cronologica),
* Il tipo di dati,
* Il **contenitore dell'Archiviazione di Azure** , con inclusa una firma di accesso condiviso valida con accesso in scrittura, dove verranno scritti i risultati dell'esportazione.
* Il parametro dell'URL del contenitore sarà ad esempio https://[NomeAccountArchiviazione].blob.core.windows.net/[NomeContenitore]?[TokenAutorizzazioniScritturaFirmaAccessoCondiviso]  

Ecco un esempio reale: https://testazmeexport.blob.core.windows.net/test1234azme?sv=2015-12-11&ss=b&srt=sco&sp=rwdlac&se=2016-12-17T04:59:26Z&st=2016-12-16T20:59:26Z&spr=https&sig=KRF3aVWjp2NEJDzjlmoplmu0M9HHlLdkBWRPAFmw90Q%3D

Si noti che l'avvio del processo potrebbe richiedere alcuni minuti e che la sua esecuzione potrebbe richiedere da qualche secondo per le app molto piccole ad alcune ore per le app con diversi utenti e attività.

Una volta creato il processo, è possibile controllarne lo stato per vedere se è ancora in esecuzione o se è stato completato.

Una volta che il processo è stato completato correttamente, il file di dati risultante è disponibile nel contenitore di archiviazione specificato.

