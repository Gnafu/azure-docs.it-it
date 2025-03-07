---
title: Usare il modulo servizi-mappe di Azure | Microsoft Docs
description: Informazioni su come usare il modulo servizi di mappe di Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: 77659fdf93947cfbaa0daa322dccf4e9cb1a41fa
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844724"
---
# <a name="use-the-azure-maps-services-module"></a>Usare il modulo servizi di mappe di Azure

Azure Maps Web SDK fornisce un *modulo dei servizi*. Questo modulo è una libreria helper che semplifica l'uso dei servizi REST di Maps di Azure nelle applicazioni Web o node. js usando JavaScript o TypeScript.

## <a name="use-the-services-module-in-a-webpage"></a>Usare il modulo servizi in una pagina Web

1. Creare un nuovo file HTML.
1. Caricare il modulo servizi di mappe di Azure. È possibile caricarlo in uno dei due modi seguenti:
    - Usare la versione di rete per la distribuzione di contenuti di Azure ospitata a livello globale del modulo servizi di Azure maps. Aggiungere un riferimento allo script all' `<head>` elemento del file:

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
        ```

    - In alternativa, caricare il codice sorgente di Azure Maps Web SDK localmente usando il pacchetto NPM [Azure-Maps-REST](https://www.npmjs.com/package/azure-maps-rest) e quindi ospitarlo nell'app. Questo pacchetto include anche le definizioni TypeScript. Usare questo comando:
    
        > **NPM installare Azure-Maps-REST**
    
        Aggiungere quindi un riferimento allo script all' `<head>` elemento del file:

         ```html
        <script src="node_modules/azure-maps-rest/dist/js/atlas-service.min.js"></script>
         ```

1. Creare una pipeline di autenticazione. Prima di poter inizializzare un endpoint client dell'URL del servizio, è necessario creare la pipeline. Usare la chiave dell'account di Azure Maps personalizzata o le credenziali Azure Active Directory (Azure AD) per autenticare un client del servizio di ricerca di Azure maps. In questo esempio verrà creato il client dell'URL del servizio di ricerca. 

    Se si usa una chiave di sottoscrizione per l'autenticazione:

    ```javascript
    // Get an Azure Maps key at https://azure.com/maps.
    var subscriptionKey = '<Your Azure Maps Key>';

    // Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);

    // Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```

    Se si usa Azure AD per l'autenticazione:

    ```javascript
    // Enter your Azure AD client ID.
    var clientId = "<Your Azure Active Directory Client Id>";

    // Use TokenCredential with OAuth token (Azure AD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);

    // Create a repeating time-out that will renew the Azure AD token.
    // This time-out must be cleared when the TokenCredential object is no longer needed.
    // If the time-out is not cleared, the memory used by the TokenCredential will never be reclaimed.
    var renewToken = async () => {
      try {
        console.log("Renewing token");
        var token = await getAadToken();
        tokenCredential.token = token;
        tokenRenewalTimer = setTimeout(renewToken, getExpiration(token));
      } catch (error) {
        console.log("Caught error when renewing token");
        clearTimeout(tokenRenewalTimer);
        throw error;
      }
    }
    tokenRenewalTimer = setTimeout(renewToken, getExpiration(aadToken));

    // Use tokenCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
      // Use the signed-in auth context to get a token.
      return new Promise((resolve, reject) => {
        // The resource should always be https://atlas.microsoft.com/.
        const resource = "https://atlas.microsoft.com/";
        authContext.acquireToken(resource, (error, token) => {
          if (error) {
            reject(error);
          } else {
            resolve(token);
          }
        });
      })
    }

    function getExpiration(jwtToken) {
      // Decode the JSON Web Token (JWT) to get the expiration time stamp.
      const json = atob(jwtToken.split(".")[1]);
      const decode = JSON.parse(json);

      // Return the milliseconds remaining until the token must be renewed.
      // Reduce the time until renewal by 5 minutes to avoid using an expired token.
      // The exp property is the time stamp of the expiration, in seconds.
      const renewSkew = 300000;
      return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    Per altre informazioni, vedere [autenticazione con mappe di Azure](azure-maps-authentication.md).

1. Il codice seguente usa il client URL del servizio di ricerca di Azure appena creato per la geocodifica di un indirizzo: "1 Microsoft Way, Redmond, WA". Il codice usa la `searchAddress` funzione e Visualizza i risultati come tabella nel corpo della pagina.

    ```javascript
    // Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa')
      .then(response => {
        var html = [];

        // Display the total results.
        html.push('Total results: ', response.summary.numResults, '<br/><br/>');

        // Create a table of the results.
        html.push('<table><tr><td></td><td>Result</td><td>Latitude</td><td>Longitude</td></tr>');

        for(var i=0;i<response.results.length;i++){
          html.push('<tr><td>', (i+1), '.</td><td>', 
            response.results[i].address.freeformAddress, 
            '</td><td>', 
            response.results[i].position.lat,
            '</td><td>', 
            response.results[i].position.lon,
            '</td></tr>');
        }

        html.push('</table>');

        // Add the resulting HTML to the body of the page.
        document.body.innerHTML = html.join('');
    });
    ```

    Ecco l'esempio di codice completo in esecuzione:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Uso del modulo Services" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la penna <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>usando il modulo Servizi</a> di Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [MapsURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.mapsurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [SearchURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [RouteURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [TokenCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.tokencredential?view=azure-maps-typescript-latest)

Per altri esempi di codice che usano il modulo Services, vedere questi articoli:

> [!div class="nextstepaction"]
> [Mostra i risultati della ricerca sulla mappa](./map-search-location.md)

> [!div class="nextstepaction"]
> [Ottenere informazioni su una coordinata](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Visualizzare le indicazioni stradali da A a B](./map-route.md)