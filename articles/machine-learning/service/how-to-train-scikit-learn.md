---
title: Training di modelli di Machine Learning con Scikit-learn
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire gli script di training di Scikit-learn su scala aziendale usando la classe SKlearn Estimator di Azure Machine Learning. Gli script di esempio classificano le immagini fiore Iris per creare un modello di machine learning basato sul set di dati Iris Scikit-learn.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/02/2019
ms.custom: seodec18
ms.openlocfilehash: 2b05ba7e4d38b596bdf76655fad0736425f8ce89
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002527"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Creazione di modelli Scikit-learn su larga scala con Azure Machine Learning

Questo articolo illustra come eseguire gli script di training di Scikit-learn su scala aziendale usando la classe [SKlearn Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) di Azure Machine Learning. 

Gli script di esempio in questo articolo vengono usati per classificare le immagini Flower Iris per creare un modello di machine learning basato sul [set di dati Iris](https://archive.ics.uci.edu/ml/datasets/iris)Scikit-learn.

Che tu stia eseguendo il training di un modello Scikit-learn di machine learning da zero o che crei un modello esistente nel cloud, puoi usare Azure Machine Learning per scalare in orizzontale processi di training open source con risorse di calcolo elastiche per il cloud. Con Azure Machine Learning è possibile creare, distribuire, eseguire la versione e monitorare i modelli a livello di produzione.

## <a name="prerequisites"></a>Prerequisiti

Eseguire questo codice in uno degli ambienti seguenti:
 - Azure Machine Learning macchina virtuale Notebook-nessun download o installazione necessaria

    - Completare l'[Esercitazione: Configurare l'ambiente e](tutorial-1st-experiment-sdk-setup.md) l'area di lavoro per creare un server notebook dedicato precaricato con l'SDK e il repository di esempio.
    - Nella cartella Training degli esempi sul server notebook trovare un notebook completato e espanso passando a questa directory: **How-to-use-azureml > training > Train-iperparameter-Tune-deploy-with-sklearn** Folder.

 - Server Jupyter Notebook personale

    - [Installare Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Creare un file di configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).
    - Scaricare il set di dati e il file di script di esempio 
        - [set di dati Iris](https://archive.ics.uci.edu/ml/datasets/iris)
        - [`train_iris.py`](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn)
    - È anche possibile trovare una [versione di Jupyter notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) completata di questa guida nella pagina degli esempi di GitHub. Il notebook include una sezione espansa che illustra l'ottimizzazione di iperparametri intelligenti e il recupero del modello migliore in base alle metriche primarie.

## <a name="set-up-the-experiment"></a>Configurare l'esperimento

Questa sezione Configura l'esperimento di training caricando i pacchetti Python necessari, inizializzando un'area di lavoro, creando un esperimento e caricando i dati di training e gli script di training.

### <a name="import-packages"></a>Importare pacchetti

Prima di tutto, importare le librerie Python necessarie.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inizializzare un'area di lavoro

L' [area di lavoro Azure Machine Learning](concept-workspace.md) è la risorsa di primo livello per il servizio. Offre una posizione centralizzata per lavorare con tutti gli artefatti creati. In Python SDK è possibile accedere agli elementi dell'area di lavoro creando un [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) oggetto.

Creare un oggetto dell'area di `config.json` lavoro dal file creato nella [sezione Prerequisiti](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>Creare un esperimento di Machine Learning

Creare un esperimento e una cartella che contengano gli script di training. In questo esempio, creare un esperimento denominato "sklearn-Iris".

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="upload-dataset-and-scripts"></a>Caricare DataSet e script

L' [Archivio](how-to-access-data.md) dati è una posizione in cui è possibile archiviare e accedere ai dati montando o copiando i dati nella destinazione di calcolo. Ogni area di lavoro fornisce un archivio dati predefinito. Caricare i dati e gli script di training nell'archivio dati in modo che possano essere facilmente accessibili durante il training.

1. Creare la directory per i dati.

    ```Python
    os.makedirs('./data/iris', exist_ok=True)
    ```

1. Caricare il set di dati Iris nell'archivio dati predefinito.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/iris', target_path='iris', overwrite=True, show_progress=True)
    ```

1. Caricare lo script di formazione Scikit-learn `train_iris.py`,.

    ```Python
    shutil.copy('./train_iris.py', project_folder)
    ```

## <a name="create-or-get-a-compute-target"></a>Creare o ottenere una destinazione di calcolo

Creare una destinazione di calcolo per il processo Scikit-learn da eseguire in. Scikit-learn supporta solo il singolo nodo, il calcolo della CPU.

Il codice seguente crea una Azure Machine Learning calcolo gestito (AmlCompute) per la risorsa di calcolo di training remoto. La creazione di AmlCompute richiede circa 5 minuti. Se il AmlCompute con lo stesso nome è già presente nell'area di lavoro, questo codice ignorerà il processo di creazione.

```Python
cluster_name = "cpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Per altre informazioni sulle destinazioni di calcolo, vedere l'articolo [che cos'è un calcolo di destinazione](concept-compute-target.md) .

