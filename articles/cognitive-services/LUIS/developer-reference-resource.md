---
title: Risorse per sviluppatori-Language Understanding
titleSuffix: Azure Cognitive Services
description: Gli sviluppatori hanno sia API REST che SDK per Language Understanding.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: diberry
ms.openlocfilehash: 01d01461c540949693855ca407920d52b22ad714
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106856"
---
# <a name="developer-resources-for-language-understanding"></a>Risorse per gli sviluppatori per Language Understanding

Gli sviluppatori possono usare sia API REST che SDK per Language Understanding. 

## <a name="azure-resource-management"></a>Gestione risorse di Azure

Usare il livello di gestione dei servizi cognitivi di Azure per creare, modificare, elencare ed eliminare la risorsa di Language Understanding o del servizio cognitivo.

Trovare la documentazione di riferimento basata sullo strumento:

* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/cognitiveservices?view=azure-cli-latest#az_cognitiveservices_list)

* [AzureRM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)

## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding la creazione e la stima delle richieste

È possibile accedere al servizio Language Understanding da una risorsa di Azure che è necessario creare. Sono disponibili due risorse: la creazione e la stima delle risorse degli endpoint. Entrambe queste risorse consentono di controllare le risorse LUIS. 

### <a name="rest-apis"></a>API REST

Le API REST di creazione ed endpoint di stima sono disponibili nelle API REST:

* Documentazione di [riferimento](https://go.microsoft.com/fwlink/?linkid=2092087) per la creazione
* [Documentazione di riferimento](https://go.microsoft.com/fwlink/?linkid=2092356) del runtime di stima

### <a name="language-based-sdks"></a>SDK basati su linguaggio

|Linguaggio |Documentazione di riferimento|Pacchetto|Esempi|Guide introduttive|
|--|--|--|--|--|
|C#|[Creazione](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Previsioni](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[Creazione di NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[Stima di NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Esempi di .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Crea e Gestisci app](sdk-csharp-quickstart-authoring-app.md)<br>[Endpoint di stima query](sdk-csharp-quickstart-query-prediction-endpoint.md)|
|Go|[Creazione e stima](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Creazione](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Previsioni](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Creazione tramite REST](luis-get-started-go-add-utterance.md)<br>[Stima con REST](luis-get-started-go-get-intent.md)|
|Java|[Creazione e stima](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Creazione di Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Stima Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Creazione](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Previsioni](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Creazione](luis-get-started-java-add-utterance.md)<br>[Previsioni](luis-get-started-java-get-intent.md)
|Node.js|[Creazione](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Previsioni](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[Creazione NPM](https://www.npmjs.com/package/azure-cognitiveservices-luis-authoring)<br>[Stima NPM](https://www.npmjs.com/package/azure-cognitiveservices-luis-runtime)|[Creazione](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Previsioni](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Creazione tramite REST](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-node-get-intent)<br>[Stima con REST](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-node-add-utterance)|
|Python|[Creazione e stima](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/?view=azure-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Creazione](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Creazione](sdk-python-quickstart-authoring-app.md)<br>[Stima con REST](luis-get-started-python-get-intent.md)

## <a name="other-tools-and-sdks"></a>Altri strumenti e SDK

Bot Framework è disponibile come [SDK](https://github.com/Microsoft/botframework) in un'ampia gamma di linguaggi e come servizio tramite il [servizio Azure bot](https://dev.botframework.com/). 

Bot Framework offre [diversi strumenti](https://github.com/microsoft/botbuilder-tools) che consentono di Language Understanding, tra cui:

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) -compila i modelli di comprensione del linguaggio Luis usando i file Markdown
* [Interfaccia](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) della riga di comando Luis: creare e gestire le applicazioni Luis.ai
* Gestire le app padre e figlio tramite [Dispatch](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) -genera automaticamente le classi C#/typescript di backup per gli Intent ed entità di Luis.
* [Emulatore bot](https://github.com/Microsoft/BotFramework-Emulator/releases) : applicazione desktop che consente agli sviluppatori di bot di testare ed eseguire il debug di bot creati usando bot Framework SDK


## <a name="next-steps"></a>Passaggi successivi

Informazioni sui codici di [errore http](luis-reference-response-codes.md) comuni