---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Creare app con la distribuzione continua da GitHub | Microsoft Docs
description: Esempio di script dell'interfaccia della riga di comando di Azure - Creare un'app ed eseguire la distribuzione continua da GitHub
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 09/02/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 02853186a1f28d8c6db6b5421c6ebba0640461cb
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "71057883"
---
# <a name="create-an-app-service-app-with-continuous-deployment-from-github-using-cli"></a>Creare un'app del servizio app con distribuzione continua da GitHub usando l'interfaccia della riga di comando

Questo script di esempio crea un'app nel servizio app con le risorse correlate e quindi configura la distribuzione continua da un repository GitHub. Per la distribuzione da GitHub senza distribuzione continua, vedere [Creare un'app e distribuire il codice da GitHub](cli-deploy-github.md). Per questo esempio è necessario:

* Repository GitHub contenente il codice dell'applicazione, con le relative autorizzazioni di amministratore. Per accedere alle compilazioni automatiche, strutturare il repository in base alla tabella [Preparare il repository](../deploy-continuous-deployment.md#prepare-your-repository).
* [Token di accesso personale](https://help.github.com/articles/creating-an-access-token-for-command-line-use) per il proprio account GitHub.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, è necessaria la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-github-continuous/deploy-github-continuous.sh?highlight=3-4 "Create an app with continuous deployment from GitHub")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Consente di creare un piano di servizio app. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Consente di creare un'app del servizio app. |
| [`az webapp deployment source config`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config) | Consente di associare un'app del servizio app a un repository GIT o Mercurial. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando del servizio app sono disponibili nella [documentazione del servizio app di Azure](../samples-cli.md).
