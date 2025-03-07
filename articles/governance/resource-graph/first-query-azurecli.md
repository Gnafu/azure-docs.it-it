---
title: Eseguire la prima query con l'interfaccia della riga di comando di Azure
description: Questo articolo illustra la procedura per abilitare l'estensione Resource Graph per l'interfaccia della riga di comando di Azure ed eseguire la prima query.
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/26/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 6fffd9e0bbaea47bfeac56a7972a38df0cbef351
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164700"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-cli"></a>Guida introduttiva: Eseguire la prima query di Resource Graph usando l'interfaccia della riga di comando di Azure

Il primo passaggio per usare Azure Resource Graph richiede di controllare che l'estensione per l'[interfaccia della riga di comando di Azure](/cli/azure/) sia installata. Questa guida introduttiva illustra il processo di aggiunta dell'estensione all'installazione dell'interfaccia della riga di comando di Azure. È possibile usare l'estensione con l'interfaccia della riga di comando di Azure installata in locale o tramite [Azure Cloud Shell](https://shell.azure.com).

Alla fine di questo processo l'estensione risulterà aggiunta all'installazione dell'interfaccia della riga di comando di Azure scelta e si eseguirà la prima query di Resource Graph.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="add-the-resource-graph-extension"></a>Aggiungere l'estensione Resource Graph

Per consentire all'interfaccia della riga di comando di Azure di eseguire query su Azure Resource Graph è necessario che l'estensione sia aggiunta. Questa estensione funziona ovunque sia possibile usare l'interfaccia della riga di comando di Azure, fra cui [bash in Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) sia autonomo che nel portale e l'[immagine Docker di Azure PowerShell](https://hub.docker.com/r/microsoft/azure-cli/), oppure installata in locale.

1. Controllare che sia installata l'interfaccia della riga di comando di Azure più recente, almeno la versione **2.0.45**. Se non è ancora installato, seguire [queste istruzioni](/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

1. Importarla nell'ambiente della riga di comando di Azure scelto usando il comando seguente:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Verificare che l'estensione sia stata installata e che la versione sia quella prevista (almeno **1.0.0**):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="run-your-first-resource-graph-query"></a>Eseguire la prima query di Resource Graph

Ora che l'estensione dell'interfaccia della riga di comando di Azure è stata aggiunta all'ambiente scelto, è possibile provare a eseguire una semplice query di Resource Graph. La query restituirà le prime cinque risorse di Azure con il **nome** e il **tipo di risorsa** di ogni risorsa.

1. Eseguire la prima query di Azure Resource Graph usando l'estensione `graph` e il comando `query`:

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Run Azure Resource Graph query
   az graph query -q 'project name, type | limit 5'
   ```

   > [!NOTE]
   > Poiché questo esempio di query non fornisce un modificatore di ordine, ad esempio `order by`, se si esegue questa query più volte, è probabile che venga restituito un set di risorse diverso per ogni richiesta.

1. Aggiornare la query applicando `order by` alla proprietà **Name**:

   ```azurecli-interactive
   # Run Azure Resource Graph query with 'order by'
   az graph query -q 'project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Come nella prima query, se si esegue questa query più volte, è probabile che venga restituito un set di risorse diverso per ogni richiesta. L'ordine dei comandi della query è importante. In questo esempio `order by` segue `limit`. In questo modo i risultati della query verranno prima limitati e poi ordinati.

1. Aggiornare la query applicando prima `order by` alla proprietà **Name** e quindi `limit` ai primi cinque risultati:

   ```azurecli-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   az graph query -q 'project name, type | order by name asc | limit 5'
   ```

Quando la query finale viene eseguita più volte, presupponendo che non vengano apportate modifiche all'ambiente, i risultati restituiti saranno coerenti e, come previsto, ordinati in base alla proprietà **Name**, ma ancora limitati ai primi cinque risultati.

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere l'estensione Resource Graph dall'ambiente dell'interfaccia della riga di comando di Azure, è possibile usare il comando seguente:

```azurecli-interactive
# Remove the Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>Passaggi successivi

- Ottenere altre informazioni sul [linguaggio di query](./concepts/query-language.md)
- Informazioni su come [esplorare le risorse](./concepts/explore-resources.md)
- Eseguire la prima query con [Azure PowerShell](first-query-powershell.md)
- Vedere esempi di [query di base](./samples/starter.md)
- Vedere esempi di [query avanzate](./samples/advanced.md)
- Commenti e suggerimenti su [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)