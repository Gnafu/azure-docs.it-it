---
title: 'Esempio: Analisi video in tempo reale - Visione artificiale'
titleSuffix: Azure Cognitive Services
description: Informazioni su come eseguire l'analisi near real time di fotogrammi acquisiti da un video in diretta streaming usando l'API Visione artificiale.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 25aed0f042050ebadbc6054fcbf0c68dbf782e5e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859083"
---
# <a name="how-to-analyze-videos-in-real-time"></a>Come analizzare video in tempo reale

Questa guida mostra come eseguire l'analisi near real time su fotogrammi acquisiti da un video in diretta streaming. Le fasi fondamentali di questo tipo di sistema sono:

- Acquisizione di fotogrammi da un'origine video
- Selezione dei fotogrammi da analizzare
- Invio di tali fotogrammi all'API
- Uso di ogni risultato dell'analisi che viene restituito dalla chiamata all'API

Questi esempi sono scritti in C# e il codice è disponibile in GitHub all'indirizzo seguente: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>Approccio

Esistono molti modi di risolvere il problema dell'esecuzione di un'analisi near real time su video in streaming. Iniziamo delineando tre approcci con livelli di sofisticazione crescenti.

### <a name="a-simple-approach"></a>Approccio semplice

Il progetto più semplice per un sistema di analisi near real time prevede un ciclo infinito in cui a ogni iterazione si acquisisce un fotogramma, lo si analizza e successivamente se ne usa il risultato:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        AnalysisResult r = await Analyze(f);
        ConsumeResult(r);
    }
}
```

Se l'analisi consiste in un algoritmo lato client leggero, questo approccio è adeguato. Quando invece l'analisi avviene nel cloud, la latenza interessata indica che una chiamata all'API può richiedere vari secondi, durante i quali non vengono acquisite immagini e il thread essenzialmente non genera alcun risultato. La frequenza massima dei fotogrammi è limitata dalla latenza delle chiamate all'API.

### <a name="parallelizing-api-calls"></a>Abbinamento in parallelo di chiamate all'API

Se l'uso di un ciclo con thread singolo semplice ha senso per un algoritmo semplificato su lato client, non è invece adatto in presenza della latenza insita nelle chiamata all'API cloud. Per risolvere questo problema, le chiamate all'API a esecuzione prolungata devono essere eseguite in parallelo con l'acquisizione dei fotogrammi. In C# è possibile applicare questa soluzione usando il parallelismo basato sulle attività, ad esempio:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        var t = Task.Run(async () =>
        {
            AnalysisResult r = await Analyze(f);
            ConsumeResult(r);
        }
    }
}
```

Questo approccio avvia ciascuna analisi in un'attività separata, che può essere eseguita in background mentre si continua ad acquisire nuovi fotogrammi. Questa soluzione evita il blocco del thread principale durante l'attesa dei risultati di una chiamata API, ma fa perdere alcune delle garanzie offerte dalla versione semplice. Possono infatti verificarsi più chiamate API in parallelo e i risultati potrebbero venire restituiti in un ordine non corretto. Questo approccio può anche fare in modo che più thread entrino nella funzione ConsumeResult() simultaneamente. Questo comportamento può essere pericoloso, se la funzione non è thread-safe. Questo codice semplice, inoltre, non tiene traccia delle attività che vengono create, pertanto le eventuali eccezioni scompaiono silenziosamente. L'ingrediente finale da aggiungere, quindi, è un thread "consumer" che tenga traccia delle attività di analisi, generi eccezioni, interrompa le attività a esecuzione prolungata e assicuri che i risultati vengano usati nell'ordine corretto e uno alla volta.

### <a name="a-producer-consumer-design"></a>Progetto producer-consumer

Nel sistema "producer-consumer" finale è presente un thread producer simile al ciclo infinito precedente. Invece di usare i risultati dell'analisi man mano che diventano disponibili, il producer inserisce semplicemente le attività in una coda per tenerne traccia.

```csharp
// Queue that will contain the API call tasks.
var taskQueue = new BlockingCollection<Task<ResultWrapper>>();

// Producer thread.
while (true)
{
    // Grab a frame.
    Frame f = GrabFrame();

    // Decide whether to analyze the frame.
    if (ShouldAnalyze(f))
    {
        // Start a task that will run in parallel with this thread.
        var analysisTask = Task.Run(async () =>
        {
            // Put the frame, and the result/exception into a wrapper object.
            var output = new ResultWrapper(f);
            try
            {
                output.Analysis = await Analyze(f);
            }
            catch (Exception e)
            {
                output.Exception = e;
            }
            return output;
        }

        // Push the task onto the queue.
        taskQueue.Add(analysisTask);
    }
}
```

È inoltre presente un thread consumer che prende le attività dalla coda, attende che siano completate e visualizza il risultato o solleva l'eccezione che era stata generata. L'uso della coda assicura che i risultati vengano usati uno alla volta, nell'ordine corretto, senza limitare la frequenza dei fotogrammi massima del sistema.

```csharp
// Consumer thread.
while (true)
{
    // Get the oldest task.
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Await until the task is completed.
    var output = await analysisTask;

    // Consume the exception or result.
    if (output.Exception != null)
    {
        throw output.Exception;
    }
    else
    {
        ConsumeResult(output.Analysis);
    }
}
```

## <a name="implementing-the-solution"></a>Implementazione della soluzione

### <a name="getting-started"></a>Introduzione

Per fare in modo che l'app sia operativa il più rapidamente possibile, è stato implementato il sistema descritto in precedenza in modo che sia sufficientemente flessibile per implementare molti scenari e al contempo sia facile da usare. Per accedere al codice, passare a [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

