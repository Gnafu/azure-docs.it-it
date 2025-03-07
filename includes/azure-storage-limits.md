---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/19/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b51d984726cfd178189e7fe1a994c35c7efaed36
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71124082"
---
La tabella seguente descrive i limiti predefiniti per gli account di archiviazione BLOB V1, V2 e per utilizzo generico di Azure. Il limite di *ingresso* si riferisce a tutti i dati delle richieste inviate a un account di archiviazione. Il limite in *uscita* fa riferimento a tutti i dati delle risposte ricevute da un account di archiviazione.

| Risorsa | Limite predefinito |
| --- | --- |
| Numero di account di archiviazione per ogni area per ogni sottoscrizione, inclusi gli account sia Standard che Premium | 250 |
| Capacità massima dell'account di archiviazione | 2 PiB per Stati Uniti ed Europa e 500 TiB per tutte le altre aree (incluso il Regno Unito)<sup>1</sup>|
| Numero massimo di contenitori BLOB, BLOB, condivisioni file, tabelle, code, entità o messaggi per account di archiviazione | Nessun limite |
| Frequenza massima di richieste<sup>1</sup> per account di archiviazione | 20.000 richieste al secondo |
| Ingresso massimo<sup>1</sup> per account di archiviazione (aree Stati Uniti, Europa) | 25 Gbps |
| Ingresso massimo<sup>1</sup> per account di archiviazione (aree diverse da Stati Uniti ed Europa) | 5 Gbps se RA-GRS/GRS è abilitata, 10 Gbps per con ridondanza locale/ZRS<sup>2</sup> |
| Numero massimo in uscita per gli account di archiviazione BLOB e V2 per utilizzo generico (tutte le aree) | 50 Gbps |
| Numero massimo in uscita per gli account di archiviazione per utilizzo generico V1 (aree degli Stati Uniti) | 20 Gbps se RA-GRS/GRS è abilitata, 30 Gbps per con ridondanza locale/ZRS<sup>2</sup> |
| Numero massimo in uscita per gli account di archiviazione per utilizzo generico V1 (aree non statunitensi) | 10 Gbps se RA-GRS/GRS è abilitato, 15 Gbps per con ridondanza locale/ZRS<sup>2</sup> |

<sup>1</sup> Gli account di archiviazione standard di Azure supportano limiti di capacità superiori e limiti più elevati per il traffico in ingresso per richiesta. Per richiedere un incremento dei limiti di archiviazione per il traffico in ingresso, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/faq/). Per altre informazioni, vedere l' [annuncio di account di archiviazione](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)con scalabilità più grandi.

<sup>2</sup> se è abilitato l'accesso in lettura (RA-GRS/ra-GZRS), le destinazioni in uscita per la posizione secondaria sono identiche a quelle della posizione primaria. Le opzioni di [replica di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy) includono:[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

> [!NOTE]
> Per la maggior parte degli scenari, è consigliabile usare un account di archiviazione di uso generico V2. È possibile aggiornare facilmente un account di archiviazione BLOB di Azure o di uso generico V1 a un account per utilizzo generico V2 senza tempi di inattività e senza la necessità di copiare i dati.
>
> Per altre informazioni sugli account di archiviazione di Azure, vedere [Panoramica dell'account di archiviazione](../articles/storage/common/storage-account-overview.md).

Se le esigenze dell'applicazione superano gli obiettivi di scalabilità di un singolo account di archiviazione, è possibile compilare l'applicazione in modo che sia possibile usare più account di archiviazione. Quindi partizionare gli oggetti dati tra tali account di archiviazione. Per informazioni sui prezzi dei volumi, vedere [prezzi di archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

Tutti gli account di archiviazione vengono eseguiti su una topologia di rete flat e supportano gli obiettivi di scalabilità e prestazioni descritti in questo articolo, indipendentemente dal momento in cui sono stati creati. Per altre informazioni sull'architettura di rete flat di Archiviazione di Azure e sulla scalabilità, vedere [Archiviazione di Microsoft Azure: A Highly Available Cloud Storage Service with Strong Consistency](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx) (Archiviazione di Microsoft Azure: un servizio di archiviazione cloud a elevata disponibilità con coerenza assoluta).

