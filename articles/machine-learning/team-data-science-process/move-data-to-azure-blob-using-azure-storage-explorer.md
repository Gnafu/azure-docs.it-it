---
title: Spostare i dati di archiviazione BLOB con Azure Storage Explorer - Processo di data science per i team
description: Spostamento dei dati da e verso l'archiviazione BLOB di Azure utilizzando Storage Explorer di Azure
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8ec4289c641b3f140950575ac2f8016f894f2c9c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61430826"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Spostamento dei dati da e verso l'archiviazione BLOB di Azure usando Storage Explorer di Azure
Esplora archivi di Azure è uno strumento gratuito di Microsoft che consente di lavorare con i dati di Archiviazione di Azure in Windows, MacOS e Linux. Questo argomento descrive come usarlo per caricare e scaricare i dati dall'archiviazione BLOB di Azure. Lo strumento può essere scaricato da [Esplora archivi di Microsoft Azure](https://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Se si utilizza una macchina virtuale che è stata impostata con gli script forniti da [Macchine virtuali della scienza dei dati in Azure](virtual-machines.md), allora Esplora archivi di Azure è già installato nella macchina virtuale.
> 
> [!NOTE]
> Per un'introduzione completa all'archiviazione BLOB di Azure, vedere [Introduzione all'archivio BLOB di Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) e [Servizio BLOB di Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).   
> 
> 

## <a name="prerequisites"></a>Prerequisiti
In questo documento si presuppone di avere una sottoscrizione di Azure, un account di archiviazione e chiavi di archiviazione corrispondenti per quell'account. Prima di caricare/scaricare i dati, è necessario conoscere il nome e la chiave del proprio account di archiviazione di Azure. 

* Per configurare una sottoscrizione di Azure, vedere [Versione di prova gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).
* Per istruzioni sulla creazione di un account di archiviazione e per ottenere informazioni sull’account e la chiave, vedere [Informazioni sugli account di archiviazione di Azure](../../storage/common/storage-create-storage-account.md). Annotare la chiave di accesso dell'account di archiviazione in quanto servirà per la connessione all'account con lo strumento Esplora archivi di Azure.
* Lo strumento Esplora archivi di Azure può essere scaricato da [Esplora archivi di Microsoft Azure](https://storageexplorer.com/). Durante l'installazione accettare le impostazioni predefinite.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Utilizzo di Esplora archivi Azure
I passaggi seguenti documentano come caricare e scaricare i dati utilizzando Esplora archivi Azure. 

1. Avviare Esplora archivi di Microsoft Azure.
2. Per visualizzare la procedura guidata **Accedi al tuo account...** , selezionare l'icona delle **impostazioni dell'account Azure**, selezionare **Aggiungi un account** e immettere le credenziali. ![Aggiungere un account di archiviazione di Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Per visualizzare la procedura guidata **Connetti ad Archiviazione di Azure**, selezionare l'icona per la **connessione ad Archiviazione di Azure**. ![Fare clic su "Connetti ad Archiviazione di Azure"](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Immettere la chiave di accesso dell'account di archiviazione di Azure nella procedura guidata **Connetti ad Archiviazione di Azure** e selezionare **Avanti**. ![Immettere la chiave di accesso dell'account di archiviazione di Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Immettere il nome dell'account di archiviazione nella casella **Nome account** e selezionare **Avanti**. ![Associa archiviazione esterna](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. L'account di archiviazione aggiunto apparirà nell'elenco. Per creare un contenitore BLOB in un account di archiviazione, fare clic con il pulsante destro del mouse sul nodo **Contenitori BLOB** in tale account, selezionare **Crea contenitore BLOB** e immettere un nome.
7. Per caricare dati in un contenitore, selezionare il contenitore di destinazione e fare clic sul pulsante **Carica**.![Account di archiviazione](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Fare clic su **...** a destra della casella **File**, selezionare uno o più file da caricare dal file system e fare clic su **Carica** per iniziare a caricare i file.![Caricare i file](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Per scaricare i dati, selezionare il BLOB nel contenitore corrispondente da scaricare e fare clic su **Scarica**. ![Download dei file](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

