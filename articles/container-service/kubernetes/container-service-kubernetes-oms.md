---
title: (DEPRECATO) Monitorare il cluster Kubernetes di Azure - Operations Management
description: Monitoraggio di un cluster Kubernetes nel servizio Azure Container con Log Analytics
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: d7370fc14a5ede23744e04ac9d35140f2368e21f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60711787"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-log-analytics"></a>(DEPRECATO) Monitorare un cluster del servizio Azure Container con Log Analytics

> [!TIP]
> Per la versione aggiornata di questa esercitazione che usa il servizio Azure Kubernetes, vedere [Panoramica di monitoraggio di Azure per contenitori (anteprima)](../../azure-monitor/insights/container-insights-overview.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Prerequisiti
Si presume che questa procedura dettagliata abbia [creato un cluster Kubernetes mediante il servizio Azure Container](container-service-kubernetes-walkthrough.md).

Si presume anche che gli strumenti dell'interfaccia della riga di comando di Azure `az` e `kubectl` siano installati.

È possibile verificare se lo strumento `az` è installato eseguendo:

```console
$ az --version
```

Se lo strumento `az` non è installato, le istruzioni sono disponibili [qui](https://github.com/azure/azure-cli#installation).
In alternativa, è possibile usare [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), che presenta l'interfaccia della riga di comando `az` di Azure e gli strumenti `kubectl` già installati per l'utente.

È possibile verificare se lo strumento `kubectl` è installato eseguendo:

```console
$ kubectl version
```

Se `kubectl` non è installato, è possibile eseguire:
```console
$ az acs kubernetes install-cli
```

Per verificare la presenza di chiavi kubernetes installate nello strumento di kubectl è possibile eseguire:
```console
$ kubectl get nodes
```

Se il comando precedente ha presentato degli errori, è necessario installare le chiavi di cluster kubernetes nello strumento di kubectl desiderato. A tale scopo, eseguire il comando seguente:
```console
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>Monitoraggio dei contenitori con Log Analytics

Log Analytics è la soluzione Microsoft per la gestione IT basata sul cloud che consente di gestire e proteggere l'infrastruttura locale e cloud. La soluzione di monitoraggio dei contenitori è una soluzione di Log Analytics che consente di visualizzare l'inventario, le prestazioni e i log dei contenitori in un'unica posizione. È possibile controllare, risolvere i problemi relativi ai contenitori visualizzando i log in una posizione centralizzata e trovare un contenitore con un consumo eccessivo in un host.

![](media/container-service-monitoring-oms/image1.png)

Per altre informazioni sulla soluzione contenitore, consultare [Log Analytics della soluzione contenitore](../../azure-monitor/insights/containers.md).

## <a name="installing-log-analytics-on-kubernetes"></a>Installazione di Log Analytics in Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Ottenere l'ID e la chiave dell'area di lavoro
Per consentire la comunicazione dell'agente di Log Analytics con il servizio, è necessario configurare il rispettivo ID e la rispettiva chiave dell'area di lavoro. Per ottenere l'ID e la chiave dell'area di lavoro, è necessario creare un account in <https://mms.microsoft.com>.
Attenersi alla procedura di creazione di un account. Al termine della creazione dell'account, è possibile ottenere l'ID e la chiave facendo clic sul pannello **Log Analytics** e quindi sul nome dell'area di lavoro. In **Impostazioni avanzate**, **Origini connesse** e quindi **Server Linux** sono disponibili le informazioni necessarie, come illustrato di seguito.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-log-analytics-agent-using-a-daemonset"></a>Installare l'agente di Log Analytics usando un oggetto DaemonSet
Gli elementi DaemonSet vengono usati da Kubernetes per eseguire una singola istanza di un contenitore in ogni host del cluster.
Sono ideali per l'esecuzione di agenti di monitoraggio.

Ecco il file [YAML di DaemonSet](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Salvarlo in un file denominato `oms-daemonset.yaml` e sostituire i valori segnaposto per `WSID` e `KEY` con l'ID e la chiave dell'area di lavoro disponibili nel file.

Dopo avere aggiunto l'ID e la chiave dell'area di lavoro alla configurazione di DaemonSet, è possibile installare l'agente di Log Analytics nel cluster con lo strumento da riga di comando `kubectl`:

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-log-analytics-agent-using-a-kubernetes-secret"></a>Installazione dell'agente di Log Analytics tramite un segreto Kubernetes
Per proteggere l'ID e la chiave dell'area di lavoro Log Analytics è possibile usare il segreto Kubernetes come parte del file YAML di DaemonSet.

- Copiare lo script, il file modello dei segreti e il file YAML di DaemonSet (dal [repository](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)) e assicurarsi che siano nella stessa directory.
  - Script per la generazione di segreti: secret-gen.sh
  - Modello di segreto: secret-template.yaml
    - File DaemonSet YAML: omsagent-ds-secrets.yaml
- Eseguire lo script. Lo script richiederà l'ID e la chiave primaria dell'area di lavoro Log Analytics. Inserirli e lo script creerà un file YAML del segreto in modo da poterlo eseguire.
  ```
  #> sudo bash ./secret-gen.sh
  ```

  - Creare il pod dei segreti eseguendo l'istruzione seguente: ```kubectl create -f omsagentsecret.yaml```

  - Per verificare, eseguire quanto segue:

  ```
  root@ubuntu16-13db:~# kubectl get secrets
  NAME                  TYPE                                  DATA      AGE
  default-token-gvl91   kubernetes.io/service-account-token   3         50d
  omsagent-secret       Opaque                                2         1d
  root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
  Name:           omsagent-secret
  Namespace:      default
  Labels:         <none>
  Annotations:    <none>

  Type:   Opaque

  Data
  ====
  WSID:   36 bytes
  KEY:    88 bytes
  ```

  - Creare il DaemonSet dell'agente OMS eseguendo l'istruzione ```kubectl create -f omsagent-ds-secrets.yaml```

### <a name="conclusion"></a>Conclusioni
L'operazione è terminata. Dopo qualche minuto dovrebbe essere visualizzato il flusso di dati verso il dashboard di Log Analytics.
