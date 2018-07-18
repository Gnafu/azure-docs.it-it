---
title: Come cercare un indirizzo usando il servizio di ricerca di Mappe di Azure | Microsoft Docs
description: Informazioni su come cercare un indirizzo usando il servizio di ricerca di Mappe di Azure
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 1acb95af7b62641c371627d6250067f9c2eac99c
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2018
ms.locfileid: "36323636"
---
# <a name="how-to-find-an-address-using-the-azure-maps-search-service"></a>Come trovare un indirizzo usando il servizio di ricerca di Mappe di Azure

Il servizio di ricerca di Mappe è un set RESTful di API progettato per gli sviluppatori che consente di cercare indirizzi, località, punti di interesse, elenchi di aziende e altre informazioni geografiche. Il servizio assegna una coppia latitudine/longitudine a un indirizzo, una strada secondaria, una caratteristica geografica o un punto di interesse specifico. I valori di latitudine e longitudine restituiti dalla ricerca possono essere usati come parametri in altri servizi di Mappe come la pianificazione dell'itinerario e il flusso del traffico.

## <a name="prerequisites"></a>prerequisiti

Per eseguire chiamate alle API del servizio Mappe sono necessari un account di Mappe e una chiave. Per informazioni sulla creazione di un account e il recupero di una chiave, vedere [Come gestire l'account e le chiavi dell'account di Mappe di Azure](how-to-manage-account-keys.md).

Questo articolo usa l'[app Postman](https://www.getpostman.com/apps) per compilare le chiamate REST. È possibile usare qualsiasi ambiente di sviluppo API preferito. 


## <a name="using-fuzzy-search"></a>Uso della ricerca fuzzy

L'API predefinita per il servizio di ricerca è quella per la [ricerca fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy), che gestisce gli input di qualsiasi combinazione di token di indirizzo o punto di interesse. Questa API di ricerca corrisponde alla tradizionale "ricerca a riga singola" ed è utile quando non è noto l'input dell'utente come query di ricerca. L'API di ricerca fuzzy è una combinazione di ricerca di punti di interesse e geocodifica. L'API può anche essere ponderata con una posizione contestuale (coppia latitudine/longitudine), vincolata completamente da una coordinata e un raggio oppure può essere eseguita più genericamente senza alcun punto di ancoraggio di compensazione geografica.

La maggior parte delle query di ricerca restituisce per impostazione predefinita "maxFuzzyLevel=1" per favorire le prestazioni e ridurre i risultati insoliti. Questa impostazione predefinita può essere sostituita nel modo necessario passando il parametro di query "maxFuzzyLevel=2" o "3".

### <a name="search-for-an-address-using-fuzzy-search"></a>Cercare un indirizzo usando la ricerca fuzzy

1. Aprire l'app Postman, fare clic su New (Nuovo) | Create New (Crea nuovo) e selezionare **GET request** (Richiesta GET). Immettere il nome di richiesta **Fuzzy search** (Ricerca fuzzy), selezionare una raccolta o una cartella in cui salvarla e quindi fare clic su **Save** (Salva).

