---
title: Filtri di sicurezza per limitare i risultati - Ricerca di Azure
description: Controllo di accesso sul contenuto di Ricerca di Azure con i filtri di sicurezza e le identità dell'utente.
ms.service: search
ms.topic: conceptual
services: search
ms.date: 05/02/2019
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
ms.custom: seodec2018
ms.openlocfilehash: 4d1ffa5b29a56d32a4f6a8ccf40f5bafd27795e6
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186482"
---
# <a name="security-filters-for-trimming-results-in-azure-search"></a>Filtri di sicurezza per limitare i risultati in Ricerca di Azure

È possibile applicare i filtri di sicurezza per limitare i risultati della ricerca in Ricerca di Azure in base all'identità dell'utente. Per questa esperienza di ricerca in genere è necessario il confronto dell'identità dell'utente che richiede la ricerca con un campo contenente i principi che dispongono delle autorizzazioni per il documento. Quando viene trovata una corrispondenza, l'utente o l'entità di sicurezza, ad esempio un gruppo o ruolo, può accedere a un documento.

Un modo per applicare il filtro di sicurezza è tramite una disgiunzione complessa di espressioni di uguaglianza: ad esempio `Id eq 'id1' or Id eq 'id2'` e così via. Questo approccio è soggetto a errori, difficili da gestire e nei casi in cui l'elenco contenga centinaia o migliaia di valori, rallenta il tempo di risposta della query di molti secondi. 

L'uso della funzione `search.in` rappresenta un approccio più semplice e veloce. Se invece si usa `search.in(Id, 'id1, id2, ...')` al posto di un'espressione di uguaglianza, è possibile prevedere tempi di risposta in frazioni di secondo.

Questo articolo descrive come applicare un filtro di sicurezza usando la procedura seguente:
> [!div class="checklist"]
> * Creare un campo che contenga gli identificatori dell'entità di sicurezza 
> * Eseguire il push o aggiornare i documenti esistenti con gli identificatori di entità di sicurezza rilevanti
> * Emettere una richiesta di ricerca con `search.in` `filter`

>[!NOTE]
> Il processo di recupero degli identificatori dell'entità di sicurezza non viene trattato in questo documento. È possibile richiederlo dal provider di servizi di identità.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone la disponibilità di una [sottoscrizione di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), del [servizio Ricerca di Azure](https://docs.microsoft.com/azure/search/search-create-service-portal) e dell'[Indice di Ricerca di Azure](https://docs.microsoft.com/azure/search/search-create-index-portal).  

## <a name="create-security-field"></a>Creare il campo di sicurezza

I documenti devono includere un campo che specifica i gruppi che hanno accesso. Queste informazioni diventano i criteri di filtro in base ai quali i documenti vengono selezionati o rifiutati dal set di risultati restituito all'autorità emittente.
Si supponga di avere un indice dei file protetti e che ogni file sia accessibile da un diverso set di utenti.
1. Aggiungere il campo `group_ids`, è possibile scegliere un nome qualsiasi, come `Collection(Edm.String)`. Verificare che per il campo l'attributo `filterable` sia impostato su `true` in modo che i risultati della ricerca vengano filtrati in base all'accesso dell'utente. Ad esempio, se si imposta il campo `group_ids` su `["group_id1, group_id2"]` per il documento con `file_name` "secured_file_b", solo gli utenti che appartengono agli ID di gruppo "group_id1" o "group_id2" hanno l'accesso in lettura al file.
   Verificare che l'attributo `retrievable` del campo sia impostato su `false` in modo che non venga restituito come parte della richiesta di ricerca.
2. Aggiungere anche i campi `file_id` e `file_name` ai fini di questo esempio.  

```JSON
{
    "name": "securedfiles",  
    "fields": [
        {"name": "file_id", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "file_name", "type": "Edm.String"},
        {"name": "group_ids", "type": "Collection(Edm.String)", "filterable": true, "retrievable": false}
    ]
}
```

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Push dei dati nell'indice tramite l'API REST
  
Eseguire una richiesta HTTP POST all'endpoint dell'URL dell'indice. Il corpo della richiesta HTTP è un oggetto JSON che contiene i documenti da aggiungere:

```
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=2019-05-06  
Content-Type: application/json
api-key: [admin key]
```

Nel corpo della richiesta, specificare il contenuto dei documenti:

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "file_id": "1",
            "file_name": "secured_file_a",
            "group_ids": ["group_id1"]
        },
        {
            "@search.action": "upload",
            "file_id": "2",
            "file_name": "secured_file_b",
            "group_ids": ["group_id1", "group_id2"]
        },
        {
            "@search.action": "upload",
            "file_id": "3",
            "file_name": "secured_file_c",
            "group_ids": ["group_id5", "group_id6"]
        }
    ]
}
```

Se si desidera aggiornare un documento esistente con l'elenco di gruppi, è possibile usare l'azione `merge` o `mergeOrUpload`:

```JSON
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "file_id": "3",
            "group_ids": ["group_id7", "group_id8", "group_id9"]
        }
    ]
}
```

Per informazioni dettagliate sull'aggiunta o l'aggiornamento dei documenti, è possibile leggere [Modificare documenti](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).
   
## <a name="apply-the-security-filter"></a>Applicare il filtro di sicurezza

Al fine di tagliare i documenti in base all'accesso `group_ids`, è consigliabile eseguire una query di ricerca con un filtro `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))`, in cui "group_id1, group_id2,..." sono i gruppi a cui appartiene l'emittente della richiesta di ricerca.
Questo filtro corrisponde a tutti i documenti per cui il campo `group_ids` contiene uno degli identificatori specificati.
Per informazioni dettagliate sulla ricerca di documenti con Ricerca di Azure, è possibile leggere [Cercare documenti](https://docs.microsoft.com/rest/api/searchservice/search-documents).
Si noti che questo esempio mostra come eseguire la ricerca di documenti con una richiesta POST.

Inviare la richiesta HTTP POST:

```
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=2019-05-06
Content-Type: application/json  
api-key: [admin or query key]
```

Specificare il filtro nel corpo della richiesta:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

È necessario riottenere i documenti in cui `group_ids` contiene "group_id1" o "group_id2". In altre parole, è possibile ottenere i documenti a cui il richiedente ha accesso in lettura.

```JSON
{
 [
   {
    "@search.score":1.0,
     "file_id":"1",
     "file_name":"secured_file_a",
   },
   {
     "@search.score":1.0,
     "file_id":"2",
     "file_name":"secured_file_b"
   }
 ]
}
```
## <a name="conclusion"></a>Conclusione

Ecco come è possibile filtrare i risultati in base all'identità dell'utente e alla funzione `search.in()` di Ricerca di Azure. È possibile usare questa funzione per passare gli identificatori di principio che l'utente richiedente deve confrontare con gli identificatori di entità associati a ogni documento di destinazione. Quando viene gestita una richiesta di ricerca, la funzione `search.in` filtra i risultati della ricerca per cui l'accesso in lettura non è consentito ad alcuna entità di sicurezza dell'utente. Gli identificatori dell'entità di sicurezza possono rappresentare oggetti quali i gruppi di sicurezza, i ruoli o persino l'identità dell'utente.
 
## <a name="see-also"></a>Vedere anche

+ [Controllo degli accessi in base all'identità di Active Directory tramite i filtri di Ricerca di Azure](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtri in Ricerca di Azure](search-filters.md)
+ [Controllo di accesso e sicurezza dei dati nelle operazioni di Ricerca di Azure](search-security-overview.md)