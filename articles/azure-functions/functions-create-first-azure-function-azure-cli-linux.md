---
title: Creare la prima funzione in Linux dall'interfaccia della riga di comando di Azure (anteprima) | Microsoft Docs
description: Informazioni su come creare la prima funzione di Azure eseguita su un'immagine predefinita di Linux tramite l'interfaccia della riga di comando di Azure.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/15/2017
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: 1cf20a4a93ef1b5bfb9c7818f35be5e75e45a3d2
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901091"
---
# <a name="create-your-first-function-running-on-linux-using-the-azure-cli-preview"></a>Creare la prima funzione eseguita in Linux usando l'interfaccia della riga di comando di Azure (anteprima)

Funzioni di Azure consente di ospitare le funzioni in Linux in un contenitore di Servizio app di Azure predefinito. È anche possibile [usare un contenitore personalizzato](functions-create-function-linux-custom-image.md). Questa funzionalità è attualmente disponibile in versione di anteprima e richiede il [runtime di Funzioni 2.0](functions-versions.md).

Questa guida introduttiva illustra in dettaglio come usare Funzioni di Azure con l'interfaccia della riga di comando di Azure per creare la prima app per le funzioni in Linux ospitata nel contenitore predefinito del servizio app. Il codice della funzione viene distribuito all'immagine da un repository GitHub di esempio.    

I passaggi seguenti sono supportati in computer Mac, Windows o Linux. 

## <a name="prerequisites"></a>Prerequisiti 

Per completare l'esercitazione introduttiva, sono necessari gli elementi seguenti:

+ Una sottoscrizione di Azure attiva.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessaria la versione 2.0.21 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione in uso. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Creare un nuovo piano di servizio app Linux

L'hosting delle funzioni in Linux è attualmente supportato solo per un piano di servizio app. L'hosting in un piano a consumo non è ancora supportato. Per altre informazioni sull'hosting, vedere [Confronto di piani di hosting per Funzioni di Azure](functions-scale.md). 

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-function-app-on-linux"></a>Creare un'app per le funzioni in Linux

Per ospitare l'esecuzione delle funzioni in Linux, è necessaria un'app per le funzioni. L'app per le funzioni offre un ambiente per l'esecuzione del codice delle funzioni. Consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione e la condivisione delle risorse. Creare un'app per le funzioni usando il comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create) con un piano di servizio app Linux. 

Nel comando seguente sostituire il segnaposto `<app_name>` con il nome univoco dell'app per le funzioni e il nome dell'account di archiviazione con `<storage_name>`. Dato che verrà usato come dominio DNS predefinito per l'app per le funzioni, è necessario che `<app_name>` sia univoco tra tutte le app in Azure. Il parametro _deployment-source-url_ è un repository di esempio in GitHub che contiene una funzione attivata da HTTP "Hello World".

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-source-url https://github.com/Azure-Samples/functions-quickstart-linux
```
Al termine della creazione e distribuzione dell'app per le funzioni, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

Dato che `myAppServicePlan` è un piano di Linux, viene usata l'immagine docker incorporata per creare il contenitore che esegue l'app per le funzioni in Linux. 

>[!NOTE]  
>Attualmente, il repository di esempio include due file di script, [deploy.sh](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/deploy.sh) e [.deployment](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/.deployment). Il file .deployment indica al processo di distribuzione di usare deploy.sh come [script di distribuzione personalizzato](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script). In questa versione di anteprima corrente gli script sono necessari per distribuire l'app per le funzioni in un'immagine di Linux.  

## <a name="configure-the-function-app"></a>Configurare l'app per le funzioni

Il progetto nel repository GitHub richiede la versione 1.x del runtime di Funzioni. Se si configura l'impostazione `FUNCTIONS_WORKER_RUNTIME` dell'applicazione su `~1`, l'app per le funzioni viene associata alla versione 1.x più recente. Configurare l'impostazione dell'applicazione con il comando [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set).

Nel comando seguente dell'interfaccia della riga di comando di Azure il valore "<app_name>" corrisponde al nome dell'app per le funzioni.

```azurecli-interactive
az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings FUNCTIONS_WORKER_RUNTIME=~1
```

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]
