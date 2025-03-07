---
title: Eseguire l'autenticazione con un Registro Azure Container
description: Opzioni di autenticazione per un Registro Azure Container, inclusi l'accesso con un'identità di Azure Active Directory, l'uso di entità servizio e l'uso di credenziali di amministratore facoltative.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 12/21/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a55cba27c676b283a4da490f05dd6fc672e10d49
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70032379"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Eseguire l'autenticazione con un registro contenitori Docker privato

Esistono diversi modi per eseguire l'autenticazione con un Registro Azure Container, ognuno dei quali è applicabile a uno o più scenari di utilizzo del registro.

Le modalità consigliate includono l'autenticazione a un registro direttamente tramite [singoli account di accesso](#individual-login-with-azure-ad), oppure le applicazioni e gli agenti di orchestrazione dei contenitori possono eseguire l'autenticazione automatica o "senza test" tramite un [servizio di Azure Active Directory (Azure ad) entità](#service-principal).

## <a name="individual-login-with-azure-ad"></a>Accesso individuale con Azure AD

Quando si usa direttamente il registro, ad esempio per eseguire il pull o il push delle immagini da una workstation di sviluppo, eseguire l'autenticazione usando il comando [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) nell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Quando si accede con `az acr login`, l'interfaccia della riga di comando usa il token creato quando si è eseguito [az login](/cli/azure/reference-index#az-login) per l'autenticazione semplificata della sessione al registro. Dopo aver effettuato l'accesso in questo modo, le credenziali vengono memorizzate nella cache e i successivi comandi `docker` nella sessione non richiedono il nome utente o la password. 

Per l'accesso al registro, il token usato da `az acr login` è valido per un'ora, pertanto è consigliabile accedere al registro sempre prima di eseguire un comando `docker`. In caso di scadenza del token, è possibile aggiornarlo usando di nuovo il comando `az acr login` per eseguire nuovamente l'autenticazione. 

L'uso di `az acr login` con le identità di Azure offre l'[accesso in base al ruolo](../role-based-access-control/role-assignments-portal.md). Per alcuni scenari è possibile accedere a un registro con la propria identità singola in Azure AD. Per gli scenari tra servizi o per coordinare le esigenze di un gruppo di lavoro in cui non si vuole gestire l'accesso singolo, è anche possibile accedere con un'[identità gestita per le risorse di Azure](container-registry-authentication-managed-identity.md).

## <a name="service-principal"></a>Entità servizio

Se si assegna un'[entità servizio](../active-directory/develop/app-objects-and-service-principals.md) al registro, l'applicazione o il servizio può usarla per eseguire l'autenticazione headless. Le entità servizio consentono di eseguire l'[accesso in base al ruolo](../role-based-access-control/role-assignments-portal.md) a un registro. È possibile assegnare più entità servizio a un registro. L'uso di più entità servizio consente di definire un accesso diverso per applicazioni diverse.

I ruoli disponibili per un registro contenitori includono:

* **AcrPull**: pull

* **AcrPush**: pull e push

* **Proprietario**: pull, push e assegnazione di ruoli ad altri utenti

Per un elenco completo dei ruoli, vedere [Ruoli e autorizzazioni di Registro Azure Container](container-registry-roles.md).

Per gli script dell'interfaccia della riga di comando per creare un'entità servizio per l'autenticazione con un registro contenitori di Azure e istruzioni sull'uso di un'entità servizio, vedere [autenticazione container Registry di Azure con entità servizio](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Account amministrativo

Ogni registro contenitori include un account utente amministratore che, per impostazione predefinita, è disabilitato. È possibile abilitare l'utente amministratore e gestirne le credenziali nel portale di Azure oppure tramite l'interfaccia della riga di comando o altri strumenti di Azure.

> [!IMPORTANT]
> L'account amministratore è pensato per consentire l'accesso al registro a un singolo utente, principalmente a scopo di test. Non è consigliabile condividere le credenziali dell'account amministratore tra più utenti. Tutti gli utenti che si autenticano con l'account amministratore vengono visualizzati come un unico utente con accesso di tipo push e pull al registro. Se si modifica o si disattiva questo account, tutti gli utenti che ne usano le credenziali non potranno più accedere al registro. Negli scenari di tipo headless è consigliabile che gli utenti e le entità servizio abbiano una propria identità.
>

L'account amministratore è dotato di due password, entrambe rigenerabili. L'uso di due password consente di mantenere la connessione al registro usando una password mentre si rigenera l'altra. Se l'account amministratore è abilitato, è possibile passare il nome utente e una password al comando `docker login` quando richiesto, per eseguire l'autenticazione di base al registro. Ad esempio:

```
docker login myregistry.azurecr.io 
```

Per le procedure consigliate per gestire le credenziali di accesso, vedere le informazioni di riferimento sul comando [Docker login](https://docs.docker.com/engine/reference/commandline/login/) .

Per consentire a un utente amministratore di accedere a un registro esistente, è possibile usare il parametro `--admin-enabled` del comando [az acr update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) nell'interfaccia della riga di comando di Azure:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Per abilitare l'utente amministratore nel portale di Azure, passare al registro interessato, selezionare **Chiavi di accesso** in **IMPOSTAZIONI**, quindi selezionare **Abilita** in **Utente amministratore**.

![Abilitare l'interfaccia utente dell'utente amministratore nel portale di Azure][auth-portal-01]

## <a name="next-steps"></a>Passaggi successivi

* [Effettuare il push della prima immagine tramite l'interfaccia della riga di comando di Azure](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
