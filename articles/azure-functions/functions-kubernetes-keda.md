---
title: Funzioni di Azure in Kubernetes con KEDA
description: Informazioni su come eseguire funzioni di Azure in Kubernetes nel cloud o in locale usando KEDA, la scalabilità automatica basata su eventi basata su Kubernetes.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: funzioni di Azure, funzioni, elaborazione di eventi, calcolo dinamico, architettura senza server, kubernetes
ms.service: azure-functions
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: jehollan
ms.openlocfilehash: b581d7c9b5876813e36ebbf41be713b44dd97735
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70096103"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Funzioni di Azure in Kubernetes con KEDA

Il runtime di funzioni di Azure offre la flessibilità necessaria per ospitare il percorso e il modo desiderato.  [Keda](https://github.com/kedacore/kore) (Scalabilità automatica basata su eventi basata su Kubernetes), in modo uniforme con il runtime di funzioni di Azure e gli strumenti per fornire scalabilità basata su eventi in Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Funzionamento delle funzioni basate su Kubernetes

Il servizio funzioni di Azure è costituito da due componenti principali: un runtime e un controller di scalabilità.  Il runtime di funzioni viene eseguito ed esegue il codice.  Il runtime include la logica su come attivare, registrare e gestire le esecuzioni di funzioni.  L'altro componente è un controller di scalabilità.  Il controller di scalabilità monitora la frequenza degli eventi destinati alla funzione e ridimensiona in modo proattivo il numero di istanze che eseguono l'app.  Per altre informazioni, vedere [Ridimensionamento e hosting di Funzioni di Azure](functions-scale.md).

Le funzioni basate su Kubernetes forniscono il runtime di funzioni in un [contenitore Docker](functions-create-function-linux-custom-image.md) con scalabilità guidata dagli eventi tramite Keda.  KEDA può ridurre a 0 istanze (quando non si verificano eventi) e fino a *n* istanze. Questa operazione viene eseguita esponendo metriche personalizzate per il ridimensionamento automatico Kubernetes (Horizontal Pod AutoScaler).  L'uso di contenitori di funzioni con KEDA consente di replicare le funzionalità della funzione senza server in qualsiasi cluster Kubernetes.  Queste funzioni possono anche essere distribuite usando la funzionalità [nodi virtuali di Azure Kubernetes Services (AKS)](../aks/virtual-nodes-cli.md) per l'infrastruttura senza server.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Gestione di KEDA e funzioni in Kubernetes

Per eseguire funzioni nel cluster Kubernetes, è necessario installare il componente KEDA. È possibile installare questo componente usando [Azure Functions Core Tools](functions-run-local.md).

### <a name="installing-with-the-azure-functions-core-tools"></a>Installazione con la Azure Functions Core Tools

Per impostazione predefinita, gli strumenti di base installano entrambi i componenti KEDA e Osiris, che supportano rispettivamente la scalabilità basata su eventi e HTTP.  L'installazione usa `kubectl` l'esecuzione nel contesto corrente.

Installare KEDA nel cluster eseguendo il comando di installazione seguente:

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>Distribuzione di un'app per le funzioni in Kubernetes

È possibile distribuire qualsiasi app per le funzioni in un cluster Kubernetes che esegue KEDA.  Poiché le funzioni vengono eseguite in un contenitore Docker, il progetto necessita `Dockerfile`di un.  Se non ne è già presente uno, è possibile aggiungere un Dockerfile eseguendo il comando seguente nella radice del progetto di funzioni:

```cli
func init --docker-only
```

Per compilare un'immagine e distribuire le funzioni in Kubernetes, eseguire il comando seguente:

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Sostituire `<name-of-function-deployment>` con il nome dell'app per le funzioni.

Viene creata una risorsa `Deployment` Kubernetes, una `ScaledObject` risorsa e `Secrets`, che include le `local.settings.json` variabili di ambiente importate dal file.

## <a name="removing-a-function-app-from-kubernetes"></a>Rimozione di un'app per le funzioni da Kubernetes

Dopo la distribuzione, è possibile rimuovere una funzione rimuovendo `Deployment`l' `ScaledObject`oggetto associato `Secrets` ,, creato.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Disinstallazione di KEDA da Kubernetes

È possibile eseguire il comando degli strumenti di base seguente per rimuovere KEDA da un cluster Kubernetes:

```cli
func kubernetes remove --namespace keda
```

## <a name="supported-triggers-in-keda"></a>Trigger supportati in KEDA

KEDA è attualmente in versione beta con supporto per i trigger di funzione di Azure seguenti:

* [Code di archiviazione di Azure](functions-bindings-storage-queue.md)
* [Code del bus di servizio di Azure](functions-bindings-service-bus.md)
* [HTTP](functions-bindings-http-webhook.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)

## <a name="next-steps"></a>Fasi successive
Per altre informazioni, vedere le seguenti risorse:

* [Creare una funzione usando un'immagine personalizzata](functions-create-function-linux-custom-image.md)
* [Scrivere codici per Funzioni di Azure e testarle in locale](functions-develop-local.md)
* [Funzionamento del piano a consumo di funzioni di Azure](functions-scale.md)