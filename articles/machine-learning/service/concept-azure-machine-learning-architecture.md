---
title: Architettura & concetti chiave
titleSuffix: Azure Machine Learning
description: Informazioni sull'architettura, i termini, i concetti e i flussi di lavoro che costituiscono Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 07/12/2019
ms.custom: seodec18
ms.openlocfilehash: 85ca03bee728ec075383566be14d2484dd7431af
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170444"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Funzionamento di Azure Machine Learning: Architettura e concetti

Informazioni sull'architettura, i concetti e il flusso di lavoro per Azure Machine Learning. Il diagramma seguente illustra i componenti principali del servizio e il flusso di lavoro generale durante il suo uso:

![Architettura e flusso di lavoro Azure Machine Learning](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>Flusso di lavoro

Il flusso di lavoro del modello di Machine Learning segue generalmente questa sequenza:

1. **Treno**
    + Sviluppare script di training di Machine Learning in **Python** o con l'interfaccia visiva.
    + Creare e configurare una **destinazione di calcolo**.
    + **Inviare gli script** alla destinazione di calcolo configurata per l'esecuzione in tale ambiente. Durante il training, gli script possono leggere o scrivere nell'**archivio dati**. I record di esecuzione inoltre vengono salvati come **esecuzioni** nell'**area di lavoro** e raggruppati negli **esperimenti**.

1. **Pacchetto** : dopo che è stata rilevata un'esecuzione soddisfacente, registrare il modello permanente nel **Registro di sistema del modello**.

1.  - Convalidare**la query nell'esperimento per le** metriche registrate dalle esecuzioni correnti e passate. Se le metriche non indicano un risultato desiderato, tornare al passaggio 1 ed eseguire l'iterazione sugli script.

1. **Distribuisci** : sviluppa uno script di assegnazione dei punteggi che usa il modello e **distribuisce il modello** come **servizio Web** in Azure o in un **dispositivo IOT Edge**.

1. **Monitoraggio** : monitorare la **tendenza dei dati** tra il set di dati di training e i dati di inferenza di un modello distribuito. Quando necessario, eseguire il loopback al passaggio 1 per ripetere il training del modello con i nuovi dati di training.

## <a name="tools-for-azure-machine-learning"></a>Strumenti per Azure Machine Learning

Usare questi strumenti per Azure Machine Learning:

+  Interagire con il servizio in qualsiasi ambiente Python con [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Automatizzare le attività di Machine Learning con l'interfaccia della riga di comando [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli).
+ Scrivere codice in Visual Studio Code con [Azure Machine Learning estensione vs code](how-to-vscode-tools.md)
+ Usare l' [interfaccia visiva (anteprima) per Azure Machine Learning](ui-concept-visual-interface.md) per eseguire i passaggi del flusso di lavoro senza scrivere codice.

> [!NOTE]
> Sebbene in questo articolo vengano definiti i termini e i concetti utilizzati da Azure Machine Learning, non vengono definiti i termini e i concetti per la piattaforma Azure. Per altre informazioni sulla terminologia della piattaforma Azure, vedere il [glossario di Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="glossary"></a>Glossario

+ <a href="#workspaces">Area</a>
+ <a href="#experiments">Sperimentazioni</a>
+ <a href="#models">Modelli</a>
+ <a href="#run-configurations">Configurazione di esecuzione</a>
+ [Estimatori](#estimators)
+ <a href="#datasets-and-datastores">DataSet & archivi dati</a>
+ <a href="#compute-targets">Destinazioni di calcolo</a>
+ <a href="#training-scripts">Script di training</a>
+ <a href="#runs">Run</a>
+ <a href="#environments">Ambienti</a>
+ <a href="#github-tracking-and-integration">Rilevamento git</a>
+ <a href="#snapshots">Snapshot</a>
+ <a href="#activities">Attività</a>
+ <a href="#deployment">Distribuzione</a>
+ <a href="#web-service-deployments">Servizi Web</a>
+ <a href="#iot-module-deployments">Moduli delle cose</a>
+ <a href="#ml-pipelines">Pipeline ML</a>
+ <a href="#logging">registrazione</a>

### <a name="workspaces"></a>Aree di lavoro

[L'area di lavoro](concept-workspace.md) è la risorsa di primo livello per Azure Machine Learning. Fornisce una posizione centralizzata per lavorare con tutti gli artefatti creati quando si usa Azure Machine Learning. È possibile condividere un'area di lavoro con altri utenti. Per una descrizione dettagliata delle aree di lavoro, vedere [che cos'è un'area di lavoro Azure Machine Learning?](concept-workspace.md).

### <a name="experiments"></a>Esperimenti

Un esperimento è un raggruppamento di più esecuzioni da uno script specifico. Appartiene sempre a un'area di lavoro. Quando si invia un'esecuzione, si fornisce un nome dell'esperimento. Le informazioni per l'esecuzione vengono archiviate in tale esperimento. Se si invia un'esecuzione e si specifica un nome dell'esperimento che non esiste, viene creato automaticamente un nuovo esperimento il nuovo nome specificato.

Per un esempio dell'uso di un esperimento, [vedere Esercitazione: Eseguire il training del](tutorial-1st-experiment-sdk-train.md)primo modello.

### <a name="models"></a>Modelli

Nella forma più semplice, un modello è un frammento di codice che accetta un input e produce output. La creazione di un modello di Machine Learning prevede di selezionare un algoritmo, fornirgli dei dati e ottimizzare gli iperparametri. Il training è un processo iterativo che produce un modello con training, che racchiude ciò che il modello ha appreso durante il processo di training.

Un modello è prodotto da un'esecuzione in Azure Machine Learning. È anche possibile usare un modello il cui training sia stato eseguito all'esterno di Azure Machine Learning. È possibile registrare un modello in un'area di lavoro Azure Machine Learning.

Azure Machine Learning è indipendente dal framework. Quando si crea un modello, è possibile usare qualsiasi framework di Machine Learning comune, ad esempio Scikit-learn, XGBoost, PyTorch, TensorFlow e Chainer.

Per un esempio di training di un modello usando Scikit-learn e un Estimator [, vedere Esercitazione: Eseguire il training di un modello di](tutorial-train-models-with-aml.md)classificazione delle immagini con Azure Machine Learning.

Il **Registro di sistema del modello** tiene traccia di tutti i modelli nell'area di lavoro Azure Machine Learning.

I modelli vengono identificati dal nome e dalla versione. Ogni volta che si registra un modello con lo stesso nome di uno esistente, il registro presuppone che si tratti di una nuova versione. La versione viene incrementata e il nuovo modello registrato con lo stesso nome.

Quando si registra il modello, è possibile specificare altri tag di metadati e quindi usarli durante la ricerca di modelli.

> [!TIP]
> Un modello registrato è un contenitore logico per uno o più file che costituiscono il modello. Se, ad esempio, si dispone di un modello archiviato in più file, è possibile registrarli come singolo modello nell'area di lavoro Azure Machine Learning. Dopo la registrazione, è possibile scaricare o distribuire il modello registrato e ricevere tutti i file registrati.

Non è possibile eliminare un modello registrato utilizzato da una distribuzione attiva.

Per un esempio di registrazione di un modello, vedere [Eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="run-configurations"></a>Configurazioni di esecuzione

Una configurazione di esecuzione è un set di istruzioni che definisce come uno script deve essere eseguito in una destinazione di calcolo specificata. La configurazione include un'ampia serie di definizioni di comportamento, ad esempio se usare un ambiente Python esistente oppure un ambiente Conda compilato a partire da una specifica.

Una configurazione di esecuzione può essere salvata in modo permanente in un file all'interno della directory che contiene lo script di training, oppure costruita come un oggetto in memoria e usata per inviare un'esecuzione.

Per degli esempi di configurazioni di esecuzione, vedere [Selezionare e usare una destinazione di calcolo per eseguire il training del modello](how-to-set-up-training-targets.md).

### <a name="estimators"></a>Estimatori

Per facilitare il training del modello con i Framework più diffusi, la classe Estimator consente di costruire facilmente configurazioni di esecuzione. È possibile creare e usare uno strumento di [stima](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) generico per inviare script di training che usano qualsiasi framework di apprendimento scelto, ad esempio Scikit-learn.

Per le attività PyTorch, TensorFlow e Chainer, Azure Machine Learning fornisce anche i rispettivi estimatori [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)e [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) per semplificare l'uso di questi Framework.

Per altre informazioni, vedere gli articoli seguenti:

* [Training di modelli ml con estimatori](how-to-train-ml-models.md).
* Esegui il [training di modelli di Deep Learning Pytorch su larga scala con Azure Machine Learning](how-to-train-pytorch.md).
* Esegui il [training e la registrazione dei modelli TensorFlow su larga scala con Azure Machine Learning](how-to-train-tensorflow.md).
* Esegui il [training e la registrazione dei modelli di Chainer su larga scala con Azure Machine Learning](how-to-train-chainer.md).

### <a name="datasets-and-datastores"></a>DataSet e archivi dati

**Set di impostazioni Azure Machine Learning** (anteprima) semplifica l'accesso e l'utilizzo dei dati. I set di dati gestiscono i dati in diversi scenari, come il training del modello e la creazione della pipeline. Con Azure Machine Learning SDK è possibile accedere all'archiviazione sottostante, esplorare i dati e gestire il ciclo di vita delle diverse definizioni dei set di dati.

I set di dati forniscono metodi per lavorare con i dati nei formati più diffusi `from_delimited_files()` , `to_pandas_dataframe()`ad esempio usando o.

Per altre informazioni, vedere [creare e registrare Azure Machine Learning set](how-to-create-register-datasets.md)di dati.  Per altri esempi di uso dei set di impostazioni, vedere i [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets).

Un **archivio dati** è un'astrazione di archiviazione in un account di archiviazione di Azure. L'archivio dati può usare un contenitore BLOB o una condivisione file di Azure come risorsa di archiviazione back-end. Ogni area di lavoro presenta un archivio dati predefinito, ma è possibile registrare altri archivi dati. Usare l'API di Python SDK o l'interfaccia della riga di comando di Azure Machine Learning per archiviare e recuperare i file dall'archivio dati.

### <a name="compute-targets"></a>Destinazioni di calcolo

Una [destinazione di calcolo](concept-compute-target.md) consente di specificare la risorsa di calcolo in cui si esegue lo script di training o si ospita la distribuzione del servizio. Questo percorso può essere il computer locale o una risorsa di calcolo basata sul cloud. Le destinazioni di calcolo facilitano la modifica dell'ambiente di calcolo senza modificare il codice.

Altre informazioni sulle [destinazioni di calcolo disponibili per il training e la distribuzione](concept-compute-target.md).

### <a name="training-scripts"></a>Script di training

Per eseguire il training di un modello si specifica la directory che contiene lo script di training e i file associati. Si specifica inoltre un nome per l'esperimento, che viene usato per archiviare le informazioni raccolte durante il training. Durante il training l'intera directory viene copiata nell'ambiente di training (destinazione di calcolo) e lo script specificato per la configurazione di esecuzione viene avviato. Uno snapshot della directory viene archiviato anche nell'esperimento nell'area di lavoro.

Per un esempio, vedere [Esercitazione: Eseguire il training di un modello di](tutorial-train-models-with-aml.md)classificazione delle immagini con Azure Machine Learning.

### <a name="runs"></a>Esecuzioni

Un'esecuzione è una singola esecuzione di uno script di training. Azure Machine Learning registra tutte le esecuzioni e archivia le informazioni seguenti:

* Metadati sull'esecuzione (timestamp, durata e così via)
* Metriche registrate dallo script
* File di output raccolti automaticamente dall'esperimento o caricati in modo esplicito dall'utente
* Uno snapshot della directory che contiene gli script, precedente all'esecuzione

Un'esecuzione viene generata quando si invia uno script per eseguire il training di un modello. Un'esecuzione può avere zero o più esecuzioni figlio. L'esecuzione di primo livello, ad esempio, potrebbe avere due esecuzioni figlio, ognuna delle quali può avere a sua volte le proprie esecuzioni figlio.

### <a name="github-tracking-and-integration"></a>Rilevamento e integrazione di GitHub

Quando si avvia un'esecuzione di training in cui la directory di origine è un repository git locale, le informazioni sul repository vengono archiviate nella cronologia di esecuzione. Ad esempio, l'ID commit corrente per il repository viene registrato come parte della cronologia. Funziona con le esecuzioni inviate usando un estimatore, una pipeline ML o un'esecuzione di script. Funziona anche per le esecuzioni inviate dall'SDK o Machine Learning interfaccia della riga di comando.

### <a name="snapshots"></a>Snapshot

Quando si invia un'esecuzione, Azure Machine Learning consente di comprimere la directory che contiene lo script come file ZIP e lo invia alla destinazione di calcolo. Il file ZIP viene quindi estratto e lo script eseguito in questa posizione. Azure Machine Learning archivia inoltre il file ZIP come snapshot come parte del record di esecuzione. Chiunque abbia accesso all'area di lavoro può esplorare un record di esecuzione e scaricare lo snapshot.

> [!NOTE]
> Per evitare che i file non necessari vengano inclusi nello snapshot, creare un file ignore (con estensione gitignore o amlignore). Inserire questo file nella directory snapshot e aggiungere i nomi file da ignorare al suo interno. Il file con estensione amlignore utilizza la stessa [sintassi e gli stessi criteri del file. gitignore](https://git-scm.com/docs/gitignore). Se sono presenti entrambi i file, il file con estensione amlignore avrà la precedenza.

### <a name="activities"></a>Attività

Un'attività rappresenta un'operazione a esecuzione prolungata. Le operazioni seguenti sono esempi di attività:

* Creazione o eliminazione di una destinazione di calcolo
* Esecuzione di uno script su una destinazione di calcolo

Le attività possono fornire notifiche tramite l'SDK o l'interfaccia utente Web in modo da poter facilmente monitorare l'avanzamento di queste operazioni.

### <a name="environments"></a>Ambienti

Gli ambienti di Azure ML vengono usati per specificare la configurazione (Docker/Python/Spark/ecc.) usata per creare un ambiente riproducibile per la preparazione dei dati, il training del modello e il servizio del modello. Sono entità gestite e con versione all'interno dell'area di lavoro Azure Machine Learning che consentono flussi di lavoro riproducibili, controllabili e portatili di Machine Learning tra destinazioni di calcolo diverse.

È possibile usare un oggetto ambiente nel calcolo locale per sviluppare lo script di training, riutilizzare lo stesso ambiente in Azure Machine Learning calcolo per il training dei modelli su larga scala e persino distribuire il modello con lo stesso ambiente. 

Informazioni [su come creare e gestire un ambiente ml riutilizzabile per il](how-to-use-environments.md) training e l'inferenza.



### <a name="deployment"></a>Distribuzione

Una distribuzione è una creazione di istanze del modello in un servizio Web che può essere ospitato nel cloud o un modulo per le distribuzioni di dispositivi integrati.

#### <a name="web-service-deployments"></a>Distribuzioni di servizi Web

Un servizio Web distribuito può usare le Istanze di Azure Container, il servizio Azure Kubernetes o le FPGA. Il servizio viene creato dal modello, dallo script e dai file associati. Questi vengono incapsulati in un'immagine, che fornisce l'ambiente di runtime per il servizio Web. L'immagine presenta un endpoint HTTP con bilanciamento del carico che riceve le richieste di punteggio inviate al servizio Web.

Azure aiuta a monitorare la distribuzione del servizio Web raccogliendo i dati di telemetria di Application Insights o, se questa funzionalità è stata abilitata, i dati di telemetria del modello. I dati di telemetria sono accessibili solo per l'utente e archiviati in Application Insights e nelle istanze dell'account di archiviazione.

Se è stata abilitata la scalabilità automatica, la distribuzione verrà ridimensionata automaticamente da Azure.

Per un esempio di distribuzione di un modello come servizio Web, vedere [Distribuire un modello di classificazione delle immagini in Istanze di Azure Container](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-deployments"></a>Distribuzioni del modulo Internet

Un modulo IoT distribuito è un contenitore Docker che include il modello, lo script/associazione associato e qualsiasi altra dipendenza. Questi moduli vengono distribuiti usando Azure IoT Edge nei dispositivi perimetrali.

Se il monitoraggio è abilitato, Azure raccoglie i dati di telemetria dal modello all'interno del modulo Azure IoT Edge. I dati di telemetria sono accessibili solo per l'utente e archiviati nell'istanza dell'account di archiviazione.

Azure IoT Edge garantisce che il modulo sia in esecuzione e monitora il dispositivo che lo ospita.

### <a name="ml-pipelines"></a>Pipeline di Machine Learning

Le pipeline di Machine Learning consentono di creare e gestire i flussi di lavoro in cui sono unite le varie fasi dell'apprendimento automatico. Una pipeline, ad esempio, può includere la preparazione dei dati, il training del modello, la distribuzione del modello e le fasi di inferenza/punteggio. Ogni fase può includere più passaggi, ciascuno dei quali può essere eseguito in modo automatico in varie destinazioni di calcolo. 

I passaggi della pipeline sono riutilizzabili e possono essere eseguiti senza eseguire nuovamente i passaggi successivi se l'output del passaggio non è stato modificato. Ad esempio, è possibile ripetere il training di un modello senza rieseguire i passaggi di preparazione dei dati costosi se i dati non sono stati modificati. Le pipeline consentono inoltre ai data scientist di collaborare mentre lavorano su aree separate di un flusso di lavoro di machine learning.

Per altre informazioni sulle pipeline di apprendimento automatico, vedere [Pipeline e Azure Machine Learning](concept-ml-pipelines.md).

### <a name="logging"></a>Registrazione

Quando si sviluppa la soluzione, usare Python SDK di Azure Machine Learning nello script di Python per registrare metriche arbitrarie. Dopo l'esecuzione, eseguire query sulle metriche per determinare se l'esecuzione ha prodotto il modello che si desidera distribuire.

### <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare Azure Machine Learning, vedere:

* [Cos'è Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Creare un'area di lavoro di Machine Learning di Azure](how-to-manage-workspace.md)
* [Esercitazione (parte 1): Eseguire il training di un modello](tutorial-train-models-with-aml.md)