La libreria contiene la classe FrameGrabber, che implementa il sistema producer-consumer discusso in precedenza per elaborare i fotogrammi di video ripresi da una webcam. L'utente può specificare il formato esatto della chiamata all'API e la classe usa gli eventi per indicare al codice chiamante quando viene acquisito un nuovo fotogramma o quando è disponibile un nuovo risultato dell'analisi.

Per illustrare alcune delle possibilità, sono disponibili due app di esempio che usano la libreria. La prima è una semplice app console ed è una versione semplificata di quella riprodotta di seguito. Acquisisce i fotogrammi dalla webcam predefinita e li invia all'API Viso per il rilevamento del volto.

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;
using VideoFrameAnalyzer;

namespace BasicConsoleSample
{
    internal class Program
    {
        const string ApiKey = "<your API key>";
        const string Endpoint = "https://<your API region>.api.cognitive.microsoft.com";

        private static void Main(string[] args)
        {
            // Create grabber.
            FrameGrabber<DetectedFace[]> grabber = new FrameGrabber<DetectedFace[]>();

            // Create Face API Client.
            FaceClient faceClient = new FaceClient(new ApiKeyServiceClientCredentials(ApiKey))
            {
                Endpoint = Endpoint
            };

            // Set up a listener for when we acquire a new frame.
            grabber.NewFrameProvided += (s, e) =>
            {
                Console.WriteLine($"New frame acquired at {e.Frame.Metadata.Timestamp}");
            };

            // Set up Face API call.
            grabber.AnalysisFunction = async frame =>
            {
                Console.WriteLine($"Submitting frame acquired at {frame.Metadata.Timestamp}");
                // Encode image and submit to Face API.
                return (await faceClient.Face.DetectWithStreamAsync(frame.Image.ToMemoryStream(".jpg"))).ToArray();
            };

            // Set up a listener for when we receive a new result from an API call.
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.TimedOut)
                    Console.WriteLine("API call timed out.");
                else if (e.Exception != null)
                    Console.WriteLine("API call threw an exception.");
                else
                    Console.WriteLine($"New result received for frame acquired at {e.Frame.Metadata.Timestamp}. {e.Analysis.Length} faces detected");
            };

            // Tell grabber when to call API.
            // See also TriggerAnalysisOnPredicate
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running in the background.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for key press to stop.
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();

            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```

La seconda app di esempio è un po' più interessante e consente di scegliere quale API chiamare sui fotogrammi video. Sul lato sinistro, l'app visualizza un'anteprima del video in diretta, mentre sul lato destro visualizza il risultato più recente dell'API sovrapposto al frame corrispondente.

Nella maggior parte delle modalità, vi sarà un ritardo visibile tra il video in diretta a sinistra e l'analisi visualizzata a destra. Il ritardo è il tempo necessario per effettuare la chiamata all'API. La modalità "EmotionsWithClientFaceDetect" fa eccezione. Questa modalità esegue il rilevamento del viso localmente nel computer client usando OpenCV, prima di inviare le immagini a Servizi cognitivi. In questo modo è possibile visualizzare immediatamente il viso rilevato e quindi aggiornare le emozioni in un secondo momento dopo che la chiamata all'API ha prodotto il risultato. Ciò dimostra la possibilità di un approccio "ibrido", in cui è possibile eseguire una semplice elaborazione nel client e successivamente usare le API Servizi cognitivi per potenziare l'elaborazione con questa analisi più avanzata quando necessario.

![Screenshot dell'app LiveCameraSample che mostra un'immagine con tag visualizzati](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Integrazione nella codebase

Per iniziare a usare questo esempio, seguire questa procedura:

1. Ottenere le chiavi API per le API Visione da [Sottoscrizioni](https://azure.microsoft.com/try/cognitive-services/). Per l'analisi dei fotogrammi video, le API applicabili sono:
    - [API Visione artificiale](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [API Viso](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Clonare il repository di GitHub [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)

3. Aprire l'esempio in Visual Studio 2015 o versioni successive, quindi compilare ed eseguire le applicazioni di esempio:
    - Per BasicConsoleSample, la chiave dell'API Viso è hardcoded direttamente in [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - Per LiveCameraSample, le chiavi devono essere immesse nel riquadro Impostazioni dell'app. Queste chiavi saranno persistenti tra le sessioni come i dati dell'utente.

Quando si è pronti per l'integrazione, **fare semplicemente riferimento alla libreria VideoFrameAnalyzer dai propri progetti.**

Le funzionalità di riconoscimento delle immagini, della voce, dei video o del testo di VideoFrameAnalyzer usano Servizi cognitivi di Azure. Microsoft riceve le immagini, l'audio, i video e altri dati che vengono caricati (tramite questa app) e può usarli per migliorare i servizi. Gli sviluppatori devono contribuire a proteggere i dati degli utenti inviati dall'app a Servizi cognitivi di Azure.

## <a name="summary"></a>Summary

In questa guida si è appreso come eseguire l'analisi near real time di video in diretta streaming usando le API Viso e Visione artificiale e come usare il codice di esempio per iniziare. È possibile iniziare a creare la propria app con chiavi API gratuite nella [pagina di accesso a Servizi cognitivi di Azure](https://azure.microsoft.com/try/cognitive-services/).

È gradito ricevere commenti e suggerimenti nel [repository GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) o, per commenti più approfonditi sulle API, nel [sito UserVoice](https://cognitive.uservoice.com/).

