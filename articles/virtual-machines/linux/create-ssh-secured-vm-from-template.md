---
title: Creare una VM Linux in Azure da un modello | Microsoft Docs
description: Procedura per usare l'interfaccia della riga di comando di Azure per creare una VM Linux da un modello di Gestione risorse
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/30/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3e6b431ee55ee73b4f5a69471cca3cc16270198c
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37930240"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Procedura per creare una macchina virtuale Linux con i modelli di Azure Resource Manager
Questo articolo illustra come distribuire rapidamente una macchina virtuale (VM) Linux con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure. 


## <a name="templates-overview"></a>Panoramica dei modelli
I modelli di Azure Resource Manager sono file JSON che definiscono l'infrastruttura e la configurazione della soluzione Azure. Usando il modello è possibile distribuire ripetutamente la soluzione nel corso del ciclo di vita garantendo al contempo che le risorse vengano distribuite in uno stato coerente. Per altre informazioni sul formato del modello e sulla sua costruzione, vedere [Creare il primo modello di Azure Resource Manager](../../azure-resource-manager/resource-manager-create-first-template.md). Per visualizzare la sintassi JSON per i tipi di risorse, vedere [Define resources in Azure Resource Manager templates](/azure/templates/) (Definire le risorse nei modelli di Azure Resource Manager).


## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Il gruppo di risorse deve essere creato prima della macchina virtuale. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroupVM* nell'area *stati uniti orientali*:

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale
L'esempio seguente crea una VM da [questo modello di Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json) con [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create). È consentita solo l'autenticazione SSH. Quando richiesto, specificare il valore della chiave pubblica SSH, ad esempio il contenuto di *~/.ssh/id_rsa.pub*. Se è necessario creare una coppia di chiavi SSH, vedere [Come creare e usare una coppia di chiavi SSH pubblica e privata per le VM Linux in Azure](mac-create-ssh-keys.md).

```azurecli
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

Nell'esempio precedente è stato specificato un modello archiviato in GitHub. È anche possibile scaricare o creare un modello e specificare il percorso locale con il parametro `--template-file`.


## <a name="connect-to-virtual-machine"></a>Connettersi alla macchina virtuale
Per la connessione SSH alla macchina virtuale, ottenere l'indirizzo IP pubblico con [az vm show](/cli/azure/vm#az-vm-show):

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name sshvm \
    --show-details \
    --query publicIps \
    --output tsv
```

È quindi possibile usare la connessione SSH alla VM nel modo usuale. Fornire il proprio indirizzo IP pubblico dal comando precedente:

```bash
ssh azureuser@<ipAddress>
```

## <a name="next-steps"></a>Passaggi successivi
In questo esempio è stata creata una VM Linux di base. Per altri modelli di Resource Manager che includono i framework di applicazioni o creano ambienti più complessi, visitare la [Raccolta di modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/).