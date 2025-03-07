---
title: Esplorare le API di Gemelli digitali di Azure | Microsoft Docs
description: Informazioni sui modelli comuni di query delle API di gestione di Gemelli digitali di Azure.
author: kingdomofends
manager: philmea
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: v-adgera
ms.openlocfilehash: 8472a86800d13cedd228ca881a7c095ff748350a
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172809"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Come usare le API di gestione di Gemelli digitali di Azure

Le API di gestione di Gemelli digitali di Azure forniscono funzionalità avanzate per le app IoT. Questo articolo illustra come esplorare la struttura di API.  

## <a name="api-summary"></a>Riepilogo dell'API

L'elenco seguente illustra i componenti delle API di Gemelli digitali.

* [/spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces): queste API interagiscono con le posizioni fisiche nella configurazione. Consentono di creare, eliminare e gestire i mapping digitali delle posizioni fisiche sotto forma di [grafo spaziale](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph).

* [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices): queste API interagiscono con i dispositivi nella configurazione. Questi dispositivi possono gestire uno o più sensori. Un dispositivo può ad esempio essere il telefono, un pod di sensori Raspberry Pi o un gateway Lora e così via.

* [/sensors](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors): queste API consentono di comunicare con i sensori associati ai dispositivi e alle posizioni fisiche. I sensori registrano e inviano i valori dell'ambiente che possono quindi essere usati per manipolare l'ambiente spaziale.  

* [/resources](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources): queste API consentono di configurare le risorse, ad esempio un hub IoT, per l'istanza di Gemelli digitali.

* [/types](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types): queste API consentono di associare i tipi estesi agli oggetti di Gemelli digitali, per aggiungere caratteristiche specifiche a tali oggetti. Questi tipi consentono di filtrare e raggruppare facilmente gli oggetti nell'interfaccia utente e nelle funzioni personalizzate che elaborano i dati di telemetria. Esempi di tipi estesi sono *DeviceType*, *SensorType*, *SensorDataType*, *SpaceType*, *SpaceSubType*, *SpaceBlobType*, *SpaceResourceType* e così via.

* [/ontologies](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies): queste API consentono di gestire le ontologie, che sono raccolte di tipi estesi. Ontologie forniscono i nomi per i tipi di oggetto in base allo spazio fisico che rappresentano. L'ontologia *BACnet*, ad esempio, fornisce nomi specifici per *sensor types*, *datatypes*, *datasubtypes* e *dataunittypes*. Le ontologie vengono gestite e create dal servizio. Gli utenti possono caricare e scaricare le ontologie. Quando un'ontologia viene caricata, tutti i nomi di tipo associati sono abilitati ed è possibile effettuarne il provisioning nel grafo spaziale. 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys): è possibile usare queste API per creare proprietà personalizzate per gli *spazi*, i *dispositivi*, gli *utenti* e i *sensori*. Queste proprietà vengono create come coppie chiave/valore. È possibile definire il tipo di dati per queste proprietà impostando *PrimitiveDataType*. È ad esempio possibile definire una proprietà denominata *BasicTemperatureDeltaProcessingRefreshTime* di tipo *uint* per i sensori e quindi assegnare un valore per questa proprietà per ognuno dei sensori. È anche possibile aggiungere vincoli per questi valori durante la creazione della proprietà, ad esempio intervalli *Min* e *Max*, oltre a valori consentiti come *ValidationData*.

* [/matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers): queste API consentono di specificare le condizioni che si vuole valutare dai dati in ingresso del dispositivo. Per altre informazioni, vedere [questo articolo](concepts-user-defined-functions.md#matchers). 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions): queste API consentono di creare, eliminare o aggiornare una funzione personalizzata che verrà eseguita quando si verificano le condizioni definite da *matchers*, per elaborare i dati provenienti dalla configurazione. Vedere [questo articolo](concepts-user-defined-functions.md#user-defined-functions) per altre informazioni su queste funzioni personalizzate, chiamate anche *funzioni definite dall'utente*. 

* [/endpoints](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints): queste API consentono di creare endpoint in modo che la soluzione Gemelli digitali possa comunicare con altri servizi di Azure per l'archiviazione e l'analisi dei dati. Per maggiori informazioni, leggere [questo articolo](concepts-events-routing.md). 

* [/keyStores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores): queste API consentono di gestire gli archivi di chiavi di sicurezza per gli spazi. Questi archivi possono contenere una raccolta di chiavi di sicurezza e consentono di recuperare facilmente le chiavi valide più recenti.

* [/users](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users): queste API consentono di associare gli utenti con gli spazi, per individuare queste persone quando è necessario. 

* [/system](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System): queste API consentono di gestire le impostazioni a livello di sistema, ad esempio i tipi predefiniti di spazi e sensori. 

* [/roleAssignments](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments): queste API consentono di associare i ruoli alle entità, ad esempio ID utente, ID funzione definita dall'utente e così via. Ogni assegnazione di ruolo include l'ID dell'entità da associare, il tipo di entità, l'ID del ruolo da associare, l'ID tenant e un percorso che definisce il limite superiore della risorsa a cui l'entità può accedere con tale associazione. Per maggiori informazioni, leggere [questo articolo](security-role-based-access-control.md).


## <a name="api-navigation"></a>Esplorazione delle API

Le API di Gemelli digitali supportano il filtro e lo spostamento in tutto il grafo spaziale con i parametri seguenti:

- **spaceId**: l'API filtrerà i risultati in base all'ID spazio specificato. Inoltre, il flag booleano **useParentSpace** è applicabile alle API [/spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces), a indicare che l'ID spazio specificato fa riferimento allo spazio padre invece che allo spazio corrente. 

- **minLevel** e **maxLevel**: gli spazi radice sono considerati di livello 1. Gli spazi con lo spazio padre al livello *n* sono al livello *n+1*. Con questi valori impostati, è possibile filtrare i risultati a livelli specifici. Questi valori sono inclusivi quando vengono impostati. Dispositivi, sensori e altri oggetti sono considerati dello stesso livello dello spazio più vicino. Per ottenere tutti gli oggetti a un determinato livello, impostare sia **minLevel** che **maxLevel** sullo stesso valore.

- **minRelative** e **maxRelative**: quando questi filtri vengono specificati, il livello corrispondente è relativo al livello dell'ID spazio specificato:
   - Il livello relativo *0* è lo stesso livello dell'ID spazio specificato.
   - Il livello relativo *1* rappresenta gli spazi allo stesso livello degli elementi figlio dell'ID spazio specificato. Il livello relativo *n* rappresenta gli spazi inferiori allo spazio specificato dai livelli *n*.
   - Il livello relativo *-1* rappresenta gli spazi allo stesso livello dello spazio padre dello spazio specificato.

- **traverse**: consente di attraversare in entrambe le direzioni da un ID spazio specificato, come specificato dai valori seguenti.
   - **Nessuna**: questo valore predefinito filtra l'ID spazio specificato.
   - **Down**: filtra in base all'ID spazio specificato e ai relativi discendenti. 
   - **Up**: filtra in base all'ID spazio specificato e ai relativi predecessori. 
   - **Span**: filtra una porzione orizzontale del grafo spaziale, allo stesso livello dell'ID spazio specificato. È necessario che **minRelative** o **maxRelative** sia impostato su true. 


### <a name="examples"></a>Esempi

L'elenco seguente illustra alcuni esempi di spostamento nelle API [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices). Si noti che il segnaposto `YOUR_MANAGEMENT_API_URL` si riferisce all'URI delle API di Gemelli digitali nel formato `https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/`, dove `YOUR_INSTANCE_NAME` è il nome dell'istanza di Gemelli digitali di Azure e `YOUR_LOCATION` è l'area in cui è ospitata l'istanza.

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1` restituisce tutti i dispositivi collegati a spazi radice.
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4` restituisce tutti i dispositivi collegati a spazi di livello 2, 3 o 4.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId` restituisce tutti i dispositivi direttamente collegati a mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down` restituisce tutti i dispositivi collegati a mySpaceId o a uno dei discendenti.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true` restituisce tutti i dispositivi collegati ai discendenti di mySpaceId, escluso mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true` restituisce tutti i dispositivi collegati ai figli immediati di mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true` restituisce tutti i dispositivi collegati a uno dei predecessori di mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5` restituisce tutti i dispositivi collegati ai discendenti di mySpaceId a un livello pari o inferiore a 5.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true` restituisce tutti i dispositivi collegati agli spazi allo stesso livello di mySpaceId.


## <a name="odata-support"></a>Supporto OData

La maggior parte delle API che restituiscono raccolte, ad esempio una chiamata GET su /spaces, supporta il subset seguente delle opzioni di query di sistema di [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) generiche:  

* **$filter**
* **$orderby** 
* **$top**
* **$skip**: se si vuole visualizzare l'intera raccolta, è consigliabile richiederla come un unico set in una singola chiamata e quindi eseguire il paging nell'applicazione. 

> [!NOTE]
> Alcune opzioni OData, ad esempio le opzioni di query **$count**, **$expand**e **$Search**, non sono attualmente supportate.

### <a name="examples"></a>Esempi

Nell'elenco seguente vengono illustrate diverse query con sintassi OData valida:

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,’space’)`
- `YOUR_MANAGEMENT_API_URL/propertykeys?$filter=Scope ne ‘Spaces’`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=Size gt ‘M’`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,’k’)&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor’,Name)`
 
## <a name="next-steps"></a>Passaggi successivi

Per informazioni su alcuni modelli di query sulle API comuni, vedere [Come eseguire una query delle API di Gemelli digitali di Azure per trovare le attività comuni](./how-to-query-common-apis.md).

Per altre informazioni sugli endpoint dell'API, vedere l'articolo su [come usare i gemelli digitali spavalderia](./how-to-use-swagger.md).

Per esaminare la sintassi OData e gli operatori di confronto disponibili, vedere [operatori di confronto OData in ricerca di Azure](../search/search-query-odata-comparison-operators.md).
