---
title: Panoramica dei codificatori multimediali su richiesta di Azure | Microsoft Docs
description: Questo argomento offre una panoramica dei codificatori multimediali su richiesta di Azure.
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
ms.date: 06/25/2019
ms.author: juliako
ms.openlocfilehash: 5c55b419b88a66d2e1acf1687478ab35d9f0a059
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019040"
---
# <a name="overview-of-azure-on-demand-media-encoders"></a>Panoramica dei codificatori multimediali su richiesta di Azure 

> [!NOTE]
> Non saranno aggiunte nuove caratteristiche o funzionalità a Servizi multimediali v2. <br/>Esplorare l'ultima versione, [Servizi multimediali v3](https://docs.microsoft.com/azure/media-services/latest/). Vedere anche [linee guida sulla migrazione da V2 a V3](../latest/migrate-from-v2-to-v3.md)

Servizi multimediali di Azure offre diverse opzioni per la codifica di servizi multimediali nel cloud.

Quando si iniziano ad utilizzare i servizi multimediali, è importante comprendere la differenza tra codec e formati di file.
I codec sono costituiti da software che implementa gli algoritmi di compressione/decompressione, mentre i formati di file sono contenitori che includono il video compresso.

Servizi multimediali include la funzionalità per la creazione dinamica dei pacchetti, che consente di distribuire contenuto con codifica Smooth Streaming o MP4 a bitrate adattivo nei formati supportati da Servizi multimediali, ovvero MPEG-DASH, HLS, Smooth Streaming, senza dover ricreare i pacchetti in questi formati di streaming.

Quando l'account di Servizi multimediali viene creato, un endpoint di streaming **predefinito** viene aggiunto all'account con stato **Arrestato**. Per avviare lo streaming del contenuto e sfruttare i vantaggi della creazione dinamica dei pacchetti e della crittografia dinamica, l'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato **In esecuzione**. La fatturazione per gli endpoint di streaming si verifica ogni volta che l'endpoint si trova in uno stato di **esecuzione** .

Servizi multimediali supporta i seguenti codificatori su richiesta descritti in questo articolo:

* [Codificatore multimediale standard](media-services-encode-asset.md#media-encoder-standard)
* [Flusso di lavoro Premium del codificatore multimediale](media-services-encode-asset.md#media-encoder-premium-workflow)

In questo articolo è fornita una breve panoramica dei codificatori multimediali su richiesta e sono presenti collegamenti ad articoli che contengono informazioni più dettagliate. L'argomento fornisce inoltre il confronto dei codificatori.

Per impostazione predefinita, in ciascun account di Servizi multimediali può essere attiva una sola attività di codifica alla volta. È tuttavia possibile riservare unità di codifica che consentano di eseguire più attività di codifica contemporaneamente, una per ciascuna unità acquistata. Per informazioni, vedere [Scalabilità dell’unità di codifica](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Media Encoder Standard

### <a name="how-to-use"></a>Utilizzo
[Come codificare con Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Formati
[Codec e formati](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Impostazioni predefinite
Media Encoder Standard viene configurato mediante un set di impostazioni descritto [qui](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Metadati di input e output
I metadati di input dei codificatori sono descritti [qui](media-services-input-metadata-schema.md).

I metadati di output dei codificatori sono descritti [qui](media-services-output-metadata-schema.md).

### <a name="generate-thumbnails"></a>Generare anteprime
Per informazioni, vedere l'argomento [Come generare anteprime utilizzando Media Encoder Standard](media-services-advanced-encoding-with-mes.md#thumbnails).

### <a name="trim-videos-clipping"></a>Tagliare video (ritaglio)
Per informazioni, vedere [Come tagliare video usando Media Encoder Standard](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Creare sovrimpressioni
Per informazioni, vedere [Come creare sovrimpressioni usando Media Encoder Standard](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Vedere anche
[Blog di Servizi multimediali](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>Media Encoder Premium Workflow
### <a name="overview"></a>Panoramica
[Introduzione alla codifica Premium in Servizi multimediali di Azure](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>Utilizzo
Il flusso di lavoro Premium del codificatore multimediale viene configurato usando flussi di lavoro complessi. Per creare e aggiornare i file di un flusso di lavoro, è possibile usare lo strumento [Progettazione flussi di lavoro](media-services-workflow-designer.md) .

[Come usare la codifica Premium in Servizi multimediali di Azure](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>Problemi noti
Se il video di input non contiene i sottotitoli codificati, l'asset di output conterrà comunque un file TTML vuoto.

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Articoli correlati
* [Eseguire attività di codifica avanzata personalizzando i set di impostazioni di Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Quote e limitazioni](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
