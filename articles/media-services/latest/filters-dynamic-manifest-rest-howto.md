---
title: Creazione di filtri con l'API REST di Servizi multimediali di Azure | Microsoft Docs
description: Questo argomento descrive come creare filtri che il client può usare per trasmettere in streaming sezioni specifiche di un flusso. Servizi multimediali crea manifesti dinamici per consentire questo streaming selettivo.
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
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: 76e6e1595cb8bf49dbbc82c3cae5de80ea718aeb
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786459"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Creazione di filtri con l'API REST di Servizi multimediali

Quando si distribuiscono contenuti ai clienti (flusso di Eventi dal vivo o Video on demand) il client potrebbe avere bisogno di una maggiore flessibilità rispetto a quanto descritto nel file manifesto dell'asset predefinito. Servizi multimediali di Azure consente di definire i filtri account e i filtri asset per i propri contenuti. 

Per una descrizione dettagliata di questa funzionalità e scenari in cui viene usato, vedere [manifesti dinamici](filters-dynamic-manifest-overview.md) e [filtri](filters-concept.md).

Questo argomento illustra come definire un filtro per un asset Video on demand e usare le API REST per creare [Filtri account](https://docs.microsoft.com/rest/api/media/accountfilters) e [Filtri asset](https://docs.microsoft.com/rest/api/media/assetfilters). 

> [!NOTE]
> Assicurarsi di esaminare [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Prerequisiti 

Per completare i passaggi descritti in questo argomento, è necessario:

- Leggere [Filtri e manifesti dinamici](filters-dynamic-manifest-overview.md).
- [Configurare Postman per le chiamate API REST Servizi multimediali di Azure](media-rest-apis-with-postman.md).

    Assicurarsi di seguire l'ultimo passaggio nell'argomento [Ottieni Token in Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-filter"></a>Definire un filtro  

Di seguito è riportato l'esempio del **Corpo della richiesta** che definisce le condizioni di selezione brano che vengono aggiunte al manifesto. Questo filtro include tracce audio EC-3 e tracce video con velocità in bit nell'intervallo 0-1000000.

```json
{
    "properties": {
        "tracks": [
          {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Audio",
                        "operation": "Equal"
                    },
                    {
                        "property": "FourCC",
                        "value": "EC-3",
                        "operation": "Equal"
                    }
                ]
            },
            {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Video",
                        "operation": "Equal"
                    },
                    {
                        "property": "Bitrate",
                        "value": "0-1000000",
                        "operation": "Equal"
                    }
                ]
            }
        ]
     }
}
```

## <a name="create-account-filters"></a>Creare filtri account

Nella raccolta di Postman che è stata scaricata, selezionare **Filtri account**->**Crea o aggiorna un filtro account**.

Il metodo di richiesta HTTP **PUT** è simile a:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Selezionare la scheda **Corpo** e incollare il codice json [definito in precedenza](#define-a-filter).

Selezionare **Send** (Invia). 

Il filtro è stato creato.

Per altre informazioni, vedere [Creare o aggiornare](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate). Vedere anche [Esempi JSON per i filtri](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Creare filtri asset  

Nella raccolta Postman "Servizi multimediali v3" scaricata, selezionare **Asset**->**Crea o aggiorna filtro asset**.

Il metodo di richiesta HTTP **PUT** è simile a:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Selezionare la scheda **Corpo** e incollare il codice json [definito in precedenza](#define-a-filter).

Selezionare **Send** (Invia). 

Il filtro asset è stato creato.

Per informazioni dettagliate su come creare o aggiornare i filtri asset, vedere [Crea o aggiorna](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate). Vedere anche [Esempi JSON per i filtri](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter). 

## <a name="associate-filters-with-streaming-locator"></a>Associare i filtri localizzatore di Streaming

È possibile specificare un elenco di filtri di asset o account, si applica anche per il localizzatore di Streaming. Il [creazione dinamica dei pacchetti (Endpoint di Streaming)](dynamic-packaging-overview.md) si applica questo elenco di filtri insieme a quelli del client specifica l'URL. Questa combinazione genera una [manifesto dinamico](filters-dynamic-manifest-overview.md), basata su filtri nell'URL + filtri è specificare nel localizzatore di Streaming. È consigliabile usare questa funzionalità se si desidera applicare i filtri, ma non si desidera esporre i nomi dei filtri nell'URL.

Per creare e associare filtri a un localizzatore di Streaming usando REST, usare il [creare i localizzatori di Streaming -](https://docs.microsoft.com/rest/api/media/streaminglocators/create) API e specificare `properties.filters` nel [corpo della richiesta](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body).
                                
## <a name="stream-using-filters"></a>Utilizzo dei filtri di Stream

Dopo aver definito i filtri, è possibile usarli nell'URL di streaming. È possibile applicare filtri ai protocolli di streaming a bitrate adattivo: Apple HLS (HTTP Live Streaming), MPEG-DASH e Smooth Streaming.

Nella tabella seguente sono disponibili alcuni esempi di URL con filtri:

|Protocol|Esempio|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Passaggi successivi

[Video in streaming](stream-files-tutorial-with-rest.md) 
