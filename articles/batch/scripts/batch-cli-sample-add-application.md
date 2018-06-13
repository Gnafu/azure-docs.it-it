---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Aggiungere un'applicazione in Batch | Microsoft Docs
description: Esempio di script dell'interfaccia della riga di comando di Azure - Aggiungere un'applicazione in Batch
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: danlep
ms.openlocfilehash: 80b9cb749be942f72459180182e7d74a45f6a943
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
ms.locfileid: "29843103"
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>Esempio di interfaccia della riga di comando: aggiungere un'applicazione a un account Azure Batch

Questo script mostra come aggiungere un'applicazione per l'uso con un'attività o un pool di Azure Batch. Per configurare un'applicazione da aggiungere all'account Batch, creare un pacchetto dell'eseguibile, insieme a eventuali dipendenze, come file ZIP. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo articolo richiede la versione 2.0.20 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Eseguire il comando seguente per rimuovere il gruppo di risorse e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti.
Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Crea un account di archiviazione. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Crea l'account Batch. |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Effettua l'autenticazione con l'account Batch specificato per un'ulteriore interazione con l'interfaccia della riga di comando.  |
| [az batch application create](/cli/azure/batch/application#az_batch_application_create) | Crea un'applicazione.  |
| [az batch application package create](/cli/azure/batch/application/package#az_batch_application_package_create) | Aggiunge un pacchetto di applicazione per l'applicazione specificata.  |
| [az batch application set](/cli/azure/batch/application#az_batch_application_set) | Aggiorna le proprietà di un'applicazione.  |
| [az group delete](/cli/azure/group#az_group_delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).