## <a name="create-a-scikit-learn-estimator"></a>Creare un Scikit-learn Estimator

[Scikit-learn Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) fornisce un modo semplice per avviare un processo di formazione Scikit-learn in una destinazione di calcolo. Viene implementato tramite la classe [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) , che può essere utilizzata per supportare la formazione della CPU a nodo singolo.

Se lo script di training richiede l'esecuzione di pacchetti PIP o conda aggiuntivi, è possibile fare in modo che i pacchetti siano installati nell'immagine Docker risultante passando `pip_packages` i `conda_packages` relativi nomi tramite gli argomenti e.

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    '--kernel': 'linear',
    '--penalty': 1.0,
}

estimator = SKLearn(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train_iris.py'
                    pip_packages=['joblib']
                   )
```

## <a name="submit-a-run"></a>Invia un'esecuzione

L' [oggetto Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornisce l'interfaccia alla cronologia di esecuzione mentre il processo è in esecuzione e dopo il completamento.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Quando l'esecuzione viene eseguita, vengono eseguite le fasi seguenti:

- **Preparazione**in corso: Viene creata un'immagine Docker in base allo strumento di stima TensorFlow. L'immagine viene caricata nel registro contenitori dell'area di lavoro e memorizzata nella cache per le esecuzioni successive. Anche i log vengono trasmessi alla cronologia di esecuzione e possono essere visualizzati per monitorare lo stato di avanzamento.

- **Ridimensionamento**: Il cluster tenta di eseguire la scalabilità verticale se il cluster batch per intelligenza artificiale richiede un numero maggiore di nodi per eseguire l'esecuzione che attualmente sono disponibili.

- **In esecuzione**: Tutti gli script nella cartella script vengono caricati nella destinazione di calcolo, gli archivi dati vengono montati o copiati e viene eseguito entry_script. Gli output da stdout e la cartella./logs vengono trasmessi alla cronologia di esecuzione e possono essere usati per monitorare l'esecuzione.

- **Post-elaborazione**: La cartella./Outputs dell'esecuzione viene copiata nella cronologia di esecuzione.

## <a name="save-and-register-the-model"></a>Salvare e registrare il modello

Dopo aver eseguito il training del modello, è possibile salvarlo e registrarlo nell'area di lavoro. Con la registrazione del modello è possibile archiviare e deversionere i modelli nell'area di lavoro per semplificare la [gestione e la distribuzione del modello](concept-model-management-and-deployment.md).

Aggiungere il codice seguente allo script di training, train_iris. py, per salvare il modello. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Registrare il modello nell'area di lavoro con il codice seguente.

```Python
model = run.register_model(model_name='sklearn-iris', model_path='model.joblib')
```

## <a name="next-steps"></a>Passaggi successivi


In questo articolo è stato eseguito il training e la registrazione di un modello keras in Azure Machine Learning. Per informazioni su come distribuire un modello, continuare con l'articolo sulla distribuzione del modello.

> [!div class="nextstepaction"]
> [Come e dove distribuire i modelli](how-to-deploy-and-where.md)
* [Tracciare le metriche di esecuzione durante il training](how-to-track-experiments.md)
* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)
* [Distribuire un modello con training](how-to-deploy-and-where.md)
* [Architettura di riferimento per il training di Deep learning distribuito in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
