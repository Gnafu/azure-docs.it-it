---
title: Dati di esempio nelle tabelle Hive di Azure HDInsight - Processo di analisi scientifica dei dati per i team
description: Eseguire il sottocampionamento dei dati archiviati nelle tabelle Hive di Azure HDInsight usando query Hive per ridurli a una dimensione più facilmente gestibile a scopo di analisi.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c417950e07ae3c6922aa260a3ef40d862870aa1e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61042884"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Dati di esempio nelle tabelle Hive di Azure HDInsight
Questo articolo descrive come eseguire il sottocampionamento dei dati archiviati nelle tabelle Hive di Azure HDInsight usando query Hive per ridurli a una dimensione più facilmente gestibile a scopo di analisi. Vengono analizzati i tre metodi di campionamento più comuni:

* Campionamento casuale uniforme
* Campionamento casuale per gruppi
* Campionamento stratificato

**Perché campionare i dati?**
Se il set di dati da analizzare è grande, è in genere opportuno sottocampionare i dati per ridurlo e ottenere dimensioni inferiori più facilmente gestibili ma comunque rappresentative. Il sottocampionamento facilita la comprensione e l'esplorazione dei dati, nonché la progettazione di funzionalità. Il suo ruolo nel Processo di analisi scientifica dei dati per i team consiste nell'abilitare la creazione relativa a prototipi di funzioni di elaborazione dei dati e di modelli di Machine Learning.

Questo campionamento è un passaggio del [Processo di analisi scientifica dei dati per i team (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="how-to-submit-hive-queries"></a>Come inviare query Hive
Le query Hive possono essere inviate dalla console della riga di comando di Hadoop nel nodo head del cluster Hadoop. Per effettuare questa operazione, accedere al nodo head del cluster Hadoop, aprire la console della riga di comando e inviare le query Hive da tale posizione. Per istruzioni su come inviare le query Hive nella console della riga di comando di Hadoop, vedere [Come inviare le query Hive](move-hive-tables.md#submit).

## <a name="uniform"></a> Campionamento casuale uniforme
Nel campionamento casuale uniforme tutte le righe del set di dati hanno la stessa possibilità di essere sottoposte a campionamento. Questo metodo può essere implementato aggiungendo un ulteriore campo casuale () al set di dati relativo alla query "select" interna e a quella "select" esterna che condizionano il campo casuale.

Di seguito è fornito un esempio di query:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, …, fieldN
    from
        (
        select
            field1, field2, …, fieldN, rand() as samplekey
        from <hive table name>
        )a
    where samplekey<='${hiveconf:sampleRate}'

In questo caso, `<sample rate, 0-1>` indica la proporzione di record che gli utenti desiderano campionare.

## <a name="group"></a> Campionamento casuale per gruppi
Quando si esegue il campionamento dei dati di categoria, è possibile scegliere di includere o di escludere tutte le istanze di un valore della variabile di categoria. Questo tipo di campionamento è chiamato "campionamento per gruppo". Se, ad esempio, si ha una variabile di categoria "*State*" con valori quali NY, MA, CA, NJ e PA, è possibile che l'utente voglia che i record di uno stesso stato siano sempre visualizzati insieme, che siano campionati o meno.

Di seguito è presentata una query di esempio che consente di eseguire il campionamento per gruppi:

    SET sampleRate=<sample rate, 0-1>;
    select
        b.field1, b.field2, …, b.catfield, …, b.fieldN
    from
        (
        select
            field1, field2, …, catfield, …, fieldN
        from <table name>
        )b
    join
        (
        select
            catfield
        from
            (
            select
                catfield, rand() as samplekey
            from <table name>
            group by catfield
            )a
        where samplekey<='${hiveconf:sampleRate}'
        )c
    on b.catfield=c.catfield

## <a name="stratified"></a> Campionamento stratificato
Il campionamento casuale è stratificato rispetto a una variabile di categoria nel caso in cui i campioni ottenuti presentino, per quella categoria, valori di proporzione equivalente a quelli del popolamento padre. Usando lo stesso esempio precedente, supponiamo che i dati presentino le seguenti osservazioni in base allo stato: NJ dispone di 100 osservazioni, NY di 60 osservazioni e WA di 300 osservazioni. Se si specifica che la proporzione del campionamento stratificato sia pari a 0,5, il campione ottenuto deve disporre all'incirca di 50, 30 e 150 osservazioni per NJ, NY e WA

Di seguito è fornito un esempio di query:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, field3, ..., fieldN, state
    from
        (
        select
            field1, field2, field3, ..., fieldN, state,
            count(*) over (partition by state) as state_cnt,
              rank() over (partition by state order by rand()) as state_rank
          from <table name>
        ) a
    where state_rank <= state_cnt*'${hiveconf:sampleRate}'


Per informazioni su metodi di campionamento più avanzati disponibili in Hive, vedere [Campionamento di LanguageManual](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).

