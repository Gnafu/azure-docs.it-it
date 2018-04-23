---
title: Apprendimento avanzato per scenari reali di manutenzione predittiva - Azure| Microsoft Docs
description: Si apprenderà come replicare l'esercitazione sull'apprendimento avanzato per la manutenzione predittiva con Azure Machine Learning Workbench.
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: ireiter
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.devlang: ''
ms.topic: article
ms.date: 11/22/2017
ms.openlocfilehash: 962a9d32790638af7513e805424d8579b29f433e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2018
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenarios"></a>Apprendimento avanzato per scenari reali di manutenzione predittiva

L'apprendimento avanzato è una delle tendenze più diffuse nell'ambito dell'apprendimento automatico e ha applicazioni in molte aree tra cui:
- Automobili senza conducente e robotica.
- Riconoscimento vocale e di immagini.
- Previsioni finanziarie.

Noti anche come Reti neurali profonde, questi metodi sono ispirati ai singoli neuroni all'interno del cervello, ovvero alle reti neurali biologiche.

L'impatto del tempo di inattività delle apparecchiature non pianificato può danneggiare un'azienda. È fondamentale mantenere in funzione le attrezzature sul campo per massimizzare l'uso e le prestazioni, riducendo il costoso tempo di inattività non pianificato. L'identificazione preventiva dei problemi può aiutare ad allocare una quantità limitata di risorse di manutenzione in modo più conveniente e migliorare la qualità e i processi della supply chain. 

Una strategia di manutenzione predittiva usa i metodi di apprendimento automatico per determinare la condizione delle attrezzature per eseguire preventivamente operazioni di manutenzione ed evitare cattive prestazioni del computer. Nella manutenzione predittiva i dati, raccolti nel tempo per monitorare lo stato del computer, vengono analizzati per individuare i criteri da usare per stimare gli errori. Le reti [Long Short Term Memory, ovvero LSTM](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) sono interessanti per questa impostazione, poiché sono state progettate per poter apprendere dalle sequenze di dati.

### <a name="cortana-intelligence-gallery-github-repository"></a>Repository GitHub di Cortana Intelligence Gallery

L'esercitazione su Cortana Intelligence Gallery per la manutenzione predittiva è un repository GitHub pubblico ([https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance)) in cui è possibile contribuire e segnalare problemi.


## <a name="use-case-overview"></a>Panoramica del caso d'uso

Questa esercitazione usa l'esempio di eventi "run-to-failure" simulati dei motori di un aereo per illustrare il processo di modellazione per la manutenzione predittiva. Lo scenario viene descritto in [Manutenzione predittiva](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3).

Il presupposto principale in questo caso è che il motore viene progressivamente ridotto per tutta la durata. La riduzione delle prestazioni può essere rilevata nelle misurazioni del sensore del motore. La manutenzione predittiva tenta di modellare la relazione tra le modifiche nei valori del sensore e gli errori cronologici. Il modello può quindi stimare quando potrebbero verificarsi errori nel motore in futuro in base allo stato attuale delle misurazioni del sensore.

