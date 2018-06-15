---
title: Domande frequenti su Azure Machine Learning 2017 (Anteprima) | Microsoft Docs
description: Questo articolo riporta alcune domande frequenti e le corrispondenti risposte sulle funzionalità di anteprima di Azure Machine Learning
services: machine-learning
author: serinakaye
ms.author: serinak
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 08/30/2017
ms.openlocfilehash: 8a0d26a1ba1ca5fa7a3bd5cfca078ee6c94b229d
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831576"
---
# <a name="azure-machine-learning-frequently-asked-questions"></a>Domande frequenti su Azure Machine Learning

Azure Machine Learning è un servizio completamente gestito che consente di creare, testare, gestire e distribuire modelli di Machine Learning e Al. I servizi e l'applicazione scaricabile si basano su un approccio di tipo code-first che sfrutta il cloud, il livello locale e il nodo perimetrale per offrire modelli di training, distribuzione, gestione e monitoraggio con velocità, potenza e flessibilità. Azure Machine Learning Studio è invece un ambiente di creazione visivo con trascinamento della selezione e accessibile da browser, dove la scrittura di codice non è necessaria. 

## <a name="general-product-questions"></a>Domande generiche sul prodotto

**Quali altri servizi Azure sono necessari?**

Archiviazione BLOB di Azure e Registro contenitori di Azure vengono usati da Azure Machine Learning. Inoltre, è necessario eseguire il provisioning di risorse di calcolo, ad esempio una macchina virtuale di data science o un cluster HDInsight. Sono necessarie anche risorse di calcolo e di hosting per la distribuzione dei servizi Web, ad esempio il [servizio contenitore di Azure](https://docs.microsoft.com/azure/aks).

**Qual è la relazione tra Azure Machine Learning e i Servizi di Microsoft Machine Learning in SQL Server 2017?**   

Servizi di Machine Learning in SQL Server 2017 è una piattaforma estendibile e scalabile che consente di integrare le attività di machine learning nei flussi di lavoro database. È adatta negli casi in cui è necessaria una soluzione locale, dove, ad esempio, lo spostamento dei dati è dispendioso o insostenibile. I carichi di lavoro cloud o ibridi sono invece una valida soluzione per i nuovi servizi di Azure. 

**Il prodotto supporta sia Python sia R? Supporta anche altri linguaggi di programmazione, come C++?**

Al momento è supportato soltanto Python. Microsoft sta lavorando all'integrazione di R, il cui supporto verrà introdotto a breve. 

**Qual è la relazione tra Azure Machine Learning e Microsoft Machine Learning per Apache Spark?**

MMLSpark offre strumenti di apprendimento avanzato e data science per Apache Spark, con particolare attenzione alla produttività, alla facilità di sperimentazione e agli algoritmi all'avanguardia. Integra le pipeline di Spark Machine Learning con Microsoft Cognitive Toolkit e OpenCV. Consente di creare modelli avanzati altamente scalabili, predittivi e analitici per dati di immagini e di testo. MMLSpark è disponibile con licenza open source ed è incluso in Azure Machine Learning Workbench come un set di modelli e algoritmi fruibili. Per altre informazioni su MMLSpark, visitare la documentazione del prodotto. 

**Quali versioni di Spark sono supportate dai nuovi strumenti e servizi?**

Azure Machine Learning Workbench include e supporta MMLSpark versione 0.8, che è compatibile con Apache Spark 2.1. Nelle macchine virtuali Linux è anche possibile usare un'immagine Docker con supporto per GPU di MMLSpark 0.8.

## <a name="experimentation-service"></a>Servizio Sperimentazione

**Che cos'è il Servizio Sperimentazione di Azure Machine Learning?**

Il Servizio Sperimentazione è un servizio gestito di Azure che porta la sperimentazione in ambito di Machine Learning a un livello avanzato. È possibile compilare esperimenti in locale o nel cloud, eseguirne un prototipo su un desktop, quindi passare alle macchine virtuali o a cluster Spark. Le macchine virtuali di Azure, dotate della più recente tecnologia GPU, consentono di avviare l'apprendimento avanzato in modo rapido ed efficace. Grazie anche alla stretta integrazione con Git, è possibile collegare facilmente flussi di lavoro esistenti per le attività di tracciabilità del codice, configurazione e collaborazione. 

**Come viene addebitato il Servizio Sperimentazione?**

I primi due utenti associati al Servizio Sperimentazione di Azure Machine Learning sono gratuiti. Per ogni utente aggiuntivo viene addebitata la tariffa dell'anteprima pubblica, pari a 50 dollari al mese. Per altre informazioni su prezzi e fatturazione, visitare la pagina dei prezzi.

**Gli addebiti saranno basati sul numero di esperimenti eseguiti?**

No. Il Servizio Sperimentazione permette di eseguire tutti gli esperimenti che servono e gli addebiti sono basati solo sul numero di utenti. Le risorse di calcolo di Sperimentazione vengono addebitate separatamente. È consigliabile eseguire più esperimenti in modo da trovare il modello più adatto alla propria soluzione.   

**Quale tipo specifico di risorse di calcolo e di archiviazione è possibile usare?**

Il Servizio Sperimentazione può eseguire gli esperimenti su computer locali (dirette o basate su Docker), su [macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/) e su [HDInsight](https://azure.microsoft.com/services/hdinsight/). Il servizio accede anche a un account di [Archiviazione di Azure](https://azure.microsoft.com/services/storage/) per l'archiviazione degli output di esecuzione e può usare un account di [Visual Studio Team Service](https://azure.microsoft.com/services/visual-studio-team-services/) per il controllo della versione e l'archiviazione Git. Si noti che la fatturazione avviene in modo indipendente per qualsiasi risorsa di calcolo e di archiviazione usata, in base al rispettivo prezzo.  


## <a name="model-management"></a>Gestione modelli

**Che cos'è Gestione modelli di Azure Machine Learning?**

Il servizio Gestione modelli di Machine Learning è un servizio gestito di Azure che consente a data scientist e team di dev-ops di distribuire modelli predittivi in un'ampia gamma di ambienti. I repository Git e contenitori Docker forniscono tracciabilità e ripetibilità. I modelli possono essere distribuiti in modo affidabile nel cloud, in locale o nel nodo perimetrale. Nell'ambiente di produzione è possibile gestire le prestazioni del modello e ripeterne il training in modo proattivo se si osserva una loro riduzione. È possibile distribuire i modelli su computer locali, su [macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/), Spark in [HDInsight](https://azure.microsoft.com/services/hdinsight/) o cluster del [servizio contenitore di Azure](https://azure.microsoft.com/services/container-service/) gestiti con Kubernetes.  

**Che cos'è un modello?**

Un modello è costituito dall'output di un esperimento eseguito il cui livello è stato alzato alla gestione del modello. Un modello registrato nell'account di hosting viene conteggiato rispetto al piano dell'utente, anche nel caso di modelli aggiornati con la ripetizione del training o l'iterazione della versione.

**Che cos'è un "modello gestito"?**

Un modello è costituito dall'output di un processo di training e corrisponde all'applicazione di un algoritmo di Machine Learning ai dati di training. Il servizio Gestione modelli permette di distribuire modelli come servizi Web, gestire diverse versioni dei modelli e monitorare prestazioni e metriche associate. I "modelli gestiti" sono modelli registrati in un account di Gestione modelli di Azure Machine Learning. Si consideri ad esempio uno scenario di tentativo di previsione delle vendite. Durante la fase di sperimentazione, vengono generati molti modelli usando diversi set di dati o algoritmi. Si supponga di aver generato quattro modelli con valori diversi riferiti all'accuratezza ma si sceglie di registrare solo il modello con l'accuratezza più elevata. Il modello che viene registrato diventa il primo modello gestito.
 
**Che cos'è una distribuzione?**

Gestione modelli consente di distribuire i modelli come contenitori di servizi Web inseriti in un pacchetto in Azure. Questi servizi Web possono essere chiamati tramite le API REST. Ogni servizio Web viene conteggiato come singola distribuzione e il numero totale di distribuzioni attive viene calcolato rispetto al piano dell'utente. Usando l'esempio di previsione delle vendite, se si distribuisce il modello con le prestazioni migliori il piano viene incrementato con una distribuzione. Se si ripete il training e si distribuisce un'altra versione, si avranno due distribuzioni. Se si stabilisce che il modello più recente è migliore e si elimina il modello originale, il numero di distribuzioni si riduce di uno.  

**Quali sono le risorse di calcolo specifiche disponibili per le distribuzioni?** 

Gestione modelli consente di eseguire le distribuzioni come contenitori Docker registrati nel [servizio contenitore di Azure](https://azure.microsoft.com/services/container-service/), come [Macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/), o su computer locali. A breve verranno aggiunte altre destinazioni di distribuzione. Si noti che la fatturazione avviene in modo indipendente per qualsiasi risorsa di calcolo usata, in base al rispettivo prezzo.     

**È possibile usare Gestione modelli di Azure Machine Learning per distribuire modelli creati con strumenti diversi dal Servizio Sperimentazione?**

I risultati migliori si ottengono distribuendo i modelli creati con il Servizio Sperimentazione. È tuttavia possibile distribuire modelli compilati con altri framework e strumenti. È supportata un'ampia gamma di modelli, inclusi MMLSpark, TensorFlow, Microsoft Cognitive Toolkit, scikit-learn, Keras e così via. 

**È possibile usare le risorse di Azure?**

Sì, il Servizio Sperimentazione e Gestione modelli funzionano insieme a diversi archivi di dati di Azure, carichi di lavoro e altri servizi. Consultare la documentazione tecnica per altre informazioni sui servizi di Azure necessari.

**Sono supportati scenari di distribuzione in locale e nel cloud?**

Sì. Gli scenari di distribuzione in locale e nel cloud sono supportati tramite i contenitori Docker. Le destinazioni di esecuzione includono distribuzioni Docker su nodo singolo, [Microsoft SQL Server con servizi di Machine Learning](https://docs.microsoft.com/sql/advanced-analytics/r/r-services), Hadoop o Spark. Sono supportate anche distribuzioni cloud tramite Docker, incluse distribuzioni in cluster tramite il servizio contenitore di Azure e Kubernetes, HDInsight o cluster Spark. Gli scenari con nodo perimetrale sono supportati tramite container Docker e Azure IoT Edge. 

**È possibile eseguire un'immagine Docker creata con l'interfaccia della riga di comando di Azure Machine Learning in un altro host?**

Sì. È possibile usare l'immagine come servizio Web in qualsiasi host Docker a condizione che l'host disponga di risorse di calcolo sufficienti per l'hosting dell'immagine stessa.

**È supportata la ripetizione del training di modelli distribuiti?**

Sì, è possibile distribuire più versioni dello stesso modello. Gestione modelli supporta gli aggiornamenti del servizio per tutti i modelli e le immagini aggiornate.

## <a name="workbench"></a>Workbench

**Che cos'è Azure Machine Learning Workbench?**

Azure Machine Learning Workbench è un'applicazione complementare progettata per data scientist. Disponibile per Windows e Mac, Machine Learning Workbench fornisce funzioni di panoramica, gestione e controllo per soluzioni di machine learning. Consente l'accesso a framework all'avanguardia in materia di AI, sia di Microsoft sia della community open source. Include anche i toolkit di data science più diffusi, tra cui TensorFlow, Microsoft Cognitive Toolkit, Spark ML, scikit-learn e altri ancora. È stata anche abilitata l'integrazione con i più noti ambienti di sviluppo integrato di data science quali Notebook di Jupyter, PyCharm e Visual Studio Code. Machine Learning Workbench include inoltre funzionalità di preparazione dati incorporate che consentono di campionare, comprendere e preparare dati, sia strutturati sia non strutturati. Il nuovo strumento di preparazione dei dati, denominato [PROSE](https://microsoft.github.io/prose/), si basa sulla più recente tecnologia di Microsoft Research.  

**Machine Learning Workbench è un ambiente di sviluppo integrato?**

di serie Machine Learning Workbench è stato progettato come complemento per gli ambienti di sviluppo integrato (IDE) più diffusi, ad esempio Notebook di Jupyter, PyCharm e Visual Studio Code, ma non si tratta di un IDE completamente funzionale. È dotato di alcune funzionalità di base per l'editing di testo, ma non supporta altre funzionalità IDE quali debug, intellisense e altre comunemente usate. È consigliabile usare l'IDE preferito per lo sviluppo di codice, la modifica e il debug. Può anche essere utile provare gli strumenti [Visual Studio Code Tools for AI](https://www.visualstudio.com/downloads/ai-tools-vscode).

**È previsto un addebito per l'utilizzo di Azure Machine Learning Workbench?**

di serie Azure Machine Learning Workbench è un'applicazione gratuita. È possibile scaricarla nel numero di computer e per il numero di utenti necessario. Per usare Azure Machine Learning Workbench, è necessario disporre di un account di Sperimentazione. .  

**Sono supportate le funzionalità dell'interfaccia della riga di comando?**

Sì. Azure Machine Learning offre un'interfaccia della riga di comando completa, installata per impostazione predefinita con Azure Machine Learning Workbench. Viene anche fornita come parte della macchina virtuale Linux Data Science in Azure e verrà integrata nell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).


**È possibile usare Notebook di Jupyter con Azure Machine Learning Workbench?**

Sì. È possibile eseguire Notebook di Jupyter in Workbench come applicazione di hosting del client, nello stesso modo in cui si userebbe come client un browser. 

**Quali sono i kernel di Notebook di Jupyter supportati?**

La versione corrente di Jupyter inclusa in Workbench avvia un kernel di Python 3 e un kernel aggiuntivo per ogni file "runconfig" nella cartella aml_config. Le configurazioni supportate includono:
- Python in locale
- Python in locale o in un Docker remoto

## <a name="data-formats-and-capabilities"></a>Formati e funzionalità dati

**Quali formati di file sono attualmente supportati per l'inserimento di dati in Azure Machine Learning Workbench?**

Gli strumenti di preparazione dei dati in Workbench supportano attualmente l'inserimento a partire dai formati seguenti: 
- File delimitati da virgole, ad esempio CSV, TSV e così via.  
- File a larghezza fissa
- File di testo normale
- Excel (.xls/.xlsx)
- File JSON
- File Parquet 
- File personalizzati (script). Se la soluzione richiede l'inserimento di dati da altre origini, è anche possibile usare codice Python. 

**Quali sono le posizioni di archiviazione dei dati attualmente supportate?**

Per l'anteprima pubblica, Workbench supporta l'inserimento di dati da: 
- Unità disco rigido locale o posizione di archiviazione di rete mappata
- BLOB di Azure o Archiviazione di Azure (richiede una sottoscrizione ad Azure)
- Azure SQL Server
- Microsoft SQL Server


**Quali tipi di wrangling, preparazione e trasformazioni dei dati sono disponibili?**

Per l'anteprima pubblica, Workbench supporta la derivazione e la divisione delle colonne in base a un esempio, il clustering di testo, la gestione di valori mancanti e molti altri.  Supporta anche la conversione del tipo di dati, l'aggregazione dei dati (COUNT, MEAN, VARIANCE e così via) e join di dati complessi. Per un elenco completo delle funzionalità supportate, vedere la documentazione del prodotto. 

**Ci sono limiti alle dimensioni dei dati in Azure Machine Learning, Sperimentazione o Gestione modelli?**

No. I nuovi servizi non prevedono alcuna limitazione dei dati. Tuttavia, esistono limitazioni introdotte dall'ambiente in cui si sta eseguendo la preparazione dei dati, il training del modello, la sperimentazione o la distribuzione. Se, ad esempio, la destinazione del training è un ambiente locale, il limite è dato dallo spazio disponibile nel disco rigido. Se invece la destinazione è HDInsight, il limite è dato da qualsiasi dimensione o limite di calcolo associato. 

## <a name="algorithms-and-libraries"></a>Algoritmi e librerie

**Quali algoritmi sono supportati in Azure Machine Learning Workbench?**

I prodotti e i servizi in anteprima includono il meglio della community open source. È supportata una vasta gamma di algoritmi e librerie incluse TensorFlow, scikit-learn, Apache Spark e Microsoft Cognitive Toolkit. Azure Machine Learning Workbench include anche il pacchetto [revoscalepy di Microsoft](https://docs.microsoft.com/sql/advanced-analytics/python/what-is-revoscalepy).

**Qual è la relazione tra Azure Machine Learning e Microsoft Cognitive Toolkit?**

Il [Microsoft Cognitive Toolkit](https://www.microsoft.com/cognitive-toolkit/) è uno dei tanti framework supportati dai nuovi strumenti e servizi. È costituito da un toolkit di apprendimento avanzato unificato che consente di usare e combinare i modelli di machine learning più diffusi, tra cui reti neurali feed-forward, reti convoluzionali, sequenziali e ricorsive. Per altre informazioni su Microsoft Cognitive Toolkit, visitare la [documentazione del prodotto](https://docs.microsoft.com/cognitive-toolkit/). 