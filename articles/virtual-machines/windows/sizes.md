---
title: Dimensioni delle macchine virtuali Windows in Azure | Documentazione Microsoft
description: Elenca le diverse dimensioni disponibili per le macchine virtuali Windows in Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: aabf0d30-04eb-4d34-b44a-69f8bfb84f22
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/22/2018
ms.author: jonbeck
ms.openlocfilehash: d4e1b56ba695f3305d1be023c4352a3c4bcfe27d
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "34658350"
---
# <a name="sizes-for-windows-virtual-machines-in-azure"></a>Dimensioni per le macchine virtuali Windows in Azure

Questo articolo descrive le dimensioni e le opzioni disponibili per le macchine virtuali di Azure che è possibile usare per eseguire le app Windows e i carichi di lavoro. Offre anche considerazioni sulla distribuzione da tenere presenti quando si prevede di usare queste risorse.  Questo articolo è disponibile anche per le [macchine virtuali Linux](../linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


| type                     | Dimensioni           |    DESCRIZIONE       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Utilizzo generico](sizes-general.md)          | B, Dsv3, Dv3, DSv2, Dv2, Av2 | Rapporto equilibrato tra CPU e memoria. Soluzione ideale per test e sviluppo, database medio-piccoli e server Web con traffico da medio a ridotto. |
| [Ottimizzate per il calcolo](sizes-compute.md)        | Fsv2, Fs, F             | Rapporto elevato tra CPU e memoria. Soluzione idonea per server Web con livelli medi di traffico, dispositivi di rete, processi batch e server applicazioni.        |
| [Ottimizzate per la memoria](../virtual-machines-windows-sizes-memory.md)         | Esv3, Ev3, M, GS, G, DSv2, Dv2  | Rapporto elevato tra memoria e CPU. Soluzione ideale per server di database relazionali, cache medio-grandi e analisi in memoria.                 |
| [Ottimizzate per l'archiviazione](../virtual-machines-windows-sizes-storage.md)        | Ls                | I/O e velocità effettiva del disco elevati. Ideale per Big Data, database SQL e NoSQL.                                                         |
| [GPU](sizes-gpu.md)            | NV, NC, NCv2, NCv3, ND            | Macchine virtuali specializzate ottimizzate per livelli intensivi di rendering della grafica e modifica di video, nonché per il training e l'inferenza dei modelli (ND) con apprendimento profondo. disponibili con GPU singole o più GPU.       |
| [High Performance Computing (HPC)](sizes-hpc.md) | H       | Le nostre macchine virtuali con CPU più veloci e potenti, con interfacce di rete ad alta velocità effettiva facoltative (RDMA). 


<br> 

- Per informazioni sui prezzi di varie dimensioni, vedere [Prezzi di Macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). 
- Per trovare i limiti generali delle VM di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../../azure-subscription-service-limits.md).
- I costi di archiviazione vengono calcolati separatamente in base alle pagine usate nell'account di archiviazione. Per informazioni dettagliate, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).
- Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.


## <a name="rest-api"></a>API REST

Per informazioni sull'uso dell'API REST per query relative alle dimensioni delle macchine virtuali, vedere gli argomenti seguenti:

- [List available virtual machine sizes for resizing](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes) (Elencare le dimensioni delle macchine virtuali disponibili per il ridimensionamento)
- [List available virtual machine sizes for a subscription](https://docs.microsoft.com/rest/api/compute/virtualmachines/listall) (Elencare le dimensioni delle macchine virtuali disponibili per una sottoscrizione)
- [List available virtual machine sizes in an availability set](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes) (Elencare le dimensioni delle macchine virtuali disponibili in un set di disponibilità)

## <a name="acu"></a>Unità di calcolo di Azure

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.

## <a name="benchmark-scores"></a>Punteggi di benchmark

Altre informazioni sulle prestazioni di calcolo per l'uso di macchine virtuali di Windows usando i [punteggi di benchmark CoreMark](compute-benchmark-scores.md).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle diverse dimensioni di macchina virtuale disponibili:
- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per il calcolo](sizes-compute.md)
- [Ottimizzate per la memoria](../virtual-machines-windows-sizes-memory.md)
- [Ottimizzate per l'archiviazione](../virtual-machines-windows-sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- Vedere la pagina [Generazione precedente](sizes-previous-gen.md) per informazioni sulle serie A Standard, Dv1 (D1-4 e D11-14 v1) e A8-A11




