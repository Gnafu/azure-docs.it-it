---
title: "Guida introduttiva: Riconoscere l'input penna con l'API REST Riconoscimento input penna e C#"
titleSuffix: Azure Cognitive Services
description: Usare l'API Riconoscimento input penna per avviare il riconoscimento di tratti input penna.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 6a1b4ab43a7d87ac1162a7f0a3556d6bc3bfbfab
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721254"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Guida introduttiva: Riconoscere l'input penna con l'API REST Riconoscimento input penna e C#

Usare questo avvio rapido per iniziare a inviare tratti input penna all'API Riconoscimento input penna. Questa applicazione C# invia una richiesta API contenenti i dati del tratto input penna in formato JSON e riceve la risposta.

L'applicazione è scritta in C#, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione.

In genere è necessario chiamare l'API da un'app di input penna. Questa guida di avvio rapido invia i dati del tratto input penna per l'esempio scritto a mano seguente da un file JSON.

![immagine di testo scritto a mano](../media/handwriting-sample.jpg)

Il codice sorgente per questo avvio rapido è disponibile su [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Prerequisiti

- Qualsiasi edizione di [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Per installare Newtonsoft.Json come pacchetto NuGet in Visual Studio:
        1. Fare clic con il pulsante destro del mouse su **Solution Manager**
        2. Scegliere **Gestisci pacchetti NuGet...**
        3. Cercare `Newtonsoft.Json` e installare il pacchetto
- Se si usa Linux/MacOS, questa applicazione può essere eseguita tramite [Mono](https://www.mono-project.com/).

- I dati di esempio del tratto input penna per questo avvio rapido sono disponibili in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json).

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]


## <a name="create-a-new-application"></a>Creare una nuova applicazione

1. In Visual Studio creare una nuova soluzione console e aggiungere i pacchetti seguenti. 

    ```csharp
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    ```

2. Creare variabili per la chiave di sottoscrizione e per l'endpoint. Di seguito è riportato l'URI che è possibile usare per il riconoscimento input penna. Questo URI verrà aggiunto all'endpoint di servizio in un secondo momento per creare l'URI della richiesta API.

    ```csharp
    // Replace the subscriptionKey string with your valid subscription key.
    const string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Replace the dataPath string with a path to the JSON formatted ink stroke data.
    const string dataPath = @"PATH-TO-INK-STROKE-DATA"; 

    // URI information for ink recognition:
    const string endpoint = "https://api.cognitive.microsoft.com";
    const string inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Creare una funzione per inviare richieste

1. Creare una nuova funzione asincrona denominata `Request` che accetta le variabili create in precedenza.

2. Impostare il protocollo di sicurezza del client e le informazioni di intestazione con l'oggetto `HttpClient`. Assicurarsi di aggiungere la chiave di sottoscrizione all'intestazione `Ocp-Apim-Subscription-Key`. Creare quindi un oggetto `StringContent` per la richiesta.
 
3. Inviare la richiesta con `PutAsync()`. Se la richiesta ha esito positivo, viene restituita la risposta.  
    
    ```csharp
    static async Task<string> Request(string apiAddress, string endpoint, string subscriptionKey, string requestData){
        
        using (HttpClient client = new HttpClient { BaseAddress = new Uri(apiAddress) }){
            System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

            var content = new StringContent(requestData, Encoding.UTF8, "application/json");
            var res = await client.PutAsync(endpoint, content);
            if (res.IsSuccessStatusCode){
                return await res.Content.ReadAsStringAsync();
            }
            else{
                return $"ErrorCode: {res.StatusCode}";
            }
        }
    }
    ```

## <a name="send-an-ink-recognition-request"></a>Inviare una richiesta di riconoscimento input penna

1. Creare una nuova funzione denominata `recognizeInk()`. Creare la richiesta e inviarla chiamando la funzione `Request()` con l'endpoint, la chiave di sottoscrizione, l'URL per l'API e i dati del tratto input penna.

2. Deserializzare l'oggetto JSON e visualizzarlo nella console. 
    
    ```csharp
    static void recognizeInk(string requestData){

        //construct the request
        var result = Request(
            endpoint,
            inkRecognitionUrl,
            subscriptionKey,
            requestData).Result;

        dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
        System.Console.WriteLine(jsonObj);
    }
    ```

## <a name="load-your-digital-ink-data"></a>Caricare i dati dell'input penna

Creare una funzione denominata `LoadJson()` per caricare il file JSON con i dati dell'input penna. Usare `StreamReader` e `JsonTextReader` per creare `JObject` e restituirlo.
    
```csharp
public static JObject LoadJson(string fileLocation){

    var jsonObj = new JObject();

    using (StreamReader file = File.OpenText(fileLocation))
    using (JsonTextReader reader = new JsonTextReader(file)){
        jsonObj = (JObject)JToken.ReadFrom(reader);
    }
    return jsonObj;
}
```

## <a name="send-the-api-request"></a>Inviare la richiesta API

1. Nel metodo main dell'applicazione caricare i dati JSON con la funzione creata in precedenza. 

2. Chiamare la funzione `recognizeInk()` creata in precedenza. Usare `System.Console.ReadKey()` per mantenere aperta la finestra della console dopo l'esecuzione dell'applicazione.
    
    ```csharp
    static void Main(string[] args){

        var requestData = LoadJson(dataPath);
        string requestString = requestData.ToString(Newtonsoft.Json.Formatting.None);
        recognizeInk(requestString);
        System.Console.WriteLine("\nPress any key to exit ");
        System.Console.ReadKey();
        }
    ```

## <a name="run-the-application-and-view-the-response"></a>Eseguire l'applicazione e visualizzare la risposta

Eseguire l'applicazione. Viene restituita una risposta con esito positivo in formato JSON. È anche possibile trovare la risposta JSON in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle API REST](https://go.microsoft.com/fwlink/?linkid=2089907)


Per vedere come funziona l'API Riconoscimento input penna in un'app di input penna, esaminare le applicazioni di esempio seguenti in GitHub:
* [C# e piattaforma UWP (Universal Windows Platform)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# e WPF (Windows Presentation Foundation)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [App Javascript per Web browser](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [App Java e per dispositivi mobili Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [App Swift e per dispositivi mobili iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
