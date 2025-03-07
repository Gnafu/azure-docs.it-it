---
title: Rendere persistenti i risultati o i log da processi e attività completati in un archivio dati - Azure Batch | Microsoft Docs
description: Informazioni su diverse opzioni per rendere persistenti i dati di output da attività e processi di Batch. È possibile rendere persistenti i dati in Archiviazione di Azure o in un altro archivio dati.
services: batch
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d03fd754e5a8e2872063b8a10bd1293b94d8f3b6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094437"
---
# <a name="persist-job-and-task-output"></a>Rendere persistente l'output di processi e attività

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Alcuni esempi comuni di output delle attività includono:

- File creati mentre l'attività elabora i dati di input.
- File di log associati all'esecuzione dell'attività.

Questo articolo descrive diverse opzioni per rendere persistente l'output delle attività.

## <a name="options-for-persisting-output"></a>Opzioni per la persistenza dell'output

A seconda dello scenario, sono disponibili alcuni approcci diversi che è possibile scegliere per rendere persistente l'output delle attività:

- [Usare l'API del servizio Batch](batch-task-output-files.md).  
- [Usare la libreria Batch File Conventions per .NET](batch-task-output-file-conventions.md).  
- Implementare lo standard Batch File Conventions nelle applicazioni.
- Implementare una soluzione personalizzata per lo spostamento dei file.

Le sezioni seguenti descrivono brevemente ogni approccio, oltre a considerazioni sulla progettazione generali per rendere persistente l'output.

### <a name="use-the-batch-service-api"></a>Usare l'API del servizio Batch

Il servizio Batch consente di specificare i di file di output in Archiviazione di Azure per i dati delle attività quando si [aggiunge un'attività a un processo](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job) o si [aggiunge una raccolta di attività a un processo](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

Per altre informazioni su come rendere persistente l'output delle attività con l'API del servizio Batch, vedere [Rendere persistenti i dati di attività in Archiviazione di Azure con l'API del servizio Batch](batch-task-output-files.md).

### <a name="use-the-batch-file-conventions-library-for-net"></a>Usare la libreria Batch File Conventions per .NET

Batch definisce un set facoltativo di convenzioni per la denominazione dei file di output delle attività in Archiviazione di Azure. Lo [standard Batch File Conventions](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) descrive queste convenzioni. Lo standard File Conventions determina i nomi del percorso del contenitore e del BLOB di destinazione in Archiviazione di Azure per un file di output specificato in base ai nomi del processo e dell'attività.

Ogni utente può decidere se usare lo standard File Conventions per la denominazione dei file di dati di output. È anche possibile assegnare i nomi preferiti al contenitore e al BLOB di destinazione. Se si usa lo standard File Conventions per la denominazione dei file di output, i file di output sono disponibili per la visualizzazione nel [portale di Azure][portal].

Gli sviluppatori che compilano soluzioni batch con C# e .NET possono usare la [libreria file Conventions per .NET][nuget_package] per rendere permanente i dati delle attività in un account di archiviazione di Azure, in base allo [standard batch file Conventions](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). La libreria File Conventions gestisce lo spostamento dei file di output in Archiviazione di Azure e la denominazione dei contenitori e dei BLOB di destinazione in modo noto.

Per altre informazioni su come rendere persistente l'output delle attività con la libreria File Conventions per .NET, vedere [Rendere persistenti i dati di attività e processi in Archiviazione di Azure con la libreria Batch File Conventions per .NET](batch-task-output-file-conventions.md).

### <a name="implement-the-batch-file-conventions-standard"></a>Implementare lo standard Batch File Conventions

Se si usa un linguaggio diverso da .NET, è possibile implementare lo [standard Batch File Conventions](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) nell'applicazione.

Può essere utile implementare lo standard File Conventions quando si vuole usare uno schema di denominazione collaudato o è necessario visualizzare l'output delle attività nel portale di Azure.

### <a name="implement-a-custom-file-movement-solution"></a>Implementare una soluzione personalizzata per lo spostamento dei file

È anche possibile implementare una soluzione completa propria per lo spostamento dei file. Usare questo approccio nei casi seguenti:

- Si vogliono rendere persistenti i dati delle attività in un archivio dati diverso da Archiviazione di Azure. Per caricare i file in un archivio dati come SQL Azure o Azure Data Lake, è possibile creare uno script o un eseguibile personalizzato per eseguire il caricamento in tale posizione. È quindi possibile chiamarlo dalla riga di comando dopo l'esecuzione del file eseguibile principale. In un nodo di Windows, ad esempio, si potrebbero chiamare questi due comandi:`doMyWork.exe && uploadMyFilesToSql.exe`
- Si vogliono usare checkpoint o caricamenti anticipati dei risultati iniziali.
- Si vuole mantenere un controllo granulare sulla gestione degli errori. Ad esempio, può essere utile implementare una soluzione personalizzata se si vogliono usare le azioni per le dipendenze delle attività per eseguire opzioni di caricamento specifiche in base a codici di uscita specifici delle attività. Per altre informazioni sulle azioni per le dipendenze delle attività, vedere [Creare relazioni tra attività per eseguire attività che dipendono da altre attività](batch-task-dependencies.md).

## <a name="design-considerations-for-persisting-output"></a>Considerazioni sulla progettazione per la persistenza dell'output

Quando si progetta una soluzione Batch, è necessario considerare i fattori seguenti correlati agli output di processi e attività.

- **Durata dei nodi di calcolo**: i nodi di calcolo sono spesso temporanei, in particolare nei pool abilitati per il ridimensionamento automatico. L'output da un'attività eseguita su un nodo è disponibile solo finché il nodo esiste e solo durante il periodo di conservazione dei file impostato per l'attività. Se un'attività genera output che potrebbe essere necessario dopo il completamento dell'attività, l'attività deve caricare i file di output in un archivio permanente, come Archiviazione di Azure.

- **Archiviazione dell'output**: Archiviazione di Azure è la soluzione consigliata come archivio dati per l'output delle attività, ma è possibile usare qualsiasi sistema di archiviazione permanente. La funzionalità di scrittura dell'output in Archiviazione di Azure è integrata nell'API del servizio Batch. Se si usa un'altra forma di archiviazione permanente, sarà necessario scrivere autonomamente la logica dell'applicazione per rendere persistente l'output delle attività.

- **Recupero dell'output**: è possibile recuperare l'output delle attività direttamente dai nodi di calcolo nel pool oppure da Archiviazione di Azure o un altro archivio dati, se l'output delle attività viene reso persistente. Per recuperare l'output di un'attività direttamente da un nodo di calcolo, è necessario il nome del file e il relativo percorso di output nel nodo. Se si rende persistente l'output delle attività in Archiviazione di Azure, sarà necessario il percorso completo del file in Archiviazione di Azure per scaricare i file di output tramite Azure Storage SDK.

- **Visualizzazione dell'output**: quando si passa a un'attività di Batch nel portale di Azure e si seleziona **File nel nodo**, vengono visualizzati tutti i file associati all'attività, non solo i file di output a cui si è interessati. Anche in questo caso, i file nei nodi di calcolo sono disponibili solo finché il nodo esiste e solo durante periodo di conservazione dei file impostato per l'attività. Per visualizzare l'output delle attività salvato in modo permanente in archiviazione di Azure, è possibile usare l'portale di Azure o un'applicazione client di archiviazione di Azure, ad esempio il [Azure Storage Explorer][storage_explorer]. Per visualizzare i dati di output in Archiviazione di Azure con il portale o un altro strumento, è necessario conoscere il percorso del file e passare direttamente a tale percorso.

## <a name="next-steps"></a>Passaggi successivi

- Leggere le informazioni sull'uso delle nuove funzionalità dell'API del servizio Batch per rendere persistenti i dati in [Rendere persistenti i dati di attività in Archiviazione di Azure con l'API del servizio Batch](batch-task-output-files.md).
- Scoprire di più sull'uso della libreria Batch File Conventions per .NET in [Rendere persistenti i dati di attività e processi in Archiviazione di Azure con la libreria Batch File Conventions per .NET](batch-task-output-file-conventions.md).
- Vedere il progetto di esempio [PersistOutputs][github_persistoutputs] su GitHub, che illustra come usare sia la libreria client batch per .NET che la libreria file Conventions per .NET per salvare in modo permanente l'output delle attività in una risorsa di archiviazione durevole.

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs 
