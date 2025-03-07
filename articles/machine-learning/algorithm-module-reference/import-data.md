---
title: 'Importa dati: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Import data nel servizio Azure Machine Learning per caricare dati in un esperimento di machine learning da servizi dati cloud esistenti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: e8522c759efb0cc599bbcf8e7569f6045da571a8
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128793"
---
# <a name="import-data-module"></a>Modulo Import Data

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Usare questo modulo per caricare dati in un esperimento di machine learning da servizi dati cloud esistenti.  
Il modulo ora include una procedura guidata che consente di scegliere un'opzione di archiviazione e di scegliere tra le sottoscrizioni e gli account esistenti per configurare rapidamente tutte le opzioni. Se è necessario modificare una connessione dati esistente, Nessun problema; la procedura guidata carica tutti i dettagli di configurazione precedenti in modo che non sia necessario riavviare da zero. 
  
Dopo aver definito i dati desiderati e averli connessi all'origine, l' [importazione dei dati](./import-data.md) deduce il tipo di dati di ogni colonna in base ai valori che contiene e carica i dati nell'area di lavoro Azure Machine Learning. L'output dei [dati di importazione](./import-data.md) è un set di dati che può essere usato con qualsiasi esperimento.

  
Se i dati di origine cambiano, è possibile aggiornare il set di dati e aggiungere nuovi dati rieseguendo [Import Data](./import-data.md). Tuttavia, se non si vuole rileggere dall'origine ogni volta che si esegue l'esperimento, selezionare l'opzione **USA risultati memorizzati nella cache** su true. Quando questa opzione è selezionata, il modulo controlla se l'esperimento è stato eseguito in precedenza utilizzando la stessa origine e le stesse opzioni di input. Se viene trovata un'esecuzione precedente, vengono usati i dati nella cache, invece di ricaricare i dati dall'origine.
 

## <a name="data-sources"></a>Origini dati

Il modulo Import Data (Importa dati) supporta le origini dati seguenti. Fare clic sui collegamenti per istruzioni dettagliate ed esempi sull'utilizzo di ogni origine dati. 
 
Se non si è certi di come o dove archiviare i dati, vedere questa guida agli scenari di dati comuni nel processo di data science:  [Scenari per l'analisi avanzata in Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-plan-sample-scenarios). 


|Origine dati| Usare con|
|-----------|-----------|  
|[URL Web tramite HTTP](./import-from-web-url-via-http.md)|Recuperare i dati ospitati in un URL Web che usa HTTP e che è stato fornito nei formati CSV, TSV, ARFF o SvmLight|  
|[Importare dall'archiviazione BLOB di Azure](./import-from-azure-blob-storage.md) |Recuperare i dati archiviati nel servizio BLOB di Azure|  

## <a name="how-to-use-import-data"></a>Come usare i dati di importazione
 
1. Aggiungere il modulo **Import Data (Importa dati** ) all'esperimento. È possibile trovare questo modulo nella categoria **input e output dei dati** nell'interfaccia.

2. Fare clic su **Avvia importazione guidata dati** per configurare l'origine dati utilizzando una procedura guidata.

    La procedura guidata ottiene il nome e le credenziali dell'account e consente di configurare altre opzioni. Se si sta modificando una configurazione esistente, vengono caricati prima i valori correnti.

3. Se non si vuole usare la procedura guidata, fare clic su **origine dati**e scegliere il tipo di archiviazione basata sul cloud da cui si sta leggendo. 

    Le impostazioni aggiuntive dipendono dal tipo di archiviazione scelto e dal fatto che la risorsa di archiviazione sia protetta o meno. Potrebbe essere necessario specificare il nome dell'account, il tipo di file o le credenziali. Per alcune origini non è richiesta l'autenticazione. per altri, potrebbe essere necessario individuare il nome dell'account, una chiave o un nome del contenitore.

4. Selezionare l'opzione **USA risultati memorizzati nella cache** se si desidera memorizzare nella cache il set di dati per il riutilizzo in esecuzioni successive.

    Supponendo che non siano state apportate altre modifiche ai parametri del modulo, l'esperimento carica i dati solo la prima volta che il modulo viene eseguito e successivamente usa una versione memorizzata nella cache del set di dati.

    Deselezionare questa opzione se è necessario ricaricare i dati ogni volta che si esegue l'esperimento.

5. Eseguire l'esperimento.

    Quando Import data carica i dati nell'interfaccia, deduce il tipo di dati di ogni colonna in base ai valori che contiene, sia numerici che categorici.

    - Se è presente un'intestazione, l'intestazione viene utilizzata per assegnare un nome alle colonne del set di dati di output.

    - Se nei dati non sono presenti intestazioni di colonna, i nuovi nomi di colonna vengono generati utilizzando il formato col1, Col2,... , Coln *.

## <a name="results"></a>Risultati

Al termine dell'importazione, fare clic sul set di dati di output e selezionare **Visualizza** per verificare se i dati sono stati importati correttamente.

Se si desidera salvare i dati per riutilizzarli, anziché importare un nuovo set di dati ogni volta che viene eseguito l'esperimento, fare clic con il pulsante destro del mouse sull'output e selezionare **Salva come set**di dati. Scegliere un nome per il set di dati. Il set di dati salvato conserva i dati al momento del salvataggio e i dati non vengono aggiornati quando l'esperimento viene rieseguito, anche se il set di dati nell'esperimento viene modificato. Questo può essere utile per acquisire snapshot dei dati.

Dopo aver importato i dati, potrebbero essere necessarie alcune preparazioni aggiuntive per la modellazione e l'analisi:


- Utilizzare [Modifica metadati](./edit-metadata.md) per modificare i nomi delle colonne, per gestire una colonna con un tipo di dati diverso o per indicare che alcune colonne sono etichette o funzionalità.

- Utilizzare [Seleziona colonne nel set di dati](./select-columns-in-dataset.md) per selezionare un subset di colonne da trasformare o utilizzare nella modellazione. È possibile riaggiungere facilmente le colonne trasformate o rimosse al set di dati originale usando il modulo [Add columns](./add-columns.md) .  

- Usare [Partition and Sample](./partition-and-sample.md) per dividere il set di dati, eseguire il campionamento o ottenere le prime n righe.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 