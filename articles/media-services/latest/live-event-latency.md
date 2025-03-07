---
title: Latenza LiveEvent in Servizi multimediali di Azure | Microsoft Docs
description: Panoramica della latenza LiveEvent e procedura per l'impostazione di una bassa latenza.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 04/22/2019
ms.author: juliako
ms.openlocfilehash: 393b87aeed759950b946ccb45a008da9af4b7ebe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64702797"
---
# <a name="live-event-latency-in-media-services"></a>Latenza degli eventi live in Servizi multimediali

Questo articolo illustra come impostare una bassa latenza in un [evento live](https://docs.microsoft.com/rest/api/media/liveevents). Esamina anche alcuni risultati tipici visualizzati quando si usano le impostazioni di bassa latenza in diversi lettori. I risultati variano a seconda della rete CDN e della latenza di rete.

Per usare la nuova funzionalità **LowLatency** impostare il flag **StreamOptionsFlag** su **LowLatency** nel **LiveEvent**. Quando si crea [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) per la riproduzione HLS, impostare [LiveOutput.Hls.fragmentsPerTsSegment](https://docs.microsoft.com/rest/api/media/liveoutputs/create#hls) su 1. Quando il flusso è in esecuzione, è possibile usare [Azure Media Player](https://ampdemo.azureedge.net/) (pagina demo di AMP) e impostare le opzioni di riproduzione per l'uso del profilo di euristica a bassa latenza.

> [!NOTE]
> Attualmente, il LowLatency HeuristicProfile in Azure Media Player è progettato per la riproduzione di flussi nel protocollo MPEG-DASH, con formato CSF o CMAF (ad esempio, `format=mdp-time-csf` o `format=mdp-time-cmaf`). 

L'esempio di .NET seguente mostra come impostare **LowLatency** nel **LiveEvent**:

```csharp
LiveEvent liveEvent = new LiveEvent(
            location: mediaService.Location, 
            description: "Sample LiveEvent for testing",
            vanityUrl: false,
            encoding: new LiveEventEncoding(
                        // Set this to Standard to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                        encodingType:LiveEventEncodingType.None, 
                        presetName:null
                    ),
            input: new LiveEventInput(LiveEventInputProtocol.RTMP,liveEventInputAccess), 
            preview: liveEventPreview,
            streamOptions: new List<StreamOptionsFlag?>()
            {
                // Set this to Default or Low Latency
                // To use low latency optimally, you should tune your encoder settings down to 1 second "Group Of Pictures" (GOP) length instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

Vedere l'esempio completo: [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

## <a name="live-events-latency"></a>Latenza di eventi live

La tabella seguente mostra i risultati tipici della latenza (quando è attivato il flag LowLatency) in Servizi multimediali, misurati dal momento in cui il feed di contributo raggiunge il servizio al momento in cui un utente vede la riproduzione sul lettore. Per un uso ottimale della bassa latenza, è consigliabile ridurre le impostazioni del codificatore fino alla durata di un 1 secondo GOP (Group Of Pictures). Quando si usa una durata GOP maggiore, ridurre al minimo il consumo di larghezza di banda e ridurre la velocità in bit nella stessa frequenza dei fotogrammi. È particolarmente utile nei video con meno movimento.

### <a name="pass-through"></a>Pass-through 

||Bassa latenza (GOP 2 secondi) abilitata|Bassa latenza (GOP 1 secondo) abilitata|
|---|---|---|
|DASH in AMP|10 secondi|8 secondi|
|HLS nel lettore iOS nativo|14 secondi|10 secondi|

### <a name="live-encoding"></a>Codifica live

||Bassa latenza (GOP 2 secondi) abilitata|Bassa latenza (GOP 1 secondo) abilitata|
|---|---|---|
|DASH in AMP|14 secondi|10 secondi|
|HLS nel lettore iOS nativo|18s|13s|

> [!NOTE]
> La latenza end-to-end può variare in base alle condizioni della rete locale o con l'introduzione di un livello di memorizzazione nella cache della rete CDN. È consigliabile testare l'esatta configurazione in uso.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dello streaming live](live-streaming-overview.md)
- [Esercitazione sullo streaming live](stream-live-tutorial-with-api.md)

