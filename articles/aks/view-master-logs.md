---
title: Visualizzare i log del controller del servizio Azure Kubernetes
description: Informazioni su come abilitare e visualizzare i log per il nodo master di Kubernetes nel servizio Azure Kubernetes
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/03/2019
ms.author: mlearned
ms.openlocfilehash: dc72a8d448a189918def35da0250d83c81da7fa0
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812823"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>Abilitare e controllare i log del nodo master di Kubernetes nel servizio Azure Kubernetes

Con il servizio Azure Kubernetes, i componenti master, ad esempio *kube-apiserver* e *kube-controller-manager* vengono forniti come servizio gestito. Si creano e si gestiscono i nodi che eseguono *kubelet* e il runtime del contenitore e si distribuiscono le applicazioni attraverso il server API Kubernetes gestito. Per facilitare la risoluzione dei problemi dell'applicazione e dei servizi, potrebbe essere necessario visualizzare i log generati da tali componenti master. Questo articolo illustra come usare i log di Monitoraggio di Azure per abilitare i log ed eseguire query dai componenti master di Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo richiede un cluster del servizio Azure Kubernetes esistente in esecuzione nel proprio account di Azure. Se non si dispone già di un cluster AKS, crearne uno usando l'interfaccia della riga di comando di [Azure][cli-quickstart] o [portale di Azure][portal-quickstart]. I log di Monitoraggio di Azure funzionano con cluster del servizio Azure Kubernetes abilitati per il Controllo degli accessi in base al ruolo e non abilitati.

## <a name="enable-diagnostics-logs"></a>Abilitare la registrazione diagnostica

Per raccogliere e rivedere i dati da più origini, i log di Monitoraggio di Azure forniscono un linguaggio di query e un motore di analisi che offrono informazioni dettagliate per l'ambiente in uso. Viene usata un'area di lavoro per collazionare e analizzare i dati che possa integrarsi con altri servizi di Azure, ad esempio Application Insights e Centro sicurezza. Per usare una piattaforma diversa per analizzare i log, è possibile scegliere di inviare i log di diagnostica a un account di archiviazione di Azure o a un hub eventi. Per altre informazioni, vedere informazioni sui [log di monitoraggio di Azure][log-analytics-overview].

I log di monitoraggio di Azure sono abilitati e gestiti nella portale di Azure. Per abilitare la raccolta dei log per i componenti master di Kubernetes nel cluster del servizio Azure Kubernetes, aprire il portale di Azure in un Web browser e completare i passaggi seguenti:

1. Selezionare il gruppo di risorse per il cluster servizio Azure Kubernetes, ad esempio *myResourceGroup*. Non selezionare il gruppo di risorse che contiene le singole risorse del cluster servizio Azure Kubernetes, ad esempio *MC_myResourceGroup_myservizio Azure KubernetesCluster_eastus*.
1. Sul lato sinistro, scegliere **Impostazioni di diagnostica**.
1. Selezionare il cluster AKS, ad esempio *myAKSCluster*, quindi scegliere di **aggiungere l'impostazione di diagnostica**.
1. Immettere un nome, ad esempio *myAKSClusterLogs*, quindi selezionare l'opzione **Invia a Log Analytics**.
1. Selezionare un'area di lavoro esistente o crearne una nuova. Se si crea un'area di lavoro, specificare un nome per l'area di lavoro, un gruppo di risorse e un percorso.
1. Nell'elenco dei log disponibili selezionare i log che si desidera abilitare. I log comuni includono *Kube-apiserver*, *Kube-Controller-Manager*e *Kube-Scheduler*. È possibile abilitare ulteriori log, ad esempio *kube-audit* e *cluster-autoscaler*. È possibile restituire e modificare i log raccolti dopo l'abilitazione delle aree di lavoro di Log Analytics.
1. Quando si è pronti, selezionare **Salva** per abilitare la raccolta dei log selezionati.

La schermata del portale di esempio seguente mostra la finestra *impostazioni di diagnostica* :

