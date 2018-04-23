---
title: Aggiungere soluzioni di gestione di cluster HDInsight in Azure Log Analytics | Microsoft Docs
description: Informazioni su come usare Azure Log Analytics per creare visualizzazioni personalizzate per i cluster HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: caa10682f8419cfbc0837e5069357e02ee1e5f64
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="add-hdinsight-cluster-management-solutions-to-log-analytics"></a>Aggiungere soluzioni di gestione di cluster HDInsight in Log Analytics

HDInsight offre soluzioni di gestione specifiche per i cluster che è possibile aggiungere per Azure Log Analytics. Le [soluzioni di gestione](../log-analytics/log-analytics-add-solutions.md) aggiungono funzionalità a Log Analytics e forniscono altri strumenti di analisi e dati. Queste soluzioni raccolgono metriche importanti sulle prestazioni dai cluster HDInsight e forniscono gli strumenti per cercare le metriche. Le soluzioni forniscono anche visualizzazioni e dashboard per la maggior parte dei tipi di cluster supportati in HDInsight. Usando le metriche raccolte con la soluzione, è possibile creare regole e avvisi di monitoraggio personalizzati. 

Questo articolo descrive come aggiungere soluzioni di gestione specifiche per i cluster in un'area di lavoro di Log Analytics.

## <a name="prerequisites"></a>prerequisiti

* È necessario aver configurato un cluster HDInsight per l'uso di Azure Log Analytics. Per istruzioni, vedere [Usare Azure Log Analytics con i cluster HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="add-cluster-specific-management-solutions"></a>Aggiungere soluzioni di gestione specifiche per i cluster

In questa sezione si aggiunge una soluzione di gestione di cluster HBase in un'area di lavoro di Log Analytics esistente.

1. Aprire un cluster HDInsight nel portale di Azure, fare clic su **Monitoraggio** e quindi su **Aprire il dashboard OMS**.

    ![Aprire il dashboard Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-log-analytics-open-oms-dashboard.png "Aprire il dashboard OMS")

1. Nel dashboard fare clic su **Raccolta soluzioni** o sull'icona **Visualizza finestra di progettazione** nel riquadro sinistro.

    ![Aggiungere una soluzione di gestione in Log Analytics](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-add-management-solution-oms-portal.png "Aggiungere una soluzione di gestione in Operations Management Suite")

2. Nella Raccolta soluzioni, fare clic su uno dei seguenti riquadri:

    - Monitoraggio di Hadoop HDInsight
    - Monitoraggio Hbase in HDInsight (anteprima)
    - Monitoraggio Kafka in HDInsight
    - Monitoraggio Storm in HDInsight
    - Monitoraggio Spark in HDInsight

3. Nella schermata successiva fare clic su **Aggiungi**.  La schermata seguente mostra il pulsante Aggiungi per il monitoraggio di HBase.

     ![Aggiungere una soluzione di gestione HBase](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/add-hbase-management-solution.png "Aggiungere una soluzione di gestione HBase")

4. Sul dashboard viene visualizzato un riquadro per la soluzione di gestione HBase. Se il cluster associato a Operations Management Suite (come parte dei prerequisiti per questo articolo) è un cluster HBase, il riquadro mostra il nome del cluster e il numero di nodi nel cluster.

    ![Soluzione di gestione HBase aggiunta](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/added-hbase-management-solution.png "Soluzione di gestione HBase aggiunta")

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire query in Azure Log Analytics per monitorare i cluster HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

## <a name="see-also"></a>Vedere anche 

* [Uso di Log Analytics](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Creare regole di avviso in Log Analytics](../log-analytics/log-analytics-alerts-creating.md)
