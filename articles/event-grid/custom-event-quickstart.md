---
title: Eventi personalizzati per Griglia di eventi di Azure con l'interfaccia della riga di comando | Microsoft Docs
description: Usare Griglia di eventi di Azure e l'interfaccia della riga di comando di Azure per pubblicare un argomento e sottoscrivere l'evento.
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 03/20/2018
ms.topic: quickstart
ms.service: event-grid
ms.openlocfilehash: d68df064614c262bd9755be0688841fdb64af762
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301802"
---
# <a name="create-and-route-custom-events-with-azure-cli-and-event-grid"></a>Creare e instradare eventi personalizzati con l'interfaccia della riga di comando di Azure e Griglia di eventi

La griglia di eventi di Azure è un servizio di gestione degli eventi per il cloud. Questo articolo illustra come usare l'interfaccia della riga di comando di Azure per creare un argomento personalizzato, sottoscrivere l'argomento e attivare l'evento per visualizzare il risultato. In genere, si inviano eventi a un endpoint che risponde all'evento, ad esempio un webhook o una funzione di Azure. Per maggiore semplicità, tuttavia, in questo articolo gli eventi vengono inviati a un URL che si limita a raccoglie i messaggi. È possibile creare questo URL mediante uno strumento di terze parti da [Hookbin](https://hookbin.com/).

>[!NOTE]
>**Hookbin** non è destinato all'utilizzo con velocità effettiva elevata. Questo strumento viene usato esclusivamente per scopi dimostrativi. Se si esegue il push di più di un evento alla volta, è possibile che non vengano visualizzati tutti gli eventi nello strumento.

Al termine, i dati dell'evento vengono inviati a un endpoint.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire la versione più recente dell'interfaccia della riga di comando di Azure (2.0.24 o versione successiva). Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli).

Se non si usa Cloud Shell, prima è necessario accedere usando `az login`.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Gli argomenti della griglia di eventi sono risorse di Azure e devono essere inseriti in un gruppo di risorse di Azure. Un gruppo di risorse è una raccolta logica in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create). 

L'esempio seguente crea un gruppo di risorse denominato *gridResourceGroup* nella località *westus2*.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>Creare un argomento personalizzato

Un argomento di Griglia di eventi fornisce un endpoint definito dall'utente in cui vengono pubblicati gli eventi. L'esempio seguente crea l'argomento personalizzato nel gruppo di risorse. Sostituire `<topic_name>` con un nome univoco per l'argomento. Il nome dell'argomento deve essere univoco perché è rappresentato da una voce DNS.

```azurecli-interactive
az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="create-a-message-endpoint"></a>Creare un endpoint del messaggio

Prima di sottoscrivere l'argomento, creare l'endpoint per il messaggio dell'evento. Anziché scrivere codice per rispondere all'evento, creare un endpoint che raccoglie i messaggi per poterli visualizzare. Hookbin è uno strumento di terze parti che consente di creare un endpoint e visualizza le richieste che gli vengono inviate. Passare a [Hookbin](https://hookbin.com/) e fare clic su **Create New Endpoint** (Crea nuovo endpoint).  Copiare l'URL del contenitore, necessario per sottoscrivere l'argomento personalizzato.

## <a name="subscribe-to-a-topic"></a>Sottoscrivere un argomento

Si sottoscrive un argomento per indicare alla griglia di eventi gli eventi di cui si vuole tenere traccia. L'esempio seguente sottoscrive l'argomento creato e passa l'URL da Hookbin come endpoint per la notifica dell'evento. Sostituire `<event_subscription_name>` con un nome univoco per la sottoscrizione e `<endpoint_URL>` con il valore preso dalla sezione precedente. Se durante la sottoscrizione si specifica un endpoint, la griglia di eventi gestisce il routing degli eventi verso tale endpoint. Per `<topic_name>`, usare il valore creato in precedenza. 

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

## <a name="send-an-event-to-your-topic"></a>Inviare un evento all'argomento

Attivare un evento per vedere come la Griglia di eventi distribuisce il messaggio nell'endpoint. Ottenere prima di tutto l'URL e la chiave per l'argomento personalizzato. Usare ancora una volta il nome dell'argomento per `<topic_name>`.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

Per semplificare questo articolo, usare dati di esempio dell'evento da inviare all'argomento. In genere, i dati dell'evento vengono inviati da un'applicazione o un servizio di Azure. L'esempio seguente ottiene i dati dell'evento:

```azurecli-interactive
body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
```

Per visualizzare l'evento completo, usare `echo "$body"`. L'elemento `data` del JSON è il payload dell'evento. Questo campo accetta qualsiasi JSON ben formato. È anche possibile usare il campo oggetto per il filtro e il routing avanzato.

CURL è un'utilità che invia richieste HTTP. In questo articolo CURL viene usato per inviare l'evento all'argomento. 

```azurecli-interactive
curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

È stato attivato l'evento e Griglia di eventi ha inviato il messaggio all'endpoint configurato al momento della sottoscrizione. Passare all'URL dell'endpoint creato in precedenza. In alternativa, fare clic su Aggiorna nel browser aperto. Verrà visualizzato l'evento appena inviato. 

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/topics/{topic}"
}]
```

## <a name="clean-up-resources"></a>Pulire le risorse
Se si intende continuare a usare questo evento, non è necessario pulire le risorse create con questo articolo. In caso contrario, usare il comando seguente per eliminare le risorse create con questo articolo.

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come creare argomenti e sottoscrizioni di eventi, è possibile approfondire le operazioni possibili con la griglia di eventi:

- [Informazioni sulla griglia di eventi](overview.md)
- [Indirizzare gli eventi di archiviazione BLOB a un endpoint Web personalizzato (anteprima)](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitorare le modifiche alla macchina virtuale con la griglia di eventi di Azure e le app per la logica](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Trasmettere Big Data a un data warehouse](event-grid-event-hubs-integration.md)
