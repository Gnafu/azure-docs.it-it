---
title: Creare cluster Apache Hadoop mediante modelli - Azure HDInsight
description: Informazioni su come creare cluster per HDInsight usando modelli di Resource Manager
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 73402421a87d2cf14719ff34201890ea96c90519
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64715245"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Creare cluster Apache Hadoop in HDInsight mediante modelli di Resource Manager
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

In questo articolo vengono illustrati diversi modi per creare cluster di Azure HDInsight con modelli di Gestione risorse di Azure. Per altre informazioni, vedere [Distribuire un'applicazione con il modello di Gestione risorse di Azure](../azure-resource-manager/resource-group-template-deploy.md). Per informazioni su altri strumenti e funzionalità per la creazione di cluster, fare clic sul selettore di schede nella parte superiore della pagina o vedere [Metodi di creazione di cluster](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

## <a name="prerequisites"></a>Prerequisiti
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Per seguire le istruzioni di questo articolo sono necessari:

* Una [sottoscrizione di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell e/o l'interfaccia della riga di comando classica di Azure.

### <a name="resource-manager-templates"></a>Modelli di Gestione risorse
Un modello di Resource Manager semplifica creare le risorse seguenti per l'applicazione in un'unica operazione coordinata:
* Cluster HDInsight e le relative risorse dipendenti, ad esempio l'account di archiviazione predefinito.
* Altre risorse, ad esempio il database SQL di Azure per usare [Apache Sqoop](https://sqoop.apache.org/).

Nel modello si definiscono le risorse necessarie per l'applicazione. È anche possibile specificare i parametri di distribuzione per immettere valori per ambienti diversi. Il modello è composto da JSON ed espressioni da usare per creare valori per la distribuzione.

È possibile trovare esempi di modelli di HDInsight in [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight). Usare lo strumento multipiattaforma [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) con l'[estensione per Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) o un editor di testo per salvare il modello in un file sulla workstation. 

Per altre informazioni sui modelli di Resource Manager, consultare gli articoli e gli esempi seguenti:

* [Creazione di modelli di Gestione risorse di Azure](../azure-resource-manager/resource-group-authoring-templates.md)
* [Distribuire un'applicazione con i modelli di Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md)
* Informazioni di riferimento sul modello [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/allversions)
* [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>Generare modelli

Gestione risorse consente di esportare un modello di Gestione risorse dalle risorse esistenti nella sottoscrizione usando diversi strumenti. Il modello generato può essere usato per ottenere informazioni sulla sintassi del modello o per automatizzare la ridistribuzione della soluzione in base alle esigenze. Per altre informazioni, vedere [esportare modelli](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates).

## <a name="deploy-using-the-portal"></a>Distribuire tramite il portale

È possibile distribuire un modello di Gestione risorse tramite il portale di Azure. Per altre informazioni, vedere [Distribuire risorse da un modello personalizzato](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Distribuire tramite PowerShell

È possibile distribuire un modello di Gestione risorse tramite Azure PowerShell. Per altre informazioni, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) e [Distribuire un modello di Resource Manager privato con un token di firma di accesso condiviso e Azure PowerShell](../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="deploy-using-azure-cli"></a>Eseguire la distribuzione usando l'interfaccia della riga di comando di Azure

È possibile distribuire un modello di Gestione risorse tramite l'interfaccia della riga di comando classica. Per altre informazioni, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) e [Distribuire un modello di Resource Manager privato con un token di firma di accesso condiviso e l'interfaccia della riga di comando di Azure](../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>Distribuire tramite l'API REST
È possibile distribuire un modello di Gestione risorse tramite l'API REST. Per altre informazioni, vedere [Distribuire le risorse con i modelli e l'API REST di Resource Manager](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Distribuire con Visual Studio
 Usare Visual Studio per creare un progetto del gruppo di risorse e distribuirlo in Azure mediante l'interfaccia utente. Selezionare il tipo di risorse da includere nel progetto. Tali risorse vengono aggiunte automaticamente al modello di Resource Manager. Il progetto fornisce anche uno script di PowerShell per distribuire il modello.

Per un'introduzione all'uso di Visual Studio con gruppi di risorse, vedere [Creazione e distribuzione di gruppi di risorse di Azure tramite Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="troubleshoot"></a>Risolvere problemi

Se si verificano problemi di creazione dei cluster HDInsight, vedere i [requisiti dei controlli di accesso](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Passaggi successivi
Questo articolo ha spiegato vari modi per creare un cluster HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* Per HDInsight più modelli correlati, vedere [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Per un esempio di distribuzione delle risorse tramite la libreria client .NET, vedere [Distribuire le risorse usando le librerie .NET e un modello](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Per un esempio dettagliato di distribuzione di un'applicazione, vedere [Effettuare il provisioning di microservizi e distribuirli in modo prevedibile in Azure](../app-service/deploy-complex-application-predictably.md).
* Per indicazioni sulla distribuzione della soluzione in ambienti diversi, vedere [Ambienti di sviluppo e test in Microsoft Azure](../solution-dev-test-environments.md).
* Per informazioni sulle sezioni del modello di Azure Resource Manager, vedere [Creazione di modelli](../azure-resource-manager/resource-group-authoring-templates.md).
* Per un elenco delle funzioni che è possibile usare in un modello di Azure Resource Manager, vedere [Funzioni di modello](../azure-resource-manager/resource-group-template-functions.md).