Questo scenario crea una rete LSTM per stimare la vita utile rimanente dei motori degli aeroplani usando i valori cronologici del sensore. Lo scenario usa la libreria [Keras](https://keras.io/) con il framework di apprendimento avanzato [Tensorflow](https://www.tensorflow.org/) come motore di calcolo. Lo scenario esegue il training di LSTM con un set di motori e testa la rete su un set di motori non visibili.

## <a name="prerequisites"></a>prerequisiti
- Un [account di Azure](https://azure.microsoft.com/free/) (sono disponibili versioni di valutazione gratuite).
- Azure Machine Learning Workbench, con un'area di lavoro creata.
- Per l'operazionalizzazione del modello: operazionalizzazione di Azure Machine Learning con un ambiente di distribuzione locale configurato e un [account di Gestione modelli di Azure Machine Learning](model-management-overview.md).

## <a name="create-a-new-workbench-project"></a>Creare un nuovo progetto Workbench

Creare un nuovo progetto usando questo esempio come modello:

1. Aprire Machine Learning Workbench.
2. Nella pagina **Projects** (Progetti) selezionare **+** e quindi selezionare **New Project** (Nuovo progetto).
3. Nel riquadro **Create New Project** (Crea nuovo progetto) immettere le informazioni per il nuovo progetto.
4. Nella casella di ricerca **Cerca modelli di progetto** digitare "Manutenzione predittiva" e selezionare il modello **Deep Learning for Predictive Maintenance Scenario** (Apprendimento avanzato per scenari di manutenzione predittiva).
5. Selezionare **Create**.

## <a name="prepare-the-notebook-server-computation-target"></a>Preparare la destinazione di calcolo del server del notebook

Per l'esecuzione nel computer locale, dal menu **File** di Machine Learning Workbench scegliere **Open Command Prompt** (Apri prompt dei comandi) o **Open PowerShell CLI** (Apri interfaccia della riga di comando di PowerShell). L'interfaccia della riga di comando consente di accedere ai servizi di Azure usando i comandi `az`. Per prima cosa, accedere al proprio account di Azure con il comando:

```
az login
``` 

Questo comando fornisce una chiave di autenticazione da usare con l'URL https:\\aka.ms\devicelogin. L'interfaccia della riga di comando attende fino a quando viene restituita l'operazione di accesso al dispositivo e vengono fornite alcune informazioni di connessione. Quindi, se si dispone di un'installazione [Docker](https://www.docker.com/get-docker) locale, preparare l'ambiente di calcolo locale con il comando seguente:

```
az ml experiment prepare --target docker --run-configuration docker
```

È preferibile procedere all'esecuzione in una [macchina virtuale di data science (DSVM) per Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) per i requisiti di memoria e disco. Dopo avere configurato la macchina virtuale di data science, preparare l'ambiente Docker remoto con i due comandi seguenti:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

Una volta eseguita la connessione al contenitore Docker remoto, preparare l'ambiente di calcolo Docker per la macchina virtuale di data science usando il comando: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Con l'ambiente di calcolo Docker preparato, aprire il server del notebook di Jupyter all'interno della scheda **Notebooks** (Notebook) di Azure Machine Learning Workbench o avviare un server basato su browser usando il comando seguente: 

```
az ml notebook start
```

I notebook di esempio sono archiviati nella directory Code. I notebook sono configurati per essere eseguiti in sequenza, a partire dal primo notebook (Code\1_data_ingestion.ipynb). Quando si apre ciascun notebook, viene chiesto di selezionare il kernel di calcolo. Scegliere il kernel [Project_Name]_Template [Connection_Name] da eseguire nella macchina virtuale di data science configurata in precedenza.

## <a name="data-description"></a>Descrizione dei dati

Il modello usa tre set di dati come input, nei file PM_train.txt, PM_test.txt e PM_truth.txt. 

- **Dati di training**: dati "run-to-failure" dei motori dell'aereo. I dati di training (PM_train.txt) sono costituiti da più serie temporali multivariate, con il *ciclo* come unità di tempo. Includono 21 letture dei sensori per ogni ciclo. 

    - Ogni serie temporale viene generata da un motore diverso dello stesso tipo. Ogni motore parte con gradi diversi di usura iniziale e alcune variazioni di produzione univoche. Queste informazioni non sono note all'utente. 

    - In questi dati simulati si presuppone che il motore funzioni normalmente all'inizio di ogni serie temporale e che parta per ridurre le prestazioni in un determinato punto durante la serie di cicli operativi. La riduzione delle prestazioni avanza e aumenta in termini di grandezza. 

    - Quando viene raggiunta una soglia predefinita, il motore è considerato non sicuro per un ulteriore funzionamento. L'ultimo ciclo di ogni serie temporale è il punto di errore del motore.

- **Dati di test**: dati di funzionamento dei motori dell'aereo, senza eventi di errore registrati. I dati di test (PM_test.txt) hanno lo stesso schema di dati di quelli di training. L'unica differenza è che i dati non indicano quando si verifica l'errore (l'ultimo periodo di tempo *non* rappresenta il punto di guasto). Non è possibile sapere quanti altri cicli può durare il motore prima che si verifichi il guasto.

- **Dati reali**: informazioni sugli effettivi cicli restanti per ogni motore nei dati di test. I dati reali forniscono il numero dei cicli operativi rimanenti per i motori nei dati di test.

## <a name="scenario-structure"></a>Struttura dello scenario

Il flusso di lavoro dello scenario è suddiviso in tre passaggi, ognuno dei quali viene eseguito in un notebook di Jupyter. Ogni notebook produce elementi dati che vengono salvati in modo permanente in locale e che possono essere usati nei notebook.

### <a name="task-1-data-ingestion-and-preparation"></a>Attività 1. Inserimento e preparazione dei dati

Il notebook di Jupyter per l'inserimento dati in Code/1_data_ingestion_and_preparation.ipnyb carica i tre set di dati di input nel formato del frame di dati Pandas. Quindi prepara i dati per la modellazione ed esegue alcune visualizzazioni dei dati preliminari. I set di dati vengono archiviati in locale nel contesto di calcolo per essere usati nel notebook Jupyter di compilazione del modello.

### <a name="task-2-model-building-and-evaluation"></a>Attività 2. Creazione e valutazione del modello

Il notebook Jupyter di compilazione del modello in Code/2_model_building_and_evaluation.ipnyb legge i set di dati di training e test dal disco e genera una rete LSTM per il set di dati di training. Le prestazioni del modello vengono misurate nel set di dati di test. Il modello risultante è serializzato e archiviato nel contesto di calcolo locale per essere usato nell'attività di operazionalizzazione.

### <a name="task-3-operationalization"></a>Attività 3. Operazionalizzazione

Il notebook di Jupyter per l'operazionalizzazione in Code/3_operationalization.ipnyb usa il modello archiviato per compilare le funzioni e lo schema richiesti per chiamare il modello in un servizio Web ospitato in Azure. Il notebook testa le funzioni e quindi comprime le risorse di operazionalizzazione nel file LSTM_o16n.zip. Il file viene caricato nel contenitore di Archiviazione di Azure per la distribuzione.

Il file di distribuzioneLSTM_o16n.zip contiene gli elementi seguenti:

- **webservices_conda.yaml**: definisce i pacchetti di Python necessari per eseguire il modello LSTM nella destinazione di distribuzione.  
- **service_schema.json**: definisce lo schema di dati previsto dal modello LSTM.   
- **lstmscore.py**: definisce le funzioni che la destinazione di distribuzione esegue per ottenere nuovi dati.    
- **modellstm.json**: definisce l'architettura LSTM. Le funzioni lstmscore.py leggono l'architettura e i pesi per inizializzare il modello.
- **modellstm.h5**: definisce i pesi del modello.
- **test_service.py**: uno script di test che chiama l'endpoint di distribuzione con record dei dati di test. 
- **PM_test_files.pkl**: lo script test_service.py legge i dati cronologici del motore dal file PM_test_files.pkl e invia al servizio Web cicli sufficienti affinché LSTM restituisca la probabilità di errore del motore.

Il notebook verifica le funzioni usando la definizione del modello prima di creare il pacchetto delle risorse di operazionalizzazione per la distribuzione. Le istruzioni per la configurazione e il test del servizio Web sono incluse alla fine del notebook Code/3_operationalization.ipnyb.

## <a name="conclusion"></a>Conclusioni

Questa esercitazione offre uno scenario semplice che si serve dei valori del sensore per fare stime. Gli scenari di manutenzione predittiva più avanzati, ad esempio il [notebook R della guida di modellazione per la manutenzione predittiva](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1), possono usare molte origini dati, ad esempio record cronologici di manutenzione, log degli errori e funzionalità del computer. Altre origini dati possono richiedere l'uso di trattamenti diversi nell'ambito dell'apprendimento avanzato.


## <a name="references"></a>Riferimenti

I riferimenti seguenti offrono altri esempi di casi d'uso di manutenzione predittiva per varie piattaforme:

* [Modello di soluzione per la manutenzione predittiva](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Predictive Maintenance Modeling Guide](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1) (Guida alla modellazione per la manutenzione predittiva)
* [Predictive Maintenance Modeling Guide using SQL R Services](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1) (Guida alla modellazione per la manutenzione predittiva con SQL R Services)
* [Predictive Maintenance Modeling Guide Python Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1) (Notebook Python di guida alla modellazione per la manutenzione predittiva)
* [Predictive Maintenance using PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark) (Manutenzione predittiva con PySpark)
* [Scenari reali di manutenzione predittiva](https://docs.microsoft.com/en-us/azure/machine-learning/desktop-workbench/scenario-predictive-maintenance)

## <a name="next-steps"></a>Passaggi successivi

Sono disponibili altri scenari di esempio all'interno di Azure Machine Learning Workbench che dimostrano le funzionalità aggiuntive del prodotto. 
