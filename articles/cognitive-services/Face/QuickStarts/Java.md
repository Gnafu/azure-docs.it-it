---
title: "Guida introduttiva: Rilevare i visi in un'immagine con l'API REST di Azure e Java"
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si userà l'API REST Viso di Azure con Java per rilevare i visi in un'immagine.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 09/06/2019
ms.author: pafarley
ms.openlocfilehash: 30e4852668fc12c38cd7d1794c461041acd654db
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859205"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-java"></a>Guida introduttiva: Rilevare i visi in un'immagine con l'API REST e Java

In questa guida introduttiva si userà l'API REST Viso di Azure con Java per rilevare i visi umani in un'immagine.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="prerequisites"></a>Prerequisiti

- Una chiave di sottoscrizione API Viso. È possibile ottenere una chiave di sottoscrizione della versione di valutazione gratuita da [Prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=face-api). In alternativa, seguire le istruzioni in [Creare un account Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) per effettuare la sottoscrizione al servizio API Viso e ottenere la chiave.
- Qualsiasi ambiente IDE Java a scelta.

## <a name="create-the-java-project"></a>Creare il progetto Java

1. Creare una nuova app Java della riga di comando nell'IDE e aggiungere una classe **Main** con un metodo **main**.
1. Importare le librerie seguenti nel progetto Java. Se si usa Maven, verranno fornite le coordinate di Maven per ogni libreria.
   - [Client HTTP Apache](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.6)
   - [HTTP Apache core](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.10)
   - [Libreria JSON](https://github.com/stleary/JSON-java) (org.json:json:20180130)
   - [Registrazioni di Apache Commons](https://commons.apache.org/proper/commons-logging/download_logging.cgi) (commons-logging:commons-logging:1.1.2)

## <a name="add-face-detection-code"></a>Aggiungere codice di rilevamento volto

Aprire la classe main del progetto. In questo caso, si aggiungerà il codice necessario per caricare immagini e rilevare i visi.

### <a name="import-packages"></a>Importare pacchetti

Aggiungere le istruzioni `import` seguenti all'inizio del file.

```java
// This sample uses Apache HttpComponents:
// http://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/
// https://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/

import java.net.URI;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.HttpClientBuilder;
import org.apache.http.util.EntityUtils;
import org.json.JSONArray;
import org.json.JSONObject;
```

### <a name="add-essential-fields"></a>Aggiungere campi fondamentali

Sostituire la classe **Main** con il codice seguente. Questi dati consentono di specificare come connettersi al servizio Viso e dove ottenere i dati di input. Sarà necessario aggiornare il campo `subscriptionKey` con il valore della chiave di sottoscrizione e modificare la stringa `uriBase` in modo che contenga la stringa dell'endpoint corretta. È anche possibile impostare il valore `imageWithFaces` su un percorso che punti a un file di immagine diverso.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

Il campo `faceAttributes` è semplicemente un elenco di alcuni tipi di attributi, che specifica quali informazioni recuperare sui visi rilevati.

```Java
public class Main {
    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    private static final String uriBase =
        "https://<My Endpoint String>.com/face/v1.0/detect";

    private static final String imageWithFaces =
        "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}";

    private static final String faceAttributes =
        "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";
```

### <a name="call-the-face-detection-rest-api"></a>Chiamare l'API REST di rilevamento volto

Aggiungere il metodo **main** con il codice seguente. Il metodo costruisce una chiamata REST all'API Viso per rilevare informazioni sul viso nell'immagine remota (la stringa `faceAttributes` specifica quali attributi del viso recuperare), quindi scrive i dati di output in una stringa JSON.

```Java
    public static void main(String[] args) {
        HttpClient httpclient = HttpClientBuilder.create().build();

        try
        {
            URIBuilder builder = new URIBuilder(uriBase);

            // Request parameters. All of them are optional.
            builder.setParameter("returnFaceId", "true");
            builder.setParameter("returnFaceLandmarks", "false");
            builder.setParameter("returnFaceAttributes", faceAttributes);

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity reqEntity = new StringEntity(imageWithFaces);
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();
```

### <a name="parse-the-json-response"></a>Analizzare la risposta JSON.

Direttamente sotto il codice precedente, aggiungere il blocco seguente, che converte i dati JSON restituiti in un formato più facilmente leggibile prima della stampa sulla console. Infine, chiudere il blocco try-catch, il metodo **main** e la classe **Main**.

```Java
            if (entity != null)
            {
                // Format and display the JSON response.
                System.out.println("REST Response:\n");

                String jsonString = EntityUtils.toString(entity).trim();
                if (jsonString.charAt(0) == '[') {
                    JSONArray jsonArray = new JSONArray(jsonString);
                    System.out.println(jsonArray.toString(2));
                }
                else if (jsonString.charAt(0) == '{') {
                    JSONObject jsonObject = new JSONObject(jsonString);
                    System.out.println(jsonObject.toString(2));
                } else {
                    System.out.println(jsonString);
                }
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="run-the-app"></a>Esecuzione dell'app

Compilare ed eseguire il codice. Una risposta con esito positivo visualizzerà i dati sui volti in formato JSON facilmente leggibile nella finestra della console. Ad esempio:

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  },
  "faceAttributes": {
    "makeup": {
      "eyeMakeup": true,
      "lipMakeup": true
    },
    "facialHair": {
      "sideburns": 0,
      "beard": 0,
      "moustache": 0
    },
    "gender": "female",
    "accessories": [],
    "blur": {
      "blurLevel": "low",
      "value": 0.06
    },
    "headPose": {
      "roll": 0.1,
      "pitch": 0,
      "yaw": -32.9
    },
    "smile": 0,
    "glasses": "NoGlasses",
    "hair": {
      "bald": 0,
      "invisible": false,
      "hairColor": [
        {
          "color": "brown",
          "confidence": 1
        },
        {
          "color": "black",
          "confidence": 0.87
        },
        {
          "color": "other",
          "confidence": 0.51
        },
        {
          "color": "blond",
          "confidence": 0.08
        },
        {
          "color": "red",
          "confidence": 0.08
        },
        {
          "color": "gray",
          "confidence": 0.02
        }
      ]
    },
    "emotion": {
      "contempt": 0,
      "surprise": 0.005,
      "happiness": 0,
      "neutral": 0.986,
      "sadness": 0.009,
      "disgust": 0,
      "anger": 0,
      "fear": 0
    },
    "exposure": {
      "value": 0.67,
      "exposureLevel": "goodExposure"
    },
    "occlusion": {
      "eyeOccluded": false,
      "mouthOccluded": false,
      "foreheadOccluded": false
    },
    "noise": {
      "noiseLevel": "low",
      "value": 0
    },
    "age": 22.9
  },
  "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f"
}]
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata creata una semplice applicazione console Java che usa le chiamate REST con l'API Viso di Azure per rilevare i visi in un'immagine e restituire i relativi attributi. Successivamente, si apprenderà come eseguire altre operazioni con questa funzionalità in un'applicazione Android.

> [!div class="nextstepaction"]
> [Esercitazione: Creare un'app Android per rilevare e incorniciare i visi](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)
