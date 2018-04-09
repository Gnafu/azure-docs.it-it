---
title: "Esercitazione su Kubernetes in Azure: ridimensionare un'applicazione"
description: "Esercitazione sul servizio contenitore di Azure: ridimensionare un'applicazione"
services: container-service
author: dlepow
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 5e35be84df53386398deaca19d60d4a93f53e541
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-scale-application-in-azure-container-service-aks"></a>Esercitazione: Ridimensionare un'applicazione nel servizio contenitore di Azure (AKS)

Se sono state eseguite le esercitazioni, si dispone di un cluster Kubernetes funzionante nel servizio contenitore di Azure ed è stata distribuita l'app Azure Voting.

In questa esercitazione, la quinta di otto, si aumenterà il numero di pod nell'app e si proverà la scalabilità automatica dei pod. Si apprenderà anche come ridimensionare il numero di nodi delle macchine virtuali di Azure per modificare la capacità del cluster per l'hosting dei carichi di lavoro. Le attività completate comprendono:

> [!div class="checklist"]
> * Scalabilità dei nodi di Azure in Kubernetes
> * Scalabilità manuale di pod Kubernetes
> * Configurazione della scalabilità automatica di pod che eseguono il front-end dell'app

Nelle esercitazioni successive l'applicazione Azure Vote viene aggiornata e Log Analytics viene configurato per monitorare il cluster Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

Nelle esercitazioni precedenti è stato creato un pacchetto di un'applicazione in un'immagine del contenitore, caricata poi nel Registro contenitori di Azure, ed è stato creato un cluster Kubernetes. L'applicazione è stata quindi eseguita nel cluster Kubernetes.

Se questi passaggi non sono stati ancora eseguiti e si vuole procedere, tornare a [Esercitazione 1: Creare immagini del contenitore][aks-tutorial-prepare-app].

## <a name="scale-aks-nodes"></a>Aumentare i nodi del servizio contenitore di Azure

Se è stato creato usando i comandi nell'esercitazione precedente, il cluster Kubernetes include un nodo. Se si prevede un numero maggiore o minore di carichi di lavoro dei contenitori nel cluster, è possibile modificare manualmente il numero di nodi.

Nell'esempio seguente il numero di nodi viene aumentato a tre nel cluster Kubernetes denominato *myAKSCluster*. Il completamento del comando richiede alcuni minuti.

```azurecli
az aks scale --resource-group=myResourceGroup --name=myAKSCluster --node-count 3
```

L'output è simile a:

```
"agentPoolProfiles": [
  {
    "count": 3,
    "dnsPrefix": null,
    "fqdn": null,
    "name": "myAKSCluster",
    "osDiskSizeGb": null,
    "osType": "Linux",
    "ports": null,
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_D2_v2",
    "vnetSubnetId": null
  }
```

## <a name="manually-scale-pods"></a>Scalare manualmente i pod

Fino a questo momento sono stati distribuiti il front-end di Azure Vote e l'istanza di Redis, ognuno con una replica singola. Per verificare, eseguire il comando [kubectl get][kubectl-get].

```azurecli
kubectl get pods
```

Output:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Modificare manualmente il numero di pod nella distribuzione `azure-vote-front` usando il comando [kubectl scale][kubectl-scale]. In questo esempio il numero aumenta a 5.

```azurecli
kubectl scale --replicas=5 deployment/azure-vote-front
```

Eseguire [kubectl get pods][kubectl-get] per verificare che Kubernetes stia creando i pod. Dopo circa un minuto i pod aggiuntivi sono in esecuzione:

```azurecli
kubectl get pods
```

Output:

```
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Scalare automaticamente i pod

Kubernetes supporta la [scalabilità automatica orizzontale dei pod][kubernetes-hpa] per modificare il numero dei pod in una distribuzione a seconda dell'utilizzo della CPU o delle altre metriche selezionate.

Per usare la scalabilità automatica, i pod devono avere richieste e limiti di CPU definiti. Nella distribuzione di `azure-vote-front` il contenitore front-end richiede un valore di 0,25 della CPU, con un limite pari a 0,5 della CPU. Le impostazioni sono simili:

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

L'esempio seguente usa il comando [kubectl autoscale][kubectl-autoscale] per ridimensionare automaticamente il numero di pod nella distribuzione `azure-vote-front`. In questo caso, se l'utilizzo della CPU supera il 50%, la scalabilità automatica aumenta il numero di pod fino al valore massimo di 10.


```azurecli
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Per visualizzare lo stato della scalabilità automatica, eseguire il comando seguente:

```azurecli
kubectl get hpa
```

Output:

```
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Dopo pochi minuti con un carico minimo sull'app Azure Vote, il numero di repliche di pod si riduce automaticamente a 3.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state usate diverse funzionalità di scalabilità nel cluster Kubernetes. Le attività descritte includevano:

> [!div class="checklist"]
> * Scalabilità manuale di pod Kubernetes
> * Configurazione della scalabilità automatica di pod che eseguono il front-end dell'app
> * Scalabilità dei nodi di Azure in Kubernetes

Passare all'esercitazione successiva per apprendere come aggiornare l'applicazione in Kubernetes.

> [!div class="nextstepaction"]
> [Aggiornare un'applicazione in Kubernetes][aks-tutorial-update-app]

<!-- LINKS - external -->
[kubectl-autoscale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-scale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale
[kubernetes-hpa]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-update-app]: ./tutorial-kubernetes-app-update.md
