---
title: Risolvere errori comuni durante la distribuzione di risorse in Azure | Microsoft Docs
description: Descrive come risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager.
tags: top-support-issue
author: tfitzmac
keywords: errore di distribuzione, distribuzione di azure, distribuire in azure
ms.service: azure-resource-manager
ms.topic: troubleshooting
ms.date: 08/30/2019
ms.author: tomfitz
ms.openlocfilehash: fc6fdde4daa2d671b9d93673c2a78c2d9d85963c
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70275731"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager

Questo argomento descrive alcuni errori comuni che possono verificarsi durante la distribuzione di risorse in Azure e fornisce indicazioni sulla relativa risoluzione. Se non si trova il codice di errore per l'errore di distribuzione specifico, vedere [Trovare il codice di errore](#find-error-code).

Se si stanno cercando informazioni su un codice di errore e tali informazioni non sono disponibili in questo articolo, è possibile segnalarle. Nella parte inferiore della pagina è possibile lasciare il feedback. Il feedback viene registrato con i problemi di GitHub. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="error-codes"></a>Codici di errore

| Codice di errore | Attenuazione | Altre informazioni |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Seguire le limitazioni relative all'assegnazione dei nomi per gli account di archiviazione. | [Risolvere gli errori relativi ai nomi degli account di archiviazione](resource-manager-storage-account-name-errors.md) |
| AccountPropertyCannotBeSet | Controllare le proprietà dell'account di archiviazione disponibili. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | Il cluster o l'area non ha risorse disponibili o non può supportare le dimensioni di macchina virtuale richieste. Ripetere la richiesta in un secondo momento oppure richiedere una dimensione di macchina virtuale diversa. | [Problemi di provisioning e allocazione per Linux](../virtual-machines/linux/troubleshoot-deployment-new-vm.md), [Problemi di provisioning e allocazione per Windows](../virtual-machines/windows/troubleshoot-deployment-new-vm.md) e [Risolvere i problemi relativi agli errori di allocazione](../virtual-machines/troubleshooting/allocation-failure.md)|
| AnotherOperationInProgress | Attendere il completamento dell'operazione simultanea. | |
| AuthorizationFailed | L'account o l'entità servizio non dispone dell'accesso sufficiente per completare la distribuzione. Selezionare il ruolo a cui appartiene l'account e il relativo accesso per l'ambito della distribuzione.<br><br>Questo errore può essere visualizzato quando un provider di risorse richiesto non è registrato. | [Controllo degli accessi in base al ruolo di Azure](../role-based-access-control/role-assignments-portal.md)<br><br>[Risoluzione degli errori di registrazione del provider di risorse](resource-manager-register-provider-errors.md) |
| BadRequest | I valori della distribuzione inviati non corrispondono ai valori previsti da Resource Manager. Per informazioni sulla risoluzione dei problemi, controllare il messaggio di stato interno. | [Informazioni di riferimento sul modello](/azure/templates/) e [Località supportate](resource-location.md) |
| Conflitto | Si sta richiedendo un'operazione non consentita nello stato corrente della risorsa. Il ridimensionamento del disco, ad esempio, è consentito solo quando viene creata o deallocata una macchina virtuale. | |
| DeploymentActive | Attendere il completamento della distribuzione simultanea al gruppo di risorse. | |
| DeploymentFailed | DeploymentFailed è un errore generale che non fornisce i dettagli necessari per risolvere l'errore. Nei dettagli cercare un codice di errore che fornisca maggiori informazioni. | [Trovare il codice di errore](#find-error-code) |
| DeploymentQuotaExceeded | Se si raggiunge il limite di 800 distribuzioni per gruppo di risorse, eliminare dalla cronologia le distribuzioni che non sono più necessarie. È possibile eliminare le voci dalla cronologia usando [az group deployment delete](/cli/azure/group/deployment#az-group-deployment-delete) nell'interfaccia della riga di comando di Azure o [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) in PowerShell. L'eliminazione di una voce dalla cronologia della distribuzione non ha effetto sulle risorse distribuite. | |
| DnsRecordInUse | Il nome del record DNS deve essere univoco. Immettere un nome diverso. | |
| ImageNotFound | Controllare le impostazioni dell'immagine della macchina virtuale. |  |
| InUseSubnetCannotBeDeleted | Questo errore può essere ricevuto quando si tenta di aggiornare una risorsa e la richiesta viene elaborata eliminando e creando la risorsa. Assicurarsi di specificare tutti i valori invariati. | [Aggiornare una risorsa](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Ottenere il token di accesso per il tenant appropriato. È possibile ottenere solo il token dal tenant a cui appartiene l'account. | |
| InvalidContentLink | È probabile che si sia tentato di eseguire il collegamento a un modello annidato che non è disponibile. Ricontrollare l'URI specificato per il modello annidato. Se il modello si trova in un account di archiviazione, verificare che l'URI sia accessibile. Potrebbe essere necessario passare un token SAS. Attualmente, non è possibile collegarsi a un modello che si trova in un account di archiviazione dietro un [firewall di archiviazione di Azure](../storage/common/storage-network-security.md). Provare a trasferire il modello in un altro repository, ad esempio GitHub. | [Modelli collegati](resource-group-linked-templates.md) |
| InvalidParameter | Uno dei valori forniti per una risorsa non corrisponde al valore previsto. Questo errore può dipendere da molte condizioni diverse. Ad esempio, è possibile che una password non sia sufficiente o che un nome di BLOB non sia corretto. Il messaggio di errore dovrebbe indicare il valore che deve essere corretto. | |
| InvalidRequestContent | I valori di distribuzione includono valori non riconosciuti oppure mancano i valori obbligatori. Confermare i valori per il tipo di risorsa. | [Informazioni di riferimento sul modello](/azure/templates/) |
| InvalidRequestFormat | Abilitare la registrazione del debug quando si esegue la distribuzione e verificare il contenuto della richiesta. | [Registrazione del debug](#enable-debug-logging) |
| InvalidResourceNamespace | Controllare lo spazio dei nomi della risorsa specificato nella proprietà **type**. | [Informazioni di riferimento sul modello](/azure/templates/) |
| InvalidResourceReference | La risorsa non esiste ancora o viene referenziata in modo non corretto. Controllare se è necessario aggiungere una dipendenza. Verificare che l'utilizzo della funzione **reference** includa i parametri necessari per lo scenario in uso. | [Risolvere gli errori relativi alle risorse di Azure non trovate](resource-manager-not-found-errors.md) |
| InvalidResourceType | Controllare il tipo di risorsa specificato nella proprietà **type**. | [Informazioni di riferimento sul modello](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Registrare la sottoscrizione con il provider di risorse. | [Risoluzione degli errori di registrazione del provider di risorse](resource-manager-register-provider-errors.md) |
| InvalidTemplate | Ricercare eventuali errori nella sintassi del modello. | [Risolvere errori dovuti a modelli non validi](resource-manager-invalid-template-errors.md) |
| InvalidTemplateCircularDependency | Rimuovere le dipendenze non necessarie. | [Risolvere le dipendenze circolari](resource-manager-invalid-template-errors.md#circular-dependency) |
| LinkedAuthorizationFailed | Controllare se l'account appartiene allo stesso tenant del gruppo di risorse in cui si sta eseguendo la distribuzione. | |
| LinkedInvalidPropertyId | La risoluzione dell'ID risorsa per una risorsa non è stato eseguito correttamente. Verificare di avere fornito tutti i valori necessari per l'ID risorsa, incluso l'ID sottoscrizione, il nome del gruppo di risorse, il tipo di risorsa, il nome della risorsa padre (se necessario) e il nome della risorsa. | |
| LocationRequired | Specificare un percorso per la risorsa. | [Impostare la posizione](resource-location.md) |
| MismatchingResourceSegments | Assicurarsi che la risorsa nidificata abbia il numero corretto di segmenti nel nome e nel tipo. | [Risolvere i segmenti di risorse](resource-manager-invalid-template-errors.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | Verificare lo stato di registrazione del provider di risorse e i percorsi supportati. | [Risoluzione degli errori di registrazione del provider di risorse](resource-manager-register-provider-errors.md) |
| MissingSubscriptionRegistration | Registrare la sottoscrizione con il provider di risorse. | [Risoluzione degli errori di registrazione del provider di risorse](resource-manager-register-provider-errors.md) |
| NoRegisteredProviderFound | Controllare lo stato della registrazione del provider di risorse. | [Risoluzione degli errori di registrazione del provider di risorse](resource-manager-register-provider-errors.md) |
| Non trovato | È possibile che si stia tentando di distribuire una risorsa dipendente in parallelo con una risorsa padre. Controllare se è necessario aggiungere una dipendenza. | [Risolvere gli errori relativi alle risorse di Azure non trovate](resource-manager-not-found-errors.md) |
| OperationNotAllowed | La distribuzione sta tentando di eseguire un'operazione che supera la quota per la sottoscrizione, il gruppo di risorse o l'area. Se possibile, modificare la distribuzione in modo da non superare le quote. In alternativa è possibile richiedere una modifica delle quote. | [Risolvere gli errori di quota delle risorse](resource-manager-quota-errors.md) |
| ParentResourceNotFound | Assicurarsi che esista una risorsa padre prima di creare le risorse figlio. | [Risolvere gli errori delle risorse padre](resource-manager-parent-resource-errors.md) |
| PasswordTooLong | Potrebbe essere stata selezionata una password con troppi caratteri oppure il valore della password è stato convertito in una stringa sicura prima di passarlo come parametro. Se il modello include un parametro **secure string**, non è necessario convertire il valore in una stringa sicura. Specificare il valore della password come testo. |  |
| PrivateIPAddressInReservedRange | L'indirizzo IP specificato include un intervallo di indirizzi richiesto da Azure. Modificare l'indirizzo IP in modo da evitare l'intervallo riservato. | [Indirizzi IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | L'indirizzo IP specificato non è compreso nell'intervallo di subnet. Modificare l'indirizzo IP in modo che sia compreso nell'intervallo di subnet. | [Indirizzi IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Alcune proprietà non possono essere modificate in una risorsa distribuita. Quando si aggiorna una risorsa, è possibile limitare le modifiche alle proprietà consentite. | [Aggiornare una risorsa](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | La sottoscrizione include un criterio di risorsa che impedisce l'esecuzione di un'azione che si sta tentando di eseguire durante la distribuzione. Individuare il criterio che blocca l'azione. Se possibile, modificare la distribuzione in modo che soddisfi le limitazioni del criterio. | [Errore RequestDisallowedByPolicy con i criteri delle risorse di Azure](resource-manager-policy-requestdisallowedbypolicy-error.md) |
| ReservedResourceName | Specificare un nome di risorsa che non includa un nome riservato. | [Nomi di risorse riservati](resource-manager-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Attendere il completamento dell'eliminazione. | |
| ResourceGroupNotFound | Controllare il nome del gruppo della risorse di destinazione per la distribuzione. Il gruppo di risorse di destinazione deve essere già presente nella sottoscrizione. Controllare il contesto della sottoscrizione. | [Interfaccia della riga di comando di Azure](/cli/azure/account?#az-account-set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| Risorsa non trovata | La distribuzione referenzia una risorsa di cui non è possibile eseguire la risoluzione. Verificare che l'utilizzo della funzione **reference** includa i parametri necessari per lo scenario in uso. | [Risolvere gli errori relativi ai riferimenti](resource-manager-not-found-errors.md) |
| ResourceQuotaExceeded | La distribuzione sta tentando di creare risorse che superano la quota per la sottoscrizione, il gruppo di risorse o l'area. Se possibile, modificare l'infrastruttura in modo da non superare le quote. In alternativa è possibile richiedere una modifica delle quote. | [Risolvere gli errori di quota delle risorse](resource-manager-quota-errors.md) |
| SkuNotAvailable | Selezionare lo SKU, ad esempio le dimensioni delle macchine virtuali, disponibile per la posizione selezionata. | [Risolvere gli errori dovuti all'indisponibilità di SKU](resource-manager-sku-not-available-errors.md) |
| StorageAccountAlreadyExists | Fornire un nome univoco per l'account di archiviazione. | [Risolvere gli errori relativi ai nomi degli account di archiviazione](resource-manager-storage-account-name-errors.md)  |
| StorageAccountAlreadyTaken | Fornire un nome univoco per l'account di archiviazione. | [Risolvere gli errori relativi ai nomi degli account di archiviazione](resource-manager-storage-account-name-errors.md) |
| StorageAccountNotFound | Controllare la sottoscrizione, il gruppo di risorse e il nome dell'account di archiviazione che si sta tentando di usare. | |
| SubnetsNotInSameVnet | Una macchina virtuale può avere solo una rete virtuale. Quando si distribuiscono più schede di interfaccia di rete, assicurarsi che appartengano alla stessa rete virtuale. | [Più schede di interfaccia di rete](../virtual-machines/windows/multiple-nics.md) |
| TemplateResourceCircularDependency | Rimuovere le dipendenze non necessarie. | [Risolvere le dipendenze circolari](resource-manager-invalid-template-errors.md#circular-dependency) |
| TooManyTargetResourceGroups | Ridurre il numero di gruppi di risorse per una distribuzione singola. | [Distribuzione in più gruppi di risorse](resource-manager-cross-resource-group-deployment.md) |

## <a name="find-error-code"></a>Trovare il codice di errore

I possibili errori sono di due tipi:

* errori di convalida
* errori di distribuzione

Gli errori di convalida sono causati da scenari già determinati prima della distribuzione. Tra di essi figurano gli errori di sintassi presenti in un modello oppure il tentativo di distribuire risorse superiori alle quote di sottoscrizione. Gli errori di distribuzione sorgono da condizioni che si verificano durante il processo di distribuzione. Tra essi configurano i tentativi di accedere a una risorsa che viene distribuita in parallelo.

Entrambi i tipi di errore restituiscono un codice necessario per risolvere i problemi della distribuzione. Entrambi i tipi di errori vengono visualizzati nel [log attività](resource-group-audit.md). Gli errori di convalida non sono invece visualizzati nella cronologia della distribuzione, poiché quest'ultima non è mai stata avviata.

### <a name="validation-errors"></a>Errori di convalida

Durante la distribuzione nel portale, viene visualizzato un errore di convalida dopo l'invio dei valori.

![vista dell'errore di convalida nel portale](./media/resource-manager-common-deployment-errors/validation-error.png)

Selezionare il messaggio per avere altre informazioni. Nella figura seguente viene visualizzato un errore **InvalidTemplateDeployment** e un messaggio che indica che la distribuzione è stata bloccata da un criterio.

![visualizzare le informazioni di convalida](./media/resource-manager-common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Errori di distribuzione

Quando l'operazione supera la convalida, ma non esegue correttamente la distribuzione, viene visualizzato un errore di distribuzione.

Per visualizzare i codici di errore di distribuzione e i messaggi con PowerShell, usare:

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

Per visualizzare i codici di errore di distribuzione e i messaggi con l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
az group deployment operation list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

Dal portale, selezionare la notifica.

![Notifica di errore](./media/resource-manager-common-deployment-errors/notification.png)

Vengono visualizzati maggiori dettagli sulla distribuzione. Selezionare l'opzione per trovare altre informazioni sull'errore.

![distribuzione non riuscita](./media/resource-manager-common-deployment-errors/deployment-failed.png)

Viene visualizzato il messaggio di errore e i codici di errore. Si noti che sono presenti due codici di errore. Il primo codice di errore, **DeploymentFailed**, è un errore generale che non fornisce i dettagli necessari per risolvere l'errore. Il secondo codice di errore, **StorageAccountNotFound**, contiene i dettagli necessari. 

![dettagli dell'errore](./media/resource-manager-common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Abilitazione della registrazione di debug

In alcuni casi sono necessarie maggiori informazioni sulla richiesta e sulla risposta per conoscere la causa dell'errore. Durante la distribuzione è possibile richiedere la registrazione di informazioni aggiuntive durante la distribuzione. 

### <a name="powershell"></a>PowerShell

In PowerShell impostare il parametro **DeploymentDebugLogLevel** su All, ResponseContent o RequestContent.

```powershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

Esaminare il contenuto della richiesta con il cmdlet seguente:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

In alternativa, esaminare il contenuto della risposta con:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

Queste informazioni consentono di stabilire se nel modello sia impostato un valore errato.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Attualmente, l'interfaccia della riga di comando di Azure non supporta l'attivazione della registrazione di debug, ma è possibile recuperare la registrazione di debug.

Esaminare le operazioni di distribuzione con il comando seguente:

```azurecli
az group deployment operation list \
  --resource-group examplegroup \
  --name exampledeployment
```

Esaminare il contenuto della richiesta con il comando seguente:

```azurecli
az group deployment operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

Esaminare il contenuto della risposta con il comando seguente:

```azurecli
az group deployment operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>Modello annidato

Per registrare le informazioni di debug relative a un modello nidificato, usare l'elemento **debugSetting**.

```json
{
    "apiVersion": "2016-09-01",
    "name": "nestedTemplate",
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "templateLink": {
            "uri": "{template-uri}",
            "contentVersion": "1.0.0.0"
        },
        "debugSetting": {
           "detailLevel": "requestContent, responseContent"
        }
    }
}
```

## <a name="create-a-troubleshooting-template"></a>Creare un modello per la risoluzione dei problemi

Talvolta il modo più semplice per risolvere i problemi del modello è testarne alcune parti. A tale scopo, è possibile creare un modello semplificato che consente di concentrarsi sulla parte da cui si ritiene abbia origine l'errore. Si supponga, ad esempio, di ricevere un errore quando si fa riferimento a una risorsa. Anziché gestire un intero modello, crearne uno che restituisca la parte potenzialmente problematica. Ciò può aiutare a capire se i parametri trasmessi siano corretti, a usare correttamente le funzioni del modello e a recuperare le risorse desiderate.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
        "type": "string"
    },
    "storageResourceGroup": {
        "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
        "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
        "type" : "object"
    }
  }
}
```

In alternativa, si supponga di incontrare errori di distribuzione presumibilmente correlati a dipendenze impostate in modo errato. Testare il modello suddividendolo in modelli semplificati. Creare innanzitutto un modello che distribuisce una sola risorsa (ad esempio SQL Server). Quando si è certi che la risorsa sia definita correttamente, aggiungere una risorsa che dipende da essa (ad esempio un database SQL). Una volta definite correttamente queste due risorse, aggiungere altre risorse che dipendono da esse (ad esempio criteri di controllo). Tra una distribuzione di test e l'altra, eliminare il gruppo di risorse per assicurarsi di testare le dipendenze in modo adeguato.


## <a name="next-steps"></a>Passaggi successivi

* Per esaminare un'esercitazione sulla risoluzione dei problemi, [vedere Esercitazione: Risolvere i problemi delle distribuzioni dei modelli di Resource Manager](./resource-manager-tutorial-troubleshoot.md)
* Per altre informazioni sulle azioni di controllo, vedere [Operazioni di controllo con Resource Manager](resource-group-audit.md).
* Per altre informazioni sulle azioni che consentono di determinare gli errori di distribuzione, vedere [Visualizzare le operazioni di distribuzione con il portale di Azure](resource-manager-deployment-operations.md).
