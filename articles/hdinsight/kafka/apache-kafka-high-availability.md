---
title: Disponibilità elevata con Apache Kafka - Azure HDInsight
description: Informazioni su come garantire disponibilità elevata con Apache Kafka in Azure HDInsight. Questo articolo illustra come ribilanciare le repliche di partizione in Kafka in modo che siano incluse in domini di errore diversi all'interno dell'area di Azure contenente HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.openlocfilehash: 70843c368b0446a7c0e09559fa759a3cd51912d4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62115229"
---
# <a name="high-availability-of-your-data-with-apache-kafka-on-hdinsight"></a>Disponibilità elevata dei dati con Apache Kafka in HDInsight

Questo articolo illustra come configurare le repliche di partizione per gli argomenti Apache Kafka in modo da sfruttare la configurazione rack hardware sottostante, che garantisce la disponibilità dei dati archiviati in Apache Kafka in HDInsight.

## <a name="fault-and-update-domains-with-apache-kafka"></a>Domini di errore e di aggiornamento con Apache Kafka

Un dominio di errore è un raggruppamento logico dell'hardware sottostante in un data center di Azure. Ogni dominio di errore condivide una fonte di alimentazione e un commutatore di rete comuni. Le macchine virtuali e i dischi gestiti che implementano i nodi in un cluster HDInsight sono distribuiti tra i domini di errore. Questa architettura limita il potenziale impatto dei guasti dell'hardware fisico.

Ogni area di Azure include un numero specifico di domini di errore. Per un elenco dei domini e il numero dei domini di errore in essi contenuti, vedere la documentazione relativa ai [set di disponibilità](../../virtual-machines/windows/regions-and-availability.md#availability-sets).

> [!IMPORTANT]  
> Kafka non rileva i domini di errore. Quando si crea un argomento in Kafka, tutte le partizioni di replica potrebbero essere archiviate nello stesso dominio di errore. Per risolvere il problema, HDInsight fornisce lo [strumento per il ribilanciamento delle partizioni Kafka](https://github.com/hdinsight/hdinsight-kafka-tools).

## <a name="when-to-rebalance-partition-replicas"></a>Quando ribilanciare le repliche di partizione

Per garantire la massima disponibilità dei dati Kafka, è consigliabile ribilanciare le repliche di partizione per l'argomento nei momenti seguenti:

* Quando viene creato un nuovo argomento o una nuova partizione

* Quando si aumentano le prestazioni di un cluster

## <a name="replication-factor"></a>Fattore di replica

> [!IMPORTANT]  
> È consigliabile usare un'area di Azure contenente tre domini di errore e un fattore di replica di 3.

Se si deve usare un'area che contiene solo due domini di errore, usare un fattore di replica di 4 per distribuire uniformemente le repliche tra i due domini di errore.

Per un esempio della creazione di argomenti e dell'impostazione del fattore di replica, vedere il documento [Iniziare a usare Apache Kafka in HDInsight](apache-kafka-get-started.md).

## <a name="how-to-rebalance-partition-replicas"></a>Come ribilanciare le repliche di partizione

Usare lo [strumento per il ribilanciamento delle partizioni Apache Kafka](https://github.com/hdinsight/hdinsight-kafka-tools) per ribilanciare gli argomenti selezionati. Questo strumento deve essere eseguito da una sessione SSH al nodo head del cluster Kafka.

Per altre informazioni sulla connessione a HDInsight con SSH, vedere il documento su come [usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="next-steps"></a>Passaggi successivi

* [Scalabilità di Apache Kafka in HDInsight](apache-kafka-scalability.md)
* [Mirroring con Apache Kafka in HDInsight](apache-kafka-mirroring.md)
