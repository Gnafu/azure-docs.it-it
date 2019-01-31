---
title: Esplorare i dati nell'archiviazione BLOB di Azure con Pandas - Processo di analisi scientifica dei dati del team
description: Come esplorare i dati archiviati nel contenitore BLOB di Azure con il pacchetto Python Pandas.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1d76f9a76c85f4f3f052c3b409f5be28b51ebe91
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55459129"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Esplorare i dati nell'archiviazione BLOB di Azure con Pandas

Questo articolo descrive come esplorare i dati archiviati nel contenitore BLOB di Azure con il pacchetto Python [Pandas](http://pandas.pydata.org/).

Questa attività è un passaggio del [processo di data science per i team](overview.md).

## <a name="prerequisites"></a>Prerequisiti
Questo articolo presuppone che l'utente abbia:

* Creato un account di archiviazione di Azure. Per istruzioni, vedere [Creare un account di archiviazione di Azure](../../storage/common/storage-quickstart-create-account.md)
* I dati sono stati archiviati in un account di archiviazione BLOB di Azure. Per le istruzioni, vedere [Spostamento dei dati da e verso Archiviazione di Azure](../../storage/common/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Caricare i dati in un frame di dati Pandas
Per esplorare e modificare un set di dati, è necessario innanzitutto scaricarlo dall'origine BLOB in un file locale che può essere quindi caricato in un frame di dati Pandas. Ecco i passaggi da seguire per questa procedura:

1. Scaricare i dati da BLOB Azure con l’esempio di codice Python riportato di seguito utilizzando il servizio BLOB. Sostituire la variabile nel codice seguente con i valori specifici: 
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))
2. Leggere i dati in un frame di dati Pandas dal file scaricato.
   
        #LOCALFILE is the file path    
        dataframe_blobdata = pd.read_csv(LOCALFILE)

A questo punto si è pronti per esplorare i dati e generare le funzionalità di questo set di dati.

## <a name="blob-dataexploration"></a>Esempi di esplorazione dei dati tramite Pandas
Di seguito sono riportati alcuni esempi dei modi per esplorare i dati usando Pandas:

1. Controllare il **numero di righe e colonne** 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. **Controllare** le prime o le ultime **righe** nel set di dati seguente:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Controllare il **tipo di dati** importato in ogni colonna mediante il seguente codice di esempio
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Controllare le **statistiche di base** per le colonne nel set di dati come segue
   
        dataframe_blobdata.describe()
5. Esaminare il numero di voci per ogni valore della colonna come indicato di seguito
   
        dataframe_blobdata['<column_name>'].value_counts()
6. **Contare i valori mancanti** rispetto al numero effettivo di voci in ogni colonna utilizzando il seguente codice di esempio
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Se si dispongono di **valori mancanti** per una colonna specifica nei dati, è possibile eliminarli come indicato di seguito:
   
     dataframe_blobdata_noNA = dataframe_blobdata.dropna()   dataframe_blobdata_noNA.shape
   
   È possibile sostituire i valori mancanti anche con la funzione modalità:
   
     dataframe_blobdata_mode = dataframe_blobdata.fillna({'<nome_colonna>':dataframe_blobdata['<nome_colonna>'].mode()[0]})        
8. Creare un grafico **istogramma** utilizzando un numero variabile di contenitori per tracciare la distribuzione di una variabile    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Esaminare le **correlazioni** tra le variabili utilizzando un grafico di dispersione o la funzione di correlazione incorporata
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

