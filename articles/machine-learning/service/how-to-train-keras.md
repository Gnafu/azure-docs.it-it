---
title: Training della rete neurale Deep Learning con keras
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire il training e registrare un modello di classificazione di rete neurale keras in esecuzione in TensorFlow usando Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: efa9b8f4f5cba36bfb2557b7be33ec9519b1d804
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999379"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning"></a>Eseguire il training e registrare un modello di classificazione keras con Azure Machine Learning

Questo articolo illustra come eseguire il training e la registrazione di un modello di classificazione keras basato su TensorFlow usando Azure Machine Learning. Usa il set di [dati MNIST](http://yann.lecun.com/exdb/mnist/) più diffusi per classificare le cifre scritte a mano usando una rete neurale profonda (DNN) creata usando la [libreria keras Python](https://keras.io) in esecuzione in [TensorFlow](https://www.tensorflow.org/overview).

Keras è un'API di rete neurale di alto livello in grado di eseguire la maggior parte degli altri Framework DNN più diffusi per semplificare lo sviluppo. Con Azure Machine Learning è possibile scalare rapidamente i processi di training usando le risorse di calcolo del cloud elastico. È anche possibile tenere traccia delle esecuzioni di training, dei modelli di versione, della distribuzione dei modelli e molto altro ancora.

Indipendentemente dal fatto che si stia sviluppando un modello keras da zero o si stia introducendo un modello esistente nel cloud, Azure Machine Learning possibile creare modelli pronti per la produzione.

Per informazioni sulle differenze tra Machine Learning e Deep Learning, vedere l' [articolo concettuale](concept-deep-learning-vs-machine-learning.md) .

## <a name="prerequisites"></a>Prerequisiti

Eseguire questo codice in uno degli ambienti seguenti:

 - Azure Machine Learning macchina virtuale Notebook-nessun download o installazione necessaria

     - Completare l'[Esercitazione: Configurare l'ambiente e](tutorial-1st-experiment-sdk-setup.md) l'area di lavoro per creare un server notebook dedicato precaricato con l'SDK e il repository di esempio.
    - Nella cartella Samples nel server notebook trovare un notebook completato e espanso passando a questa directory: **How-to-use-azureml > Training-with-Deep-learning > Train-iperparameter-Tune-deploy-with-keras** Folder.

 - Server Jupyter Notebook personale

    - [Installare Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Creare un file di configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).
    - [Scaricare i file script di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` e`utils.py`

    È anche possibile trovare una [versione di Jupyter notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) completata di questa guida nella pagina degli esempi di GitHub. Il notebook include sezioni espanse che coprono l'ottimizzazione intelligente dei parametri, la distribuzione di modelli e i widget del notebook.

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

### <a name="create-an-experiment"></a>Creare un esperimento

Creare un esperimento e una cartella che contengano gli script di training. In questo esempio, creare un esperimento denominato "keras-mnist".

```Python
script_folder = './keras-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='keras-mnist')
```

### <a name="upload-dataset-and-scripts"></a>Caricare DataSet e script

L' [Archivio](how-to-access-data.md) dati è una posizione in cui è possibile archiviare e accedere ai dati montando o copiando i dati nella destinazione di calcolo. Ogni area di lavoro fornisce un archivio dati predefinito. Caricare i dati e gli script di training nell'archivio dati in modo che possano essere facilmente accessibili durante il training.

1. Scaricare il set di dati MNIST localmente.

    ```Python
    os.makedirs('./data/mnist', exist_ok=True)

    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename = './data/mnist/train-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename = './data/mnist/train-labels.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/mnist/test-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename = './data/mnist/test-labels.gz')
    ```

1. Caricare il set di dati MNIST nell'archivio dati predefinito.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/mnist', target_path='mnist', overwrite=True, show_progress=True)
    ```

1. Caricare lo script di training di `keras_mnist.py`keras, e il `utils.py`file helper.

    ```Python
    shutil.copy('./keras_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="create-a-compute-target"></a>Creare una destinazione di calcolo

Creare una destinazione di calcolo per l'esecuzione del processo TensorFlow. In questo esempio, creare un cluster di calcolo Azure Machine Learning abilitato per GPU.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Per altre informazioni sulle destinazioni di calcolo, vedere l'articolo [che cos'è un calcolo di destinazione](concept-compute-target.md) .

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>Creare un TensorFlow Estimator e importare keras

[TensorFlow Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) fornisce un modo semplice per avviare i processi di training di TensorFlow nella destinazione di calcolo. Poiché keras viene eseguito su TensorFlow, è possibile usare lo strumento di stima TensorFlow e importare la libreria keras usando `pip_packages` l'argomento.

Lo strumento di stima TensorFlow viene implementato tramite [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) la classe generica, che può essere usata per supportare qualsiasi Framework. Inoltre, creare un dizionario `script_params` che contiene le impostazioni dell'iperparametro DNN. Per altre informazioni sui modelli di training con lo strumento di stima generico, vedere eseguire il training dei [modelli con Azure Machine Learning usando Estimator](how-to-train-ml-models.md)

```Python
script_params = {
    '--data-folder': ds.path('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.001
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='keras_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 pip_packages=['keras', 'matplotlib'],
                 use_gpu=True)
```

## <a name="submit-a-run"></a>Invia un'esecuzione

L' [oggetto Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornisce l'interfaccia alla cronologia di esecuzione mentre il processo è in esecuzione e dopo il completamento.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Quando l'esecuzione viene eseguita, vengono eseguite le fasi seguenti:

- **Preparazione**in corso: Viene creata un'immagine Docker in base allo strumento di stima TensorFlow. L'immagine viene caricata nel registro contenitori dell'area di lavoro e memorizzata nella cache per le esecuzioni successive. Anche i log vengono trasmessi alla cronologia di esecuzione e possono essere visualizzati per monitorare lo stato di avanzamento.

- **Ridimensionamento**: Il cluster tenta di eseguire la scalabilità verticale se il cluster batch per intelligenza artificiale richiede un numero maggiore di nodi per eseguire l'esecuzione che attualmente sono disponibili.

- **In esecuzione**: Tutti gli script nella cartella script vengono caricati nella destinazione di calcolo, gli archivi dati vengono montati o copiati e viene eseguito entry_script. Gli output da stdout e la cartella./logs vengono trasmessi alla cronologia di esecuzione e possono essere usati per monitorare l'esecuzione.

- **Post-elaborazione**: La cartella./Outputs dell'esecuzione viene copiata nella cronologia di esecuzione.

## <a name="register-the-model"></a>Registrare il modello

Dopo aver eseguito il training del modello DNN, è possibile registrarlo nell'area di lavoro. Con la registrazione del modello è possibile archiviare e deversionere i modelli nell'area di lavoro per semplificare la [gestione e la distribuzione del modello](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

È anche possibile scaricare una copia locale del modello. Questa operazione può essere utile per eseguire altre operazioni di convalida del modello localmente. Nello script di training, `mnist-keras.py`un oggetto TensorFlow Saver Salva in modo permanente il modello in una cartella locale (locale nella destinazione di calcolo). È possibile usare l'oggetto Run per scaricare una copia dall'archivio dati.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato eseguito il training e la registrazione di un modello keras in Azure Machine Learning. Per informazioni su come distribuire un modello, continuare con l'articolo sulla distribuzione del modello.

> [!div class="nextstepaction"]
> [Come e dove distribuire i modelli](how-to-deploy-and-where.md)
* [Tracciare le metriche di esecuzione durante il training](how-to-track-experiments.md)
* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)
* [Distribuire un modello con training](how-to-deploy-and-where.md)
* [Architettura di riferimento per il training di Deep learning distribuito in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
