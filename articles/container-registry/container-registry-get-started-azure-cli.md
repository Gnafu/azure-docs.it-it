---
title: Guida introduttiva - Creare un registro Docker privato in Azure con l'interfaccia della riga di comando di Azure
description: Informazioni su come apprendere rapidamente a creare un registro Docker privato con l'interfaccia della riga di comando di Azure.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: marsma
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 20dfae9c24bac7d9f76ae95f8b6d7c0610a0735e
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888542"
---
# <a name="quickstart-create-a-container-registry-using-the-azure-cli"></a>Avvio rapido: Creare un registro contenitori con l'interfaccia della riga di comando di Azure

Registro contenitori di Azure è un servizio gestito di registri contenitori Docker usato per l'archiviazione di immagini di un contenitore Docker privato. Questa guida descrive la creazione di un'istanza di Registro contenitori di Azure tramite l'interfaccia della riga di comando di Azure, l'inserimento di un'immagine del contenitore nel registro e infine la distribuzione del contenitore dal registro in Istanze di contenitore di Azure.

Questa guida introduttiva richiede l'interfaccia della riga di comando di Azure 2.0.27 o versioni successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

È anche necessario avere Docker installato localmente. Docker offre pacchetti che consentono di configurare facilmente Docker in qualsiasi sistema [Mac][docker-mac], [Windows][docker-windows] o [Linux][docker-linux].

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create][az-group-create]. Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Creare un registro di contenitori

In questa guida introduttiva viene creato un registro contenitori di *base*. Registro contenitori di Azure è disponibile in diversi SKU, descritti brevemente nella tabella riportata di seguito. Per altri dettagli su ogni SKU, vedere [SKU di Registro contenitori][container-registry-skus].

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Creare un'istanza di Registro contenitori di Azure usando il comando [az acr create][az-acr-create].

Il nome del registro deve essere univoco in Azure e contenere da 5 a 50 caratteri alfanumerici. Nell'esempio seguente viene usato il nome *myContainerRegistry007*. Aggiornarlo a un valore univoco.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
```

Quando viene creato il registro, l'output è simile al seguente:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-09-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "myContainerRegistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Nella parte restante di questa guida introduttiva, `<acrName>` è un segnaposto per il nome del registro contenitori.

## <a name="log-in-to-acr"></a>Accedere al record di controllo di accesso

Prima di eseguire il push e il pull delle immagini del contenitore, è necessario accedere all'istanza di Registro contenitori di Azure. A tale scopo usare il comando [az acr login][az-acr-login].

```azurecli
az acr login --name <acrName>
```

Il comando restituisce un messaggio `Login Succeeded` al termine dell'esecuzione.

## <a name="push-image-to-acr"></a>Eseguire il push di un'immagine nel record di controllo di accesso

Per eseguire il push di un'immagine nel registro contenitori di Azure è necessario innanzitutto disporre di un'immagine. Se non sono ancora disponibili immagini del contenitore locale, eseguire il comando seguente per eseguire il pull di un'immagine esistente da Hub Docker.

```bash
docker pull microsoft/aci-helloworld
```

Prima di poter eseguire il push di un'immagine nel registro, è necessario contrassegnarla con il nome completo del server di accesso del record di controllo di accesso. Eseguire il comando seguente per ottenere il nome completo del server di accesso dell'istanza del record di controllo di accesso.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Contrassegnare l'immagine usando il comando [docker tag][docker-tag]. Sostituire `<acrLoginServer>` con il nome del server di accesso dell'istanza del record di controllo di accesso.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Infine, usare [docker push][docker-push] per eseguire il push dell'immagine nell'istanza del record di controllo di accesso. Sostituire `<acrLoginServer>` con il nome del server di accesso dell'istanza del record di controllo di accesso.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>Elencare le immagini del contenitore

L'esempio seguente elenca i repository in un registro:

```azurecli
az acr repository list --name <acrName> --output table
```

Output:

```bash
Result
----------------
aci-helloworld
```

L'esempio seguente elenca i tag nel repository **aci-helloworld**.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-helloworld --output table
```

Output:

```bash
Result
--------
v1
```

## <a name="deploy-image-to-aci"></a>Distribuire l'immagine in Istanze di contenitore di Azure

Per distribuire un'istanza del contenitore dal registro creato, è necessario fornire le credenziali del registro al momento della distribuzione. Negli scenari di produzione è necessario usare un'[entità servizio][container-registry-auth-aci] per l'accesso al registro contenitori, ma per rendere sintetica questa guida introduttiva, abilitare l'utente amministratore nel registro con il comando seguente:

```azurecli
az acr update --name <acrName> --admin-enabled true
```

Dopo aver abilitato l'amministratore, il nome utente corrisponderà al nome del registro e sarà possibile recuperare la password con questo comando:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

Per distribuire l'immagine con 1 core CPU e 1 GB di memoria, eseguire questo comando. Sostituire `<acrName>`, `<acrLoginServer>` e `<acrPassword>` con i valori ottenuti dai comandi precedenti.

```azurecli
az container create --resource-group myResourceGroup --name acr-quickstart --image <acrLoginServer>/aci-helloworld:v1 --cpu 1 --memory 1 --registry-username <acrName> --registry-password <acrPassword> --dns-name-label aci-demo --ports 80
```

Si riceverà una risposta iniziale da Azure Resource Manager con i dettagli del contenitore. Per monitorare lo stato del contenitore e verificare quando è in esecuzione, ripetere il comando [az container show][az-container-show]. Per il completamento dovrebbe essere necessario meno di un minuto.

```azurecli
az container show --resource-group myResourceGroup --name acr-quickstart --query instanceView.state
```

## <a name="view-the-application"></a>Visualizzare l'applicazione

Dopo aver completato la distribuzione in Istanze di contenitore di Azure, recuperare il nome di dominio completo (FQDN) con il comando [az container show][az-container-show]:

```azurecli
az container show --resource-group myResourceGroup --name acr-quickstart --query ipAddress.fqdn
```

Output di esempio: `"aci-demo.eastus.azurecontainer.io"`

Per visualizzare l'applicazione in esecuzione, esplorare l'indirizzo IP pubblico nel browser preferito.

![App Hello World nel browser][aci-app-browser]

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse, l'istanza del record di controllo di accesso e tutte le immagini del contenitore non sono più necessari è possibile usare il comando [az group delete][az-group-delete] per rimuoverli.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato creato un registro contenitori di Azure con l'interfaccia della riga di comando di Azure, è stato eseguito il push di un'immagine del contenitore nel registro e ne è stata avviata un'istanza tramite Istanze di contenitore di Azure. Passare all'esercitazione su Istanze di contenitore di Azure per maggiori informazioni.

> [!div class="nextstepaction"]
> [Esercitazione su Istanze di contenitore di Azure][container-instances-tutorial-prepare-app]

<!-- IMAGES> -->
[aci-app-browser]: ../container-instances/media/container-instances-quickstart/aci-app-browser.png


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli]: /cli/azure/install-azure-cli
[az-container-show]: /cli/azure/container#az_container_show
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md
[container-registry-auth-aci]: container-registry-auth-aci.md