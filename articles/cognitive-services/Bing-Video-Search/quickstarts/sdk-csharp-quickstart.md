---
title: "Guida introduttiva: Cercare video con l'SDK di Ricerca video Bing per C#"
titleSuffix: Azure Cognitive Services
description: Usare questa guida introduttiva per inviare richieste di ricerca video con l'SDK di Ricerca video Bing per C#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 06/26/2019
ms.author: aahi
ms.openlocfilehash: 3673f18ff38b2ae98180f470b9f76f1fc57ee8b6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442542"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-c"></a>Guida introduttiva: Eseguire una ricerca di video con l'SDK di Ricerca video Bing per C#

Usare questa guida introduttiva per iniziare a cercare notizie con l'SDK di Ricerca video Bing per C#. Mentre Ricerca video Bing ha un'API REST compatibile con la maggior parte dei linguaggi di programmazione, l'SDK offre un modo semplice per integrare il servizio nelle applicazioni. Il codice sorgente per questo esempio è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch) con annotazioni aggiuntive e altre funzionalità.

## <a name="prerequisites"></a>Prerequisiti

* Qualsiasi edizione di [Visual Studio 2017 o versioni successive](https://visualstudio.microsoft.com/downloads/).
* Framework Json.NET, disponibile come [pacchetto NuGet](https://www.nuget.org/packages/Newtonsoft.Json/).

Per aggiungere l'SDK di Ricerca video Bing a un progetto, selezionare **Gestisci pacchetti NuGet** da **Esplora soluzioni** in Visual Studio. Aggiungere il pacchetto `Microsoft.Azure.CognitiveServices.Search.VideoSearch`.

L'installazione del [[pacchetto NuGet dell'SDK di Ricerca video]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0) determina l'installazione anche delle dipendenze seguenti:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Creare e inizializzare un progetto

1. Creare una nuova soluzione di console C# in Visual Studio. Aggiungere quindi quanto segue nel file di codice principale.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;
    ```

2. Creare un'istanza del client creando un nuovo oggetto `ApiKeyServiceClientCredentials` con la chiave di sottoscrizione e chiamando il costruttore.

    ```csharp
    var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

## <a name="send-a-search-request-and-process-the-results"></a>Inviare una richiesta di ricerca ed elaborare i risultati

1. Usare il client per inviare una richiesta di ricerca. Usare "SwiftKey" come query di ricerca.

    ```csharp
    var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
    ```

2. Se vengono restituiti risultati, recuperare il primo con `videoResults.Value[0]`. Stampare quindi ID, titolo e URL del video.

    ```csharp
    if (videoResults.Value.Count > 0)
    {
        var firstVideoResult = videoResults.Value[0];

        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
        Console.WriteLine($"First video name: {firstVideoResult.Name}");
        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
    }
    else
    {
        Console.WriteLine("Couldn't find video results!");
    }
    ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app Web a pagina singola](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Vedere anche 

* [Informazioni sull'API Ricerca video Bing](../overview.md)
* [Esempi di .NET SDK per Servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
