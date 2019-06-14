---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 8a596293a5c1572b30ea6101dad16328c8db2634
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66159804"
---
## <a name="what-is-queue-storage"></a>Informazioni sull'archiviazione di accodamento
Il servizio di archiviazione di accodamento di Azure consente di archiviare grandi quantità di messaggi ai quali è possibile accedere da qualsiasi parte del mondo mediante chiamate autenticate tramite HTTP o HTTPS. La dimensione massima di un singolo messaggio della coda è di 64 KB e una coda può contenere milioni di messaggi, nei limiti della capacità complessiva di un account di archiviazione.

Di seguito sono riportati gli utilizzi più comuni per il servizio di archiviazione di accodamento.

* Creazione di un backlog di lavoro da elaborare in modo asincrono
* Passaggio di messaggi da un ruolo Web di Azure a un ruolo di lavoro di Azure

## <a name="queue-service-concepts"></a>Concetti del servizio di accodamento
Il servizio di accodamento contiene i componenti seguenti:

![Queue1](./media/storage-queue-concepts-include/queue1.png)

* **Formato URL:** è possibile fare riferimento alle code usando il formato URL seguente:   
    http://`<storage account>`.queue.core.windows.net/`<queue>` 
  
    L'URL seguente fa riferimento a una delle code nel diagramma:  
  
    `http://myaccount.queue.core.windows.net/images-to-download`

* **Account di archiviazione:** l'accesso ad Archiviazione di Azure viene eseguito esclusivamente tramite un account di archiviazione. Per informazioni sulla capacità dell'account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../articles/storage/common/storage-scalability-targets.md) .
* **Coda:** una coda contiene un set di messaggi. Tutti i messaggi devono essere inclusi in una coda. Si noti che il nome della coda deve essere in lettere minuscole. Per altre informazioni, vedere [Denominazione di code e metadati](https://msdn.microsoft.com/library/azure/dd179349.aspx).
* **Messaggio:** un messaggio, in qualsiasi formato, con dimensioni massime di 64 kB. Il tempo massimo che un messaggio può rimanere nella coda è di 7 giorni.

