---
title: 'Guida introduttiva: Pubblicare una knowledge base in REST, C# - QnA Maker'
titleSuffix: Azure Cognitive Services
description: Questa guida di avvio rapido C# basata su REST illustra la procedura di pubblicazione della knowledge base per eseguire il push della versione più recente della knowledge base testata in un indice di Ricerca di Azure dedicato che rappresenta la knowledge base pubblicata. Viene inoltre creato un endpoint che può essere chiamato nell'applicazione o nel chat bot.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: dbd6bddd06713cae7ac69d19e8d13f20fa04f6d0
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306881"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>Avvio rapido: Pubblicare una knowledge base in QnA Maker con C#

Questa guida introduttiva basata su REST illustra la procedura di pubblicazione della knowledge base (KB) a livello di codice. Con la pubblicazione viene eseguito il push dell'ultima versione della knowledge base in un indice dedicato di Ricerca di Azure e viene creato un endpoint che può essere chiamato nell'applicazione o nel chatbot.

In questa guida introduttiva viene chiamata l'API QnA Maker seguente:
* [Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) (Pubblicazione): con questa API non sono richieste informazioni nel corpo della richiesta.

## <a name="prerequisites"></a>Prerequisiti

* Versione più recente di [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/).
* È necessario disporre di un [servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Per recuperare la chiave, selezionare **Chiavi** in **GESTIONE RISORSE** nel dashboard. 
* ID della knowledge base (KB) QnA Maker trovato nell'URL nel parametro della stringa di query kbid come mostrato di seguito.

    ![ID della knowledge base di QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Se non si ha ancora una knowledge base, è possibile crearne una di esempio da usare per questa guida introduttiva: [Creare una nuova knowledge base](create-new-kb-csharp.md).

> [!NOTE] 
> I file di soluzione completi sono disponibili nel [repository GitHub **Azure-Samples/cognitive-services-qnamaker-csharp**](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-knowledge-base-project"></a>Creare il progetto per la knowledge base

1. Aprire Visual Studio 2019 Community Edition.
1. Creare un nuovo progetto **App console (.NET Core)** e assegnare il nome `QnaMakerQuickstart` al progetto. Accettare le impostazioni predefinite per le altre impostazioni.

## <a name="add-required-dependencies"></a>Aggiungere le dipendenze obbligatorie

All'inizio di Program.cs sostituire la singola istruzione using con le righe seguenti per aggiungere le dipendenze necessarie al progetto:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=1-2 "Add the required dependencies")]

## <a name="add-required-constants"></a>Aggiungere le costanti obbligatorie

Nel metodo **Main** aggiungere le costanti necessarie per accedere a QnA Maker. Sostituire i valori con quelli personalizzati.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=11-14 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Aggiungere la richiesta POST per pubblicare la knowledge base

Dopo le costanti richieste, aggiungere il codice seguente, che effettua una richiesta HTTPS all'API di QnA Maker per pubblicare una knowledge base e riceve la risposta:

[!code-csharp[Add HTTP Post request and response](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=16-29&dedent=8 "Add HTTP Post request and response")]

In caso di pubblicazione riuscita la chiamata API restituisce uno stato 204 senza alcun contenuto nel corpo della risposta. 
 
## <a name="build-and-run-the-program"></a>Compilare ed eseguire il programma

Compilare ed eseguire il programma. La richiesta per pubblicare la knowledge base verrà inviata automaticamente all'API QnA Maker e la risposta verrà stampata nella finestra della console.

Dopo aver pubblicato la knowledge base, è possibile eseguire query su di essa dall'endpoint con un'applicazione client o un chatbot. 

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Passaggi successivi

Dopo la pubblicazione della knowledge base, è necessario l'[URL dell'endpoint per generare una risposta](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API REST QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
