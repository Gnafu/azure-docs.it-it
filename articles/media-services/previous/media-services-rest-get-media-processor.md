---
title: Come creare un'istanza del processore di contenuti multimediali usando REST | Microsoft Docs
description: Informazioni su come creare un componente del processore di contenuti multimediali per codificare, decodificare, convertire il formato, crittografare o decrittografare contenuti multimediali per Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: f9ff1997-0da6-4528-aaed-792837e5be41
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: d342cff6d322195ee88a74215f814be7d702aa5e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60761977"
---
# <a name="how-to-get-a-media-processor-instance"></a>Come ottenere un'istanza del processore di contenuti multimediali
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Panoramica
Un processore di contenuti multimediali è un componente che gestisce un'attività di elaborazione video o audio specifica, ad esempio la codifica, la conversione del formato, la crittografia o la decrittografia di contenuti multimediali. Tutte le attività inviate a Servizi multimediali richiedono un processore di contenuti multimediali per codificare, crittografare o convertire il contenuto video o audio. 

## <a name="azure-media-processors"></a>Processori di contenuti multimediali di Azure 

L'argomento seguente fornisce elenchi di processori di contenuti multimediali:

* [Processori di contenuti multimediali di codifica](scenarios-and-availability.md#encoding-media-processors)
* [Processori di contenuti multimediali di analisi](scenarios-and-availability.md#analytics-media-processors)

>[!NOTE]
>Quando si accede alle entità in Servizi multimediali, è necessario impostare valori e campi di intestazione specifici nelle richieste HTTP. Per altre informazioni, vedere [Panoramica dell'API REST di Servizi multimediali](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Connettersi a Servizi multimediali

Per informazioni su come connettersi all'API AMS, vedere [Accedere all'API di Servizi multimediali di Azure con l'autenticazione di Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 


## <a name="get-a-media-processor"></a>Ottenere un processore di contenuti multimediali.

La chiamata REST seguente mostra come ottenere un'istanza del processore di contenuti multimediali per nome, in questo caso **Media Encoder Standard**. 

Richiesta:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.17
    Host: media.windows.net

Risposta:

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }


## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Fasi successive
Dopo avere ottenuto un'istanza del processore di contenuti multimediali, passare all'articolo [Come codificare un asset](media-services-rest-get-started.md), che illustra come usare Media Encoder Standard per codificare un asset.

