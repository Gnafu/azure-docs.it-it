---
title: Spostamento dei dati da e verso l'archiviazione BLOB di Azure | Documentazione Microsoft
description: Spostamento dei dati da e verso l'archiviazione BLOB di Azure
services: machine-learning,storage
documentationcenter: ''
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: d6681e30-ab45-45ea-a9fb-ac8acefe544d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: 16988d7dd466eb0f893dc0b1f8fdc6511115d0a7
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Spostamento dei dati da e verso l'archivio BLOB di Azure
[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

<!-- just in case, adding this to separate these two include references -->

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Quale sia il metodo adatto dipenderà dallo scenario. L'articolo [Scenari per l'analisi avanzata in Azure Machine Learning](plan-sample-scenarios.md) consente di determinare le risorse necessarie per un'ampia gamma di flussi di lavoro di analisi scientifica dei dati usati nel processo di analisi avanzata.

> [!NOTE]
> Per un'introduzione completa all'archiviazione BLOB di Azure, vedere [Informazioni di base su BLOB di Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) e [Servizio BLOB di Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

In alternativa, è possibile utilizzare [Data factory di Azure](https://azure.microsoft.com/services/data-factory/) per: 

* creare e pianificare una pipeline che scarica i dati dall'archiviazione BLOB di Azure; 
* trasmetterla a un servizio Web di Azure Machine Learning pubblicato; 
* ricevere i risultati di analisi predittiva; 
* caricare i risultati nell'archiviazione. 

Per altre informazioni, vedere [Creare pipeline predittive tramite Data factory di Azure e Azure Machine Learning](../../data-factory/v1/data-factory-azure-ml-batch-execution-activity.md).

## <a name="prerequisites"></a>prerequisiti
In questo documento si presuppone di avere una sottoscrizione di Azure, un account di archiviazione e chiavi di archiviazione corrispondenti per quell'account. Prima di caricare/scaricare i dati, è necessario conoscere il nome e la chiave del proprio account di archiviazione di Azure.

* Per configurare una sottoscrizione di Azure, vedere [Versione di prova gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).
* Per istruzioni sulla creazione di un account di archiviazione e per ottenere informazioni sull’account e la chiave, vedere [Informazioni sugli account di archiviazione di Azure](../../storage/common/storage-create-storage-account.md).

