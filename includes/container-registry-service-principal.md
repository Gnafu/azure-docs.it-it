---
title: File di inclusione
description: File di inclusione
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 04/23/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 6ed114ea6162c9d4888b6f86998cfb422a3944e8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
## <a name="create-a-service-principal"></a>Creare un'entità servizio

Per creare un'entità servizio con accesso al registro contenitori, è possibile usare lo script seguente. Aggiornare la variabile `ACR_NAME` con il nome del registro contenitori e facoltativamente, il valore `--role` nel comando [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] per concedere autorizzazioni diverse. È necessario avere installato l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) per usare questo script.

Dopo aver eseguito lo script, prendere nota dell'**ID** e della **password** dell'entità servizio. Dopo aver ottenuto le credenziali, è possibile configurare le applicazioni e i servizi per l'autenticazione nel registro contenitori come entità servizio.

[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

## <a name="use-an-existing-service-principal"></a>Usare un'entità servizio esistente

Per concedere al registro l'accesso a un'entità servizio esistente, è necessario assegnare un nuovo ruolo all'entità servizio. Come per la creazione di una nuova entità servizio, è possibile concedere l'accesso per pull, push e pull e proprietario.

Lo script seguente usa il comando [az role assignment create][az-role-assignment-create] per concedere le autorizzazioni *pull* a un'entità servizio specificata nella variabile `SERVICE_PRINCIPAL_ID`. Modificare il valore `--role` se si vuole concedere un livello di accesso diverso.

[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
