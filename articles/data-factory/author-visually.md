---
title: Creazione di oggetti visivi in Azure Data Factory | Microsoft Docs
description: Informazioni su come usare la creazione di oggetti visivi in Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: e8b9aa3af6b15272e19589eb422016f4abd9380f
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883638"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Creazione di oggetti visivi in Azure Data Factory

L'interfaccia utente di Azure Data Factory consente di creare e distribuire con strumenti visivi le risorse della data factory senza dover scrivere il codice. È possibile trascinare le attività in un'area di disegno della pipeline, eseguire test, eseguire il debug in modo iterativo, nonché distribuire e monitorare le esecuzioni della pipeline.

## <a name="authoring-canvas"></a>Area di disegno di creazione

Per aprire l' **area di disegno di creazione e modifica**, fare clic sull'icona della matita. 

![Area di disegno di creazione](media/author-visually/authoring-canvas.png)

Qui sarà possibile creare pipeline, attività, set di dati, servizi collegati, flussi di dati, trigger e runtime di integrazione che comprendono la factory. Per iniziare a creare una pipeline usando l'area di disegno di creazione, vedere [copiare i dati usando l'attività di copia](tutorial-copy-data-portal.md). 

L'esperienza di creazione visiva predefinita funziona direttamente con il servizio Data Factory. Azure Repos integrazione git o GitHub è supportata anche per consentire il controllo del codice sorgente e la collaborazione per il lavoro sulle pipeline di data factory. Per ulteriori informazioni sulle differenze tra queste esperienze di creazione, vedere [controllo del codice sorgente in Azure Data Factory](source-control.md).

## <a name="expressions-and-functions"></a>Espressioni e funzioni

Le espressioni e le funzioni possono essere usate al posto dei valori statici per specificare molte proprietà in Azure Data Factory.

Per specificare un'espressione per un valore di proprietà, selezionare **Aggiungi contenuto dinamico** oppure fare clic **ALT + P** per concentrarsi sul campo.

![Aggiungere il contenuto dinamico](media/author-visually/dynamic-content-1.png)

Verrà aperto il **Generatore di espressioni data factory** in cui è possibile compilare espressioni dalle variabili di sistema supportate, dall'output delle attività, dalle funzioni e da variabili o parametri specificati dall'utente. 

![Generatore di espressioni](media/author-visually/dynamic-content-2.png)

Per informazioni sul linguaggio delle espressioni, vedere [espressioni e funzioni in Azure Data Factory](control-flow-expression-language-functions.md).

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

Selezionare **Commenti e suggerimenti** per lasciare un commento sulle funzionalità o per notificare a Microsoft i problemi con gli strumenti:

![Commenti e suggerimenti](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul monitoraggio e sulla gestione delle pipeline, vedere [Monitorare e gestire pipeline a livello di codice](monitor-programmatically.md).
