---
title: Utilizzo dell’interfaccia della riga di comando per ridimensionare Media Served Unit - Azure | Microsoft Docs
description: In questo argomento viene spiegato come usare l’interfaccia della riga di comando per ridimensionare l'elaborazione di servizi multimediali con Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 13fa733417558ab8be9ff1e5a9f1e484fb40f445
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102938"
---
# <a name="scaling-media-processing"></a>Ridimensionamento dell'elaborazione di contenuti multimediali

Servizi multimediali di Azure consente di ridimensionare l'elaborazione di contenuti multimediali nell'account gestendo le Media Reserved Unit (MRU). MRU determina la velocità con cui vengono elaborate le attività di elaborazione di contenuti multimediali. È possibile scegliere uno dei seguenti tipi di unità riservata: **S1**, **S2** o **S3**. Lo stesso processo di codifica viene eseguito più velocemente quando si usa ad esempio il tipo di unità riservata **S2** rispetto al tipo **S1**. 

Oltre al tipo di unità riservata, è possibile specificare il provisioning dell'account con unità riservate. Il numero delle unità riservate sottoposte a provisioning determina il numero di attività multimediali che possono essere elaborate contemporaneamente in un determinato account. Se, ad esempio, il proprio account dispone di cinque unità riservate, è possibile eseguire simultaneamente cinque attività multimediali, purché siano presenti attività da elaborare. Le attività rimanenti verranno messe in coda e prelevate in sequenza per l'elaborazione quando un'attività in esecuzione viene completata. Se per un account non sono state fornite unità riservate, le attività verranno prelevate in sequenza. In questo caso, il tempo di attesa tra il completamento di un'attività e l'avvio di quella successiva dipende dalle risorse disponibili nel sistema.

## <a name="choosing-between-different-reserved-unit-types"></a>Scelta tra i tipi diversi di unità riservata

La tabella seguente consente di scegliere tra diverse velocità di codifica. Fornisce inoltre alcuni casi di riferimento sul [un video che è possibile scaricare](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) per eseguire i propri test:

|Tipo di unità richiesta|Scenario|Risultati di esempio per il [video di 7 min con risoluzione 1080p](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Codifica con velocità in bit singola. <br/>File SD o con risoluzione inferiore, non dipendenti dall'ora, a basso costo.|La codifica in un file MP4 di risoluzione SD a bitrate singolo con "H264 Single bitrate SD 16x9" richiede circa 7 minuti.|
| **S2**|Codifica con velocità in bit singola e multipla.<br/>Uso normale per la codifica SD e HD.|La codifica con il set di impostazioni "H264 a bitrate singolo 720p" richiede circa 6 minuti.<br/><br/>La codifica con il set di impostazioni "H264 a bitrate multipli 720p" richiede circa 12 minuti.|
| **S3**|Codifica con velocità in bit singola e multipla.<br/>Video Full HD e con risoluzione 4K. Codifica dipendente dall'ora con completamento più rapido.|La codifica con il set di impostazioni "H264 Single bitrate 1080p" richiede circa 3 minuti.<br/><br/>La codifica con "Codec video H.264 a bitrate multiplo con risoluzione 1080p" preconfigurato richiede circa 8 minuti.|

## <a name="considerations"></a>Considerazioni

* Per i processi di analisi audio e video generati da Servizi multimediali v3 o Video Indexer, il tipo di unità di S3 è fortemente consigliato.
* Se si usa il pool condiviso, vale a dire senza unità riservate, le attività di codifica hanno le stesse prestazioni di quando si usano le unità riservate S1. Non c'è tuttavia alcun limite superiore al tempo che le attività possono trascorrere nello stato in coda e in qualsiasi momento può essere eseguita al massimo una sola attività.

Il resto dell'articolo illustra come usare l'interfaccia della riga di comando di [servizi multimediali V3](https://aka.ms/ams-v3-cli-ref) per ridimensionare MRU.

> [!NOTE]
> Per i processi di analisi audio e video generati da Servizi multimediali v3 o Video Indexer, è fortemente consigliato effettuare il provisioning dell'account con 10 MRU S3. Se sono necessarie più di 10 MRU S3, aprire un ticket di supporto dal [portale di Azure](https://portal.azure.com/).
>
> Attualmente, non è possibile usare la portale di Azure per gestire altre risorse V3. Usare l'[API REST](https://aka.ms/ams-v3-rest-ref), l'[interfaccia della riga di comando](https://aka.ms/ams-v3-cli-ref) o uno degli [SDK](media-services-apis-overview.md#sdks) supportati.

## <a name="prerequisites"></a>Prerequisiti 

[Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Ridimensionare le Media Reserved Unit con l'interfaccia della riga di comando

Eseguire il comando `mru`.

Il comando [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) seguente imposta le Media Reserved Unit sull'account "amsaccount" usando i parametri **count** e **type**.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Fatturazione

L'addebito è basato sul numero di minuti per cui viene effettuato il provisioning di media reserved Unit nell'account. Ciò si verifica indipendentemente dalla presenza di processi in esecuzione nell'account. Per una spiegazione dettagliata, vedere la sezione delle domande frequenti nella pagina dei [prezzi dei Servizi multimediali](https://azure.microsoft.com/pricing/details/media-services/).   

## <a name="next-step"></a>Passaggio successivo

[Analizzare i video](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Vedere anche

* [Quote e limitazioni](limits-quotas-constraints.md)
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