![Abilitare l'area di lavoro Log Analytics per i log di Monitoraggio di Azure del cluster del servizio Azure Kubernetes](media/view-master-logs/enable-oms-log-analytics.png)

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Pianificare un pod di test nel cluster servizio Azure Kubernetes

Per generare alcuni log, creare un nuovo pod nel cluster servizio Azure Kubernetes. Il seguente esempio di manifesto YAML può essere usato per creare un'istanza NGINX di base. Creare un file denominato `nginx.yaml` in un editor a scelta e incollare il contenuto seguente:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    ports:
    - containerPort: 80
```

Creare il pod con il comando [kubectl create][kubectl-create] e specificare il file YAML, come illustrato nell'esempio seguente:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Visualizzare i log raccolti

Potrebbero occorrere alcuni minuti prima che i log di diagnostica siano abilitati e vengano visualizzati nell'area di lavoro Log Analytics. Nella portale di Azure selezionare il gruppo di risorse per l'area di lavoro Log Analytics, ad esempio *myResourceGroup*, quindi scegliere la risorsa di log Analytics, ad esempio *myAKSLogs*.

![Selezionare l'area di lavoro Log Analytics per il cluster servizio Azure Kubernetes](media/view-master-logs/select-log-analytics-workspace.png)

Sul lato sinistro scegliere **Log**. Per visualizzare *kube-apiserver*, immettere la query seguente nella casella di testo:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| project log_s
```

Probabilmente vengono restituiti molti log per il server API. Per limitare i risultati della query in modo da visualizzare i log sul pod NGINX creato nel passaggio precedente, aggiungere un'altra istruzione *where* per cercare *pods/nginx* come illustrato nella query di esempio seguente:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| where log_s contains "pods/nginx"
| project log_s
```

Vengono visualizzati i log specifici per il pod NGINX, come illustrato nello screenshot di esempio seguente:

![Registrare i risultati della query di Log Analytics per il pod NGINX di esempio](media/view-master-logs/log-analytics-query-results.png)

Per visualizzare altri log, è possibile aggiornare la query per il nome di *Categoria* con *kube-controller-manager* oppure *kube-scheduler*, a seconda dei log aggiuntivi che si vuole abilitare. È possibile usare ulteriori istruzioni *where* per definire gli eventi da cercare.

Per altre informazioni su come eseguire una query e filtrare i dati di log, vedere [visualizzare o analizzare i dati raccolti con la ricerca log di log Analytics][analyze-log-analytics].

## <a name="log-event-schema"></a>Schema di eventi del log

Per semplificare l'analisi dei dati dei log, la tabella seguente illustra lo schema utilizzato per ogni evento:

| Nome campo               | DESCRIZIONE |
|--------------------------|-------------|
| *resourceId*             | Risorsa di Azure che ha generato il log |
| *time*                   | Timestamp di quando è stato caricato il log |
| *category*               | Nome del contenitore o componente che ha generato il log |
| *operationName*          | Sempre *Microsoft.ContainerService/managedClusters/diagnosticLogs/Read* |
| *properties.log*         | Query full-text del log dal componente |
| *properties.stream*      | *stderr* o *stdout* |
| *properties.pod*         | Nome del pod da cui proviene il log |
| *properties.containerID* | ID del contenitore Docker da cui proviene questo log |

## <a name="log-roles"></a>Ruoli di log

| Role                     | Descrizione |
|--------------------------|-------------|
| *aksService*             | Nome visualizzato nel log di controllo per l'operazione del piano di controllo (da hcpService) |
| *MasterClient*           | Nome visualizzato nel log di controllo per MasterClientCertificate, il certificato ottenuto da AZ AKS Get-credentials |
| *nodeclient*             | Nome visualizzato per ClientCertificate, utilizzato dai nodi di Agent |

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato descritto come abilitare e analizzare i log per i componenti master di Kubernetes nel cluster servizio Azure Kubernetes. Per eseguire il monitoraggio e la risoluzione dei problemi, è anche possibile [visualizzare i log Kubelet][kubelet-logs] e [abilitare l'accesso al nodo SSH][aks-ssh].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../log-analytics/log-analytics-overview.md
[analyze-log-analytics]: ../azure-monitor/learn/tutorial-viewdata.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