2. Nella scheda Builder (Generatore) selezionare il metodo HTTP **GET** e immettere l'URI della richiesta per l'endpoint API.

    ![Ricerca fuzzy ](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parametro | Valore consigliato |
    |---------------|------------------------------------------------|
    | Metodo HTTP | GET |
    | URL richiesta | https://atlas.microsoft.com/search/fuzzy/json? |
    | Authorization | No Auth (Senza autenticazione) |

    L'attributo **json** nel percorso URL determina il formato della risposta. In questo articolo viene usato json per motivi di semplicità d'uso e leggibilità. I formati di risposta disponibili sono inclusi nella definizione **Get Search Fuzzy** (Richiesta GET per la ricerca fuzzy) in [Maps Functional API reference] (Informazioni di riferimento sull'API funzionale di Mappe) https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Fare clic su **Params** (Parametri) e immettere le coppie chiave/valore seguenti da usare come parametri di query o percorso nell'URL della richiesta:

    ![Ricerca fuzzy ](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Chiave | Valore |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<la chiave di Mappe di Azure\> |
    | query | pizza |

4. Fare clic su **Send** (Invia) e quindi esaminare il corpo della risposta. 

    La stringa di query ambigua "pizza" ha restituito 10 risultati di punti di interesse con categorie legate a "pizza" e "ristorante". Ogni risultato restituisce un indirizzo, valori di latitudine/longitudine, viewport e punti di ingresso per la località.
    
    I risultati sono diversi per questa query e non sono associati ad alcuna località di riferimento. È possibile usare il parametro **countrySet** per specificare solo i paesi per cui l'applicazione richiede copertura, in quanto il comportamento predefinito consiste nel cercare nel mondo intero, con il rischio di restituire risultati non necessari.

5. Aggiungere la coppia chiave/valore seguente alla sezione **Parametri** e fare clic su **Invia**:

    | Chiave | Valore |
    |------------------|-------------------------|
    | countrySet | Stati Uniti |
    
    I risultati sono ora limitati dall'indicativo paese e la query restituisce le pizzerie negli Stati Uniti.
    
    Per fornire risultati orientati in una località specifica, è possibile eseguire una query per un punto di interesse e usare i valori di latitudine e longitudine restituiti nella chiamata al servizio di ricerca fuzzy. In questo caso, è stato usato il servizio di ricerca per restituire la località dello Space Needle di Seattle, usando i valori di latitudine /longitudine per orientare la ricerca.
    
4. In Params (Parametri) immettere le coppie chiave/valore seguenti e fare clic su **Send** (Invia):

    ![Ricerca fuzzy ](./media/how-to-search-for-address/fuzzy_search_latlon.png)
    
    | Chiave | Valore |
    |-----|------------|
    | lat | 47.62039 |
    | lon | -122.34928 |

## <a name="search-for-address-properties-and-coordinates"></a>Cercare proprietà e coordinate di indirizzi 

È possibile passare un indirizzo completo o parziale all'API di ricerca di indirizzi e ricevere una risposta che include proprietà dell'indirizzo dettagliate come il comune o il dipartimento, nonché i valori di posizione in latitudine e longitudine.

1. In Postman fare clic su **New Request** (Nuova richiesta)  | **GET request** (Richiesta GET) e quindi denominare la richiesta **Address Search** (Ricerca indirizzi).
2. Nella scheda Builder (Generatore) selezionare il metodo HTTP **GET**, immettere l'URL della richiesta per l'endpoint API e selezionare il protocollo di autorizzazione, se necessario.

    ![Ricerca di indirizzi ](./media/how-to-search-for-address/address_search_url.png)
    
    | Parametro | Valore consigliato |
    |---------------|------------------------------------------------|
    | Metodo HTTP | GET |
    | URL richiesta | https://atlas.microsoft.com/search/address/json? |
    | Authorization | No Auth (Senza autenticazione) |

2. Fare clic su **Params** (Parametri) e immettere le coppie chiave/valore seguenti da usare come parametri di query o percorso nell'URL della richiesta:
    
    ![Ricerca di indirizzi ](./media/how-to-search-for-address/address_search_params.png)
    
    | Chiave | Valore |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<la chiave di Mappe di Azure\> |
    | query | 400 Broad St, Seattle, WA 98109 |
    
3. Fare clic su **Send** (Invia) e quindi esaminare il corpo della risposta. 
    
    In questo caso, è stata specificata una query di indirizzo completo ed è stato ricevuto un solo risultato nel corpo della risposta. 
    
4. In Params (Parametri) modificare la stringa di query nel valore seguente:
    ```
        400 Broad, Seattle
    ```

5. Aggiungere la coppia chiave/valore seguente alla sezione **Parametri** e fare clic su **Invia**:

    | Chiave | Valore |
    |-----|------------|
    | typeahead | true |

    Il flag **typeahead** indica all'API di ricerca di indirizzi di considerare la query un input parziale e di restituire una matrice di valori predittivi.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Cercare un indirizzo tramite la ricerca di indirizzi inversa
1. In Postman fare clic su **New Request** (Nuova richiesta)  | **GET request** (Richiesta GET) e quindi denominare la richiesta **Reverse Address Search** (Ricerca indirizzi inversa).

2. Nella scheda Builder (Generatore) selezionare il metodo HTTP **GET** e immettere l'URI della richiesta per l'endpoint API.
    
    ![URL di ricerca di indirizzi inversa ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | Parametro | Valore consigliato |
    |---------------|------------------------------------------------|
    | Metodo HTTP | GET |
    | URL richiesta | https://atlas.microsoft.com/search/address/reverse/json? |
    | Authorization | No Auth (Senza autenticazione) |
    
2. Fare clic su **Params** (Parametri) e immettere le coppie chiave/valore seguenti da usare come parametri di query o percorso nell'URL della richiesta:
    
    ![Parametri di ricerca di indirizzi inversa ](./media/how-to-search-for-address/reverse_address_search_params.png)
    
    | Chiave | Valore |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<la chiave di Mappe di Azure\> |
    | query | 47.59093,-122.33263 |
    
3. Fare clic su **Send** (Invia) e quindi esaminare il corpo della risposta. 
    
    La risposta include la voce del punto di interesse per Safeco Field con una categoria di punto di interesse "stadio". 
    
4. Aggiungere la coppia chiave/valore seguente alla sezione **Parametri** e fare clic su **Invia**:

    | Chiave | Valore |
    |-----|------------|
    | number | true |

    Se con la richiesta viene inviato il parametro di query [number](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters), la risposta può includere il lato della via (sinistro/destro) e anche una posizione di variazione per il numero.
    
5. Aggiungere la coppia chiave/valore seguente alla sezione **Parametri** e fare clic su **Invia**:

    | Chiave | Valore |
    |-----|------------|
    | spatialKeys | true |

    Quando è impostato il parametro di query [spatialKeys](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters), la risposta contiene informazioni proprietarie relative alla chiave geospaziale per la località specificata.

6. Aggiungere la coppia chiave/valore seguente alla sezione **Parametri** e fare clic su **Invia**:

    | Chiave | Valore |
    |-----|------------|
    | returnSpeedLimit | true |
    
    Quando è impostato il parametro di query [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters), la risposta restituisce il limite di velocità pubblicato.

7. Aggiungere la coppia chiave/valore seguente alla sezione **Parametri** e fare clic su **Invia**:

    | Chiave | Valore |
    |-----|------------|
    | returnRoadUse | true |

    Quando è impostato il parametro di query [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters), la risposta restituisce la matrice degli usi stradali per codifiche geografiche inverse a livello di via.

8. Aggiungere la coppia chiave/valore seguente alla sezione **Parametri** e fare clic su **Invia**:

    | Chiave | Valore |
    |-----|------------|
    | roadUse | true |

    È possibile limitare la query di codifica geografica inversa a un tipo specifico di uso stradale usando il parametro di query [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters).
    
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>Cercare una strada secondaria usando la ricerca di strade secondarie inversa

1. In Postman fare clic su **New Request** (Nuova richiesta)  | **GET request** (Richiesta GET) e quindi denominare la richiesta **Reverse Address Cross Street Search** (Ricerca strade secondarie inversa).

2. Nella scheda Builder (Generatore) selezionare il metodo HTTP **GET** e immettere l'URI della richiesta per l'endpoint API.
    
    ![Ricerca di strade secondarie inversa ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | Parametro | Valore consigliato |
    |---------------|------------------------------------------------|
    | Metodo HTTP | GET |
    | URL richiesta | https://atlas.microsoft.com/search/address/reverse/crossstreet/json? |
    | Authorization | No Auth (Senza autenticazione) |
    
3. Fare clic su **Params** (Parametri) e immettere le coppie chiave/valore seguenti da usare come parametri di query o percorso nell'URL della richiesta:
    
    | Chiave | Valore |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<la chiave di Mappe di Azure\> |
    | query | 47.59093,-122.33263 |
    
4. Fare clic su **Send** (Invia) e quindi esaminare il corpo della risposta. 

## <a name="next-steps"></a>Passaggi successivi
- Esplorare la documentazione dell'API del [servizio di ricerca di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/search) 
