---
title: Acceleratori di soluzioni di Azure IoT e Azure Active Directory | Microsoft Docs
description: Descrive in che modo gli acceleratori di soluzioni di Azure IoT Suite usano Azure Active Directory per gestire le autorizzazioni.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: 676d5e553e2929ae09d447141ca315fd1cc448e3
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448951"
---
# <a name="permissions-on-the-azureiotsolutionscom-site"></a>Autorizzazioni per il sito azureiotsolutions.com

## <a name="what-happens-when-you-sign-in"></a>Cosa accade quando si accede

Quando si accede per la prima volta ad [azureiotsuite.com][lnk-azureiotsolutions], il sito determina i livelli di autorizzazione in base al tenant di Azure Active Directory (AAD) attualmente selezionato e alla sottoscrizione di Azure.

1. Per prima cosa, il sito cerca prima in Azure a quale tenant di AAD appartiene l'utente per popolare l'elenco di tenant visualizzato accanto al nome utente connesso. In questo momento, il sito può ricevere solo i token utente per un tenant alla volta. Di conseguenza, quando si passa a un tenant diverso usando l'elenco a discesa nell'angolo superiore destro, il sito registra di nuovo l'utente nel tenant per ottenere il token di tale tenant.

2. Successivamente, il sito cerca in Azure quali sottoscrizioni sono state associate dall'utente al tenant selezionato. Le sottoscrizioni disponibili vengono visualizzate quando si crea un nuovo acceleratore di soluzioni.

3. Infine, il sito recupera tutte le risorse nelle sottoscrizioni e nei gruppi di risorse contrassegnati come acceleratori di soluzioni e popola i riquadri nella home page.

Le sezioni seguenti descrivono i ruoli che controllano l'accesso agli acceleratori di soluzioni.

## <a name="aad-roles"></a>Ruoli AAD

I ruoli AAD controllano la capacità di eseguire il provisioning degli acceleratori di soluzioni, di gestire gli utenti e alcuni servizi di Azure in un acceleratore di soluzioni.

Per altre informazioni sui ruoli di amministratore in AAD, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory][lnk-aad-admin]. L'articolo corrente è incentrato sui ruoli della directory di **Amministratore globale** e **Utente** usati dagli acceleratori di soluzioni.

### <a name="global-administrator"></a>Amministratore globale

Per ogni tenant di AAD possono essere presenti più amministratori globali:

* Quando si crea un tenant di AAD, si è per impostazione predefinita l'amministratore globale del tenant.
* L'amministratore globale può eseguire il provisioning di un criterio di acceleratori di base e standard (una distribuzione di base usa una singola Macchina Virtuale di Azure).

### <a name="domain-user"></a>Utente di dominio

Per ogni tenant di AAD possono essere presenti molti utenti del dominio:

* Un utente di dominio può effettuare il provisioning di un acceleratore di soluzioni di base tramite il sito [azureiotsolutions.com][lnk-azureiotsolutions].
* Un utente di dominio può creare un acceleratore di soluzioni di base tramite l'interfaccia della riga di comando.

### <a name="guest-user"></a>Utente guest

Per ogni tenant AAD possono essere presenti molti utenti guest. Gli utenti guest hanno un set di diritti limitato nel tenant di AAD. Di conseguenza, gli utenti guest non possono effettuare il provisioning di un acceleratore di soluzioni nel tenant di AAD.

Per altre informazioni sugli utenti e i ruoli in AAD, vedere le risorse seguenti:

* [Creare utenti in Azure AD][lnk-create-edit-users]
* [Assegnare utenti alle app][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Ruoli di amministratore della sottoscrizione di Azure

I ruoli di amministrazione di Azure controllano la possibilità di eseguire il mapping di una sottoscrizione di Azure a un tenant di AAD.

È possibile trovare altre informazioni sui ruoli di amministratore nell'articolo [Come aggiungere o modificare i ruoli Co-amministratore, Amministratore del servizio e Amministratore account di Azure][lnk-admin-roles].

## <a name="faq"></a>Domande frequenti

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>Un amministratore del servizio vuole modificare il mapping della directory tra la sottoscrizione e un tenant di AAD specifico. Come completare questa attività

Vedere [Come aggiungere una sottoscrizione esistente alla directory di Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Si vuole modificare un amministratore del servizio o coamministratore quando si accede con un account dell'organizzazione

Vedere l'articolo di supporto [Modifica dell'amministratore del servizio e del coamministratore quando si effettua l'accesso con un account dell'organizzazione][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Perché viene visualizzato un errore simile al seguente? "L'account non ha le autorizzazioni appropriate per creare una soluzione. Rivolgersi all'amministratore account o provare con un account diverso".

Per indicazioni vedere il diagramma seguente:

![][img-flowchart]

> [!NOTE]
> Se si continua a visualizzare l'errore dopo aver confermato di essere un amministratore globale del tenant AAD e un coamministratore della sottoscrizione, l'amministratore dell'account dovrà rimuovere l'utente e assegnare nuovamente le autorizzazioni necessarie in questo ordine. Innanzitutto, dovrà aggiungere l'utente come amministratore globale e quindi aggiungere l'utente come coamministratore della sottoscrizione di Azure. Se i problemi persistono, contattare il tema [Guida e supporto][lnk-help-support].

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Perché viene visualizzato questo errore quando si dispone di una sottoscrizione di Azure? "Per creare soluzioni preconfigurate è necessaria una sottoscrizione di Azure. È possibile creare un account di valutazione gratuito in pochi minuti."

Se si è certi di avere una sottoscrizione di Azure, convalidare il mapping del tenant per la sottoscrizione e verificare che sia selezionato il tenant corretto nell'elenco a discesa. Se si è verificato che il tenant desiderato è corretto, seguire il diagramma precedente e verificare il mapping della sottoscrizione e il tenant di AAD.

## <a name="next-steps"></a>Passaggi successivi
Per approfondire gli acceleratori di soluzioni, vedere come è possibile [personalizzare un acceleratore di soluzioni][lnk-customize].

[img-flowchart]: media/iot-accelerators-permissions/flowchart.png

[lnk-azureiotsolutions]: https://www.azureiotsolutions.com
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]:../active-directory/users-groups-roles/directory-assign-admin-roles.md
[lnk-portal]: https://portal.azure.com
[lnk-create-edit-users]:../active-directory/fundamentals/active-directory-users-profile-azure-portal.md
[lnk-assign-app-roles]:../active-directory/manage-apps/assign-user-or-group-access-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-accelerators-remote-monitoring-customize.md
