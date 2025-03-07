---
title: (DEPRECATO) Monitorare un cluster DC/OS di Azure - Stack ELK
description: Monitorare un cluster DC/OS nel cluster del servizio Azure Container con ELK (Elasticsearch, Logstash e Kibana).
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 342cf23db2df7d7c79a2b56df96d1a78d6ba215e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61467716"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-elk"></a>(DEPRECATO) Monitorare un cluster del servizio Azure Container con ELK

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Questo articolo descrive come distribuire lo stack ELK (Elasticsearch, Logstash, Kibana) in un cluster DC/OS nel servizio Azure Container. 

## <a name="prerequisites"></a>Prerequisiti
[Distribuire](container-service-deployment.md) e [connettere](../container-service-connect.md) un cluster DC/OS configurato dal servizio Azure Container. Esplorare il dashboard di DC/OS e i servizi Marathon [qui](container-service-mesos-marathon-ui.md). Installare anche il [servizio di bilanciamento del carico Marathon](container-service-load-balancing.md).


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Elasticsearch, Logstash, Kibana)
Lo stack ELK, una combinazione di Elasticsearch, Logstash e Kibana, fornisce uno stack end-to-end che può essere usato per monitorare e analizzare i log nel cluster.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Configurare lo stack ELK in un cluster DC/OS
Accedere all'interfaccia utente del controller di dominio/sistema operativo da [http://localhost:80/](http://localhost:80/) Nell'interfaccia utente del controller di dominio/sistema operativo passare a **Universe** (Universo). Cercare e installare Elasticsearch, Logstash e Kibana da DC/OS Universe in questo ordine specifico. Altre informazioni sulla configurazione sono disponibili nel collegamento relativo all'**installazione avanzata**.

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

Dopo che i contenitori ELK sono attivi, è necessario abilitare l'accesso a Kibana tramite Marathon-LB. Passare a **Servizi** > **kibana** e fare clic su **Modifica**, come illustrato di seguito.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


Passare alla **modalità JSON** ed eseguire lo scorrimento fino alla sezione relativa alle etichette.
È necessario aggiungere una voce `"HAPROXY_GROUP": "external"` qui, come mostrato di seguito.
Dopo avere fatto clic su **Distribuisci modifiche**, il contenitore viene riavviato.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Se si vuole verificare che Kibana sia registrato come servizio nel dashboard di HAPROXY, è necessario aprire la porta 9090 nel cluster agente poiché HAPROXY viene eseguito in questa porta.
Per impostazione predefinita vengono aperte le porte 80, 8080 e 443 nel cluster agente DC/OS.
Istruzioni su come aprire una porta e fornire l'accesso pubblico sono disponibili [qui](container-service-enable-public-access.md).

Per accedere al dashboard di HAPROXY, aprire l'interfaccia di amministrazione di Marathon LB all'indirizzo: `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`.
Quando si passa all'URL, verrà visualizzato il dashboard di HAPROXY come illustrato di seguito e verrà visualizzata una voce del servizio per Kibana.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


Per accedere al dashboard di Kibana distribuito nella porta 5601, è necessario aprire la porta 5601. Seguire le istruzioni [qui](container-service-enable-public-access.md). Aprire quindi il dashboard di Kibana all'indirizzo: `http://localhost:5601`.

## <a name="next-steps"></a>Passaggi successivi

* Per l'inoltro e la configurazione del log di sistema e dell'applicazione, vedere [Log Management in DC/OS with ELK](https://docs.mesosphere.com/1.8/administration/logging/elk/) (Gestione log nel controller di dominio o sistema operativo con ELK).

* Per filtrare i log, vedere [Filtering Logs with ELK](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/) (Filtrare i log con ELK). 

 

