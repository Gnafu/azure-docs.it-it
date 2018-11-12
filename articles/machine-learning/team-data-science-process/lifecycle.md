---
title: Ciclo di vita del processo di data science per i team - Azure | Microsoft Docs
description: Procedura necessaria per eseguire i progetti di data science
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 611793297559c99ffa46f7c0266ed6865eb650fd
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51234273"
---
# <a name="the-team-data-science-process-lifecycle"></a>Ciclo di vita del processo di data science per i team

Il processo di data science per i team (TDSP, Team Data Science Process) fornisce un ciclo di vita consigliato da usare per strutturare i progetti di data science. Il ciclo di vita descrive tutti i passaggi generalmente seguiti dai progetti in fase di esecuzione. Se si usa un altro ciclo di vita di data science, ad esempio il CRISP-DM [(Cross Industry Standard Process for Data Mining)](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), il KDD [(Knowledge Discovery in Databases)](https://wikipedia.org/wiki/Data_mining#Process), o il processo personalizzato dell'organizzazione, è comunque possibile usare il TDSP basato sulle attività. 

Questo ciclo di vita è stato messo a punto per i progetti di data science destinati a far parte di applicazioni intelligenti. Queste applicazioni distribuiscono modelli di Machine Learning o intelligenza artificiale per l'analisi predittiva. Anche i progetti di data science esplorativi e i progetti analitici ad hoc possono trarre vantaggio dall'uso di questo processo. Tuttavia per tali progetti potrebbero non essere necessarie alcune delle procedure descritte di seguito. 

Il ciclo di vita TDSP è composto da cinque fasi principali che vengono eseguite in modo iterativo. Queste fasi includono:

   1. [Comprensione del business](lifecycle-business-understanding.md)
   2. [Acquisizione e comprensione dei dati](lifecycle-data.md)
   3. [Modellazione](lifecycle-modeling.md)
   4. [Distribuzione](lifecycle-deployment.md)
   5. [Accettazione del cliente](lifecycle-acceptance.md)

Ecco una rappresentazione visiva del ciclo di vita del processo TDSP: 

![Ciclo di vita del processo TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


Il ciclo di vita del TDSP viene modellato come sequenza di passaggi iterati che offrono indicazioni sulle attività necessarie per usare modelli predittivi. Distribuire i modelli predittivi nell'ambiente di produzione che si intende usare per compilare applicazioni intelligenti. L'obiettivo di questo ciclo di vita del processo consiste nel procedere con il progetto di data science verso un punto finale di evidente coinvolgimento. Con data science si intente un esercizio nell'ambito della ricerca e dell'individuazione. La possibilità di comunicare le attività al team e ai clienti usando un set ben definito di elementi che si avvalgono di modelli standard consente di evitare malintesi. L'uso di questi modelli aumenta inoltre la probabilità di completare correttamente un progetto di data science complesso.

Per ogni fase, specificare le informazioni seguenti:

   * **Obiettivi**: gli obiettivi specifici.
   * **Procedura**: una struttura delle attività specifiche e una guida su come completarle.
   * **Elementi**: i risultati finali e il supporto durante la produzione degli stessi.

## <a name="next-steps"></a>Passaggi successivi

Sono disponibili esercitazioni dettagliate complete che illustrano tutti i passaggi del processo per scenari specifici. L'articolo sulle [procedure dettagliate di esempio](walkthroughs.md) include un elenco degli scenari con i collegamenti e le descrizioni di anteprima. Le procedure dettagliate illustrano come combinare strumenti cloud, strumenti locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente. 

Per esempi su come eseguire i passaggi nei processi di data science per i team (TDSP) che usano Azure Machine Learning Studio, vedere [Uso del processo di analisi scientifica dei dati per i team con Azure Machine Learning](https://aka.ms/datascienceprocess).
