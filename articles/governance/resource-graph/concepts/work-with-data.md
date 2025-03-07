---
title: Usare set di dati di grandi dimensioni
description: Informazioni su come ottenere e controllare set di dati di grandi dimensioni durante l'utilizzo di Azure Resource Graph.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/01/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: d04f46dbc60a7242e44d76915e15281cc6248d20
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786541"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Utilizzo di set di dati della risorsa di Azure di grandi dimensioni

Azure Resource Graph è progettato per elaborare e ottenere informazioni sulle risorse nell'ambiente di Azure. Resource Graph rende più rapido il recupero di questi dati, anche in caso di query su migliaia di record. Resource Graph offre diverse opzioni per l'utilizzo di questi set di dati di grandi dimensioni.

Per indicazioni sull'utilizzo di query a una frequenza elevata, vedere [linee guida per le richieste limitate](./guidance-for-throttled-requests.md).

## <a name="data-set-result-size"></a>Dimensioni dei risultati dei set di dati

Per impostazione predefinita, Resource Graph limita a **100** record i risultati che possono essere restituiti da ogni query. Questa impostazione protegge sia l'utente che il servizio da query non intenzionali che restituirebbero set di dati di grandi dimensioni. Questo evento si verifica per lo più mentre l'utente fa diversi esperimenti con le query per trovare e filtrare le risorse nel modo più adeguato alle sue esigenze. Questa impostazione è diversa dall'uso degli operatori di linguaggio [top](/azure/kusto/query/topoperator) o [limit](/azure/kusto/query/limitoperator) di Esplora dati di Azure per limitare i risultati.

> [!NOTE]
> Quando si usa **primo**, si consiglia di ordinare per almeno una colonna con i risultati `asc` o `desc`. Senza l'ordinamento, i risultati restituiti sono casuali e non ripetibile.

È possibile eseguire l'override del limite predefinito tramite tutti i metodi di interazione con Resource Graph. Gli esempi seguenti mostrano come impostare il limite di dimensioni dei set di dati su _200_:

```azurecli-interactive
az graph query -q "project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -First 200
```

Nell'[API REST](/rest/api/azureresourcegraph/resources/resources) il controllo è **$top** e fa parte di **QueryRequestOptions**.

Ha la priorità il controllo _più restrittivo_. Ad esempio, se la query usa l'operatore **top** o **limit** e la sua esecuzione restituirebbe più record rispetto a **First**, il numero massimo di record restituiti sarebbe uguale a **First**. Analogamente, se il valore di **top** o **limit** è inferiore a **First**, il set di record restituito sarebbe il valore più piccolo configurato da **top** o **limit**.

Attualmente il valore massimo consentito per **First** è _5000_.

## <a name="skipping-records"></a>Ignorare record

Un'altra opzione per l'elaborazione di set di dati di grandi dimensioni è il controllo **Skip**. Questo controllo consente alla query di saltare o ignorare il numero definito di record prima di restituire i risultati. **Skip** è utile per le query che ordinano i risultati in un modo significativo allo scopo di ottenere i record situati in una posizione centrale del set di risultati. Se i risultati desiderati sono alla fine del set di dati restituito, è più efficiente usare una configurazione di ordinamento diversa e recuperare i risultati dall'inizio del set di dati.

> [!NOTE]
> Quando si usa **Skip**, si consiglia di ordinare per almeno una colonna con i risultati `asc` o `desc`. Senza l'ordinamento, i risultati restituiti sono casuali e non ripetibile.

Gli esempi seguenti mostrano come ignorare i primi _10_ record restituiti da una query facendo iniziare il set di risultati restituito dall'undicesimo record:

```azurecli-interactive
az graph query -q "project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -Skip 10
```

Nell'[API REST](/rest/api/azureresourcegraph/resources/resources) il controllo è **$skip** e fa parte di **QueryRequestOptions**.

## <a name="paging-results"></a>Risultati di paging

Quando è necessario suddividere un set di risultati in set più piccoli di record per l'elaborazione o perché un set di risultati comporterebbe il superamento del valore del massimo consentito _1000_ restituiti record, usare il paging. L'[API REST](/rest/api/azureresourcegraph/resources/resources) **QueryResponse** fornisce i valori **resultTruncated** e **$skipToken** per indicare se un set di risultati è stato suddiviso.
**resultTruncated** è un valore booleano che informa il consumer se sono presenti record aggiuntivi non restituiti nella risposta. Questa condizione può essere identificata anche quando il valore della proprietà **count** è inferiore a quello della proprietà **totalRecords**. **totalRecords** definisce il numero di record che soddisfano la query.

Quando **resultTruncated** è **true**, la proprietà **$skipToken** è impostata nella risposta. Questo valore viene usato con gli stessi valori di query e sottoscrizione per ottenere il set di record successivo che soddisfa la query.

Gli esempi seguenti illustrano come **ignorare** il primo record 3000 e restituire il **primo** 1000 record dopo quelli ignorata con CLI di Azure e Azure PowerShell:

```azurecli-interactive
az graph query -q "project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> Affinché la paginazione funzioni, la query deve **proiettare** il campo **ID**. Se è manca dalla query, la risposta non includerà il **$skipToken**.

Per un esempio, vedere [Next page query](/rest/api/azureresourcegraph/resources/resources#next-page-query) (Query della pagina successiva) nella documentazione dell'API REST.

## <a name="next-steps"></a>Passaggi successivi

- Vedere il linguaggio in uso nel [query Starter](../samples/starter.md).
- Vedere avanzata utilizza [query avanzate](../samples/advanced.md).
- Informazioni su come [esplorare risorse](explore-resources.md).