---
title: Dettagli della struttura delle definizioni dei criteri
description: Descrizione di come la definizione dei criteri delle risorse viene usata da Criteri di Azure per stabilire le convenzioni per le risorse all'interno dell'organizzazione grazie alla definizione di quando i criteri vengono applicati e dell'azione da eseguire.
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: d7e264bda62753693cdd2333625313cf213f142a
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71145575"
---
# <a name="azure-policy-definition-structure"></a>Struttura delle definizioni di criteri di Azure

Le definizioni dei criteri delle risorse vengono usati da Criteri di Azure per stabilire le convenzioni delle risorse. Ogni definizione descrive la conformità della risorsa e le azioni da intraprendere quando una risorsa non è conforme.
Definendo le convenzioni, è possibile controllare i costi e gestire più facilmente le risorse. È ad esempio possibile specificare che vengano consentiti solo determinati tipi di macchine virtuali. In alternativa, è possibile richiedere che tutte le risorse abbiano un tag specifico. I criteri vengono ereditati da tutte le risorse figlio. Se un criterio viene applicato a un gruppo di risorse, è applicabile a tutte le risorse in tale gruppo.

Lo schema usato dai Criteri di Azure è reperibile qui: [https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json](https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json)

Per creare una definizione di criterio è possibile usare JSON. La definizione dei criteri contiene gli elementi per:

- mode
- parameters
- nome visualizzato
- description
- regola dei criteri
  - valutazione logica
  - effetto

Ad esempio, la notazione JSON seguente illustra un criterio che limita i punti in cui vengono distribuite le risorse:

```json
{
    "properties": {
        "mode": "all",
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                },
                "defaultValue": [ "westus2" ]
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

Tutti gli esempi di criteri di Azure sono disponibili in [esempi di criteri di Azure](../samples/index.md).

## <a name="mode"></a>Modalità

La **modalità** viene configurata in base al fatto che i criteri siano destinati a una proprietà Azure Resource Manager o a una proprietà del provider di risorse.

### <a name="resource-manager-modes"></a>Modalità Gestione risorse

Il parametro **mode** (modalità) determina quali tipi di risorse verranno valutate per l'assegnazione dei criteri. Le modalità supportate sono:

- `all`: vengono valutati i gruppi di risorse e tutti i tipi di risorse
- `indexed`: vengono valutati solo i tipi di risorse che supportano tag e il percorso

Nella maggior parte dei casi, è consigliabile impostare il parametro **mode** su `all`. Tutte le definizioni di criteri create tramite il portale usano la modalità `all`. Se si usa PowerShell o l'interfaccia della riga di comando di Azure è necessario specificare il parametro **mode** manualmente. Se la definizione dei criteri non include un valore **mode**, assume il valore predefinito `all` in Azure PowerShell e `null` nell'interfaccia della riga di comando di Azure. Un valore mode `null` equivale all'utilizzo di `indexed` per supportare la compatibilità con le versioni precedenti.

`indexed` deve essere usato durante la creazione di criteri che applicano tag o percorsi. Sebbene non sia necessario, evita che le risorse che non supportano tag e percorsi vengano visualizzate come non conformi nei risultati sulla conformità. L'eccezione è rappresentata dai **gruppi di risorse**. Per i criteri che applicano percorsi o tag a un gruppo di risorse, impostare il parametro **mode** su `all` e specificare una destinazione specifica per il tipo `Microsoft.Resources/subscriptions/resourceGroups`. Per un esempio, vedere [Applicare tag di gruppi di risorse](../samples/enforce-tag-rg.md). Per un elenco di risorse che supportano i tag, vedere [supporto dei tag per le risorse di Azure](../../../azure-resource-manager/tag-support.md).

### <a name="resource-provider-modes"></a>Modalità del provider di risorse

L'unica modalità del provider di risorse supportata `Microsoft.ContainerService.Data` attualmente è la gestione delle regole del controller di ammissione nel [servizio Azure Kubernetes](../../../aks/intro-kubernetes.md).

> [!NOTE]
> [Criteri di Azure per Kubernetes](rego-for-aks.md) è in anteprima pubblica e supporta solo le definizioni di criteri predefinite.

## <a name="parameters"></a>Parametri

I parametri consentono di semplificare la gestione dei criteri, riducendone il numero di definizioni. I parametri possono essere paragonati ai campi di un modulo: `name`, `address`, `city`, `state`. Questi parametri rimangono sempre invariati, ma i loro valori cambiano a seconda dei dati immessi durante la compilazione del modulo da parte dei singoli utenti.
I parametri funzionano nello stesso modo durante la creazione di criteri. L'inclusione dei parametri in una definizione dei criteri consente di riutilizzare i singoli criteri in vari scenari mediante l'uso di valori diversi.

> [!NOTE]
> I parametri possono essere aggiunti a una definizione esistente e assegnata. Il nuovo parametro deve includere la proprietà **defaultValue**. In questo modo le assegnazioni esistenti dei criteri o dell'iniziativa non possono essere rese indirettamente non valide.

### <a name="parameter-properties"></a>Proprietà parametro

Un parametro presenta le proprietà seguenti, usate nella definizione di criteri:

- **name**: nome del parametro. Usato dalla funzione di distribuzione `parameters` all'interno della regola dei criteri. Per altre informazioni, vedere [Usare un valore di parametro](#using-a-parameter-value).
- `type`: Determina se il parametro è una **stringa**, una **matrice**, un **oggetto**, un valore **booleano**, un Integer, un **valore** **float**o **DateTime**.
- `metadata`: definisce le sottoproprietà usate principalmente dal portale di Azure per visualizzare informazioni di tipo descrittivo:
  - `description`: la spiegazione di ciò per cui viene usato il parametro. Può essere usata per fornire esempi di valori accettabili.
  - `displayName`: il nome descrittivo visualizzato per il parametro nel portale.
  - `strongType`: (facoltativa) usata quando si assegna la definizione di criteri tramite portale. Fornisce un elenco con riconoscimento del contesto. Per altre informazioni, vedere [strongType](#strongtype).
  - `assignPermissions`: Opzionale Impostare su _true_ per avere portale di Azure creare assegnazioni di ruolo durante l'assegnazione dei criteri. Questa proprietà è utile nel caso in cui si desideri assegnare autorizzazioni al di fuori dell'ambito di assegnazione. È disponibile un'assegnazione di ruolo per ogni definizione di ruolo nel criterio (o per definizione di ruolo in tutti i criteri dell'iniziativa). Il valore del parametro deve essere una risorsa o un ambito valido.
- `defaultValue`: (facoltativa) imposta il valore del parametro in un'assegnazione se non viene specificato alcun valore.
  Obbligatoria quando si aggiorna una definizione di criteri esistente già assegnata.
- `allowedValues`: Opzionale Fornisce una matrice di valori accettati dal parametro durante l'assegnazione.

Ad esempio, è possibile definire una definizione di criteri per limitare i percorsi in cui le risorse possono essere distribuite. Un parametro per questa definizione di criteri potrebbe essere **allowedLocations**. Questo parametro è stato usato da ogni assegnazione della definizione di criteri per limitare i valori accettati. L'uso di **strongType** offre un'esperienza migliorata nel completamento dell'assegnazione tramite portale:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="using-a-parameter-value"></a>Usare un valore di parametro

Nella regola dei criteri fare riferimento ai parametri con la sintassi della funzione del valore di distribuzione `parameters` seguente:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Questo esempio fa riferimento al parametro **allowedLocations** illustrato nella sezione relativa alle [proprietà dei parametri](#parameter-properties).

### <a name="strongtype"></a>strongType

Nella proprietà `metadata` è possibile usare **strongType** per fornire un elenco di opzioni di selezione multipla nel portale di Azure. I valori consentiti per **strongType** attualmente includono:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`
- `omsWorkspace`
- `Microsoft.EventHub/Namespaces/EventHubs`
- `Microsoft.EventHub/Namespaces/EventHubs/AuthorizationRules`
- `Microsoft.EventHub/Namespaces/AuthorizationRules`
- `Microsoft.RecoveryServices/vaults`
- `Microsoft.RecoveryServices/vaults/backupPolicies`

## <a name="definition-location"></a>Località della definizione

Durante la creazione di iniziative o criteri è importante specificare la posizione della definizione. La posizione della definizione deve essere specificata come un gruppo di gestione o una sottoscrizione. Tale posizione determina l'ambito al quale la definizione delle iniziative o dei criteri può essere assegnata. Le risorse devono essere membri diretti o elementi figli all'interno della gerarchia della posizione della definizione da destinare all'assegnazione.

Se la posizione della definizione è:

- Una **sottoscrizione**: solo le risorse all'interno di tale sottoscrizione possono essere assegnate ai criteri.
- Un **gruppo di gestione**: solo le risorse all'interno dei gruppi di gestione figlio e delle sottoscrizioni figlio possono essere assegnate ai criteri. Se si intende applicare questa definizione di criteri a più sottoscrizioni, la posizione deve essere un gruppo di gestione contenente tali sottoscrizioni.

## <a name="display-name-and-description"></a>Nome visualizzato e descrizione

Usare **displayName** e **description** per identificare la definizione dei criteri e fornire il contesto d'uso. **displayName** ha una lunghezza massima di _128_ caratteri e **description** una lunghezza massima di _512_ caratteri.

## <a name="policy-rule"></a>Regola dei criteri

La regola dei criteri è costituita dai blocchi **If** e **Then** blocchi. Nel blocco **If**, si definiscono una o più condizioni che specificano quando i criteri vengono applicati. È possibile applicare gli operatori logici a queste condizioni per definire con precisione lo scenario di un criterio.

Nel blocco **Then** si definisce l'effetto che si verifica quando le condizioni **If** sono soddisfatte.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists | disabled"
    }
}
```

### <a name="logical-operators"></a>Operatori logici

Gli operatori logici supportati sono:

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

La sintassi **not** inverte il risultato della condizione. La sintassi **allOf** (simile all'operazione logica **And**) richiede che tutte le condizioni siano vere. La sintassi **anyOf** (simile all'operazione logica **Or**) richiede che una o più condizioni siano vere.

È possibile annidare gli operatori logici. L'esempio seguente illustra un'operazione **not** nidificata in un'operazione **allOf**.

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>Condizioni

Una condizione valuta se una funzione di accesso **field** o **value** soddisfa determinati criteri. Le condizioni supportate sono:

- `"equals": "value"`
- `"notEquals": "value"`
- `"like": "value"`
- `"notLike": "value"`
- `"match": "value"`
- `"matchInsensitively": "value"`
- `"notMatch": "value"`
- `"notMatchInsensitively": "value"`
- `"contains": "value"`
- `"notContains": "value"`
- `"in": ["value1","value2"]`
- `"notIn": ["value1","value2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "value"`
- `"lessOrEquals": "value"`
- `"greater": "value"`
- `"greaterOrEquals": "value"`
- `"exists": "bool"`

Quando si usano le condizioni **like** e **notLike**, è possibile inserire un carattere jolly `*` nel valore.
Il valore non deve contenere più di un carattere jolly `*`.

Quando si usano le condizioni **match** e **notMatch** , `#` fornire per trovare la corrispondenza `?` con una cifra, `.` per una lettera, per trovare la corrispondenza con qualsiasi carattere e qualsiasi altro carattere in modo che corrisponda al carattere effettivo.
**match** e **notMatch** fanno distinzione tra maiuscole e minuscole. Alternative senza distinzione tra maiuscole e minuscole sono disponibili in **matchInsensitively** e **notMatchInsensitively**. Ad esempio, vedere [Consentire modelli nome multipli](../samples/allow-multiple-name-patterns.md).

### <a name="fields"></a>Campi

Le condizioni vengono formate usando i campi. Un campo rappresenta le proprietà nel payload delle richieste di risorse e descrive lo stato della risorsa.

Sono supportati i seguenti campi:

- `name`
- `fullName`
  - Restituisce il nome completo della risorsa. Il nome completo di una risorsa è il nome della risorsa con anteposti gli eventuali nomi delle risorse padre (ad esempio "myServer/myDatabase").
- `kind`
- `type`
- `location`
  - Usare **global** per le risorse che sono indipendenti dalla posizione. Per un esempio, vedere [Esempi - Posizioni consentite](../samples/allowed-locations.md).
- `identity.type`
  - Restituisce il tipo di [identità gestita](../../../active-directory/managed-identities-azure-resources/overview.md) abilitata per la risorsa.
- `tags`
- `tags['<tagName>']`
  - Questa sintassi tra parentesi quadre supporta nomi di tag con segni di punteggiatura, ad esempio un trattino, punti o spazi.
  - Dove **\<tagName\>** è il nome del tag per il quale convalidare la condizione.
  - Esempi: `tags['Acct.CostCenter']` dove **Acct.CostCenter** è il nome del tag.
- `tags['''<tagName>''']`
  - Questa sintassi tra parentesi quadre supporta nomi di tag contenenti apostrofi eseguendo l'escape con esso in caratteri di escape con apostrofi doppi.
  - Dove **'\<tagName\>'** è il nome del tag per il quale convalidare la condizione.
  - Esempio: `tags['''My.Apostrophe.Tag''']` dove **'\<tagName\>'** è il nome del tag.
- alias delle proprietà; per un elenco, vedere [alias](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]` e `tags[tag.with.dots]` sono comunque modi accettabili per dichiarare un campo tags. Tuttavia, le espressioni preferibili sono quelle elencate in precedenza.

#### <a name="use-tags-with-parameters"></a>Usare tag con parametri

È possibile passare a un campo di tag un valore di parametro. Passare un parametro a un campo di tag aumenta la flessibilità della definizione dei criteri durante l'assegnazione dei criteri.

Nell'esempio seguente, `concat` viene usato per creare una ricerca nei campi di tag per il tag che ha come nome il valore del parametro **tagName**. Se il tag non esiste, viene usato l'effetto **Append** per aggiungere il tag usando il valore del tag con lo stesso nome impostato sul gruppo di risorse padre delle risorse controllate tramite la funzione di ricerca `resourcegroup()`.

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "append",
        "details": [{
            "field": "[concat('tags[', parameters('tagName'), ']')]",
            "value": "[resourcegroup().tags[parameters('tagName')]]"
        }]
    }
}
```

### <a name="value"></a>Value

Le condizioni possono essere formate anche usando **value**. **value** controlla le condizioni rispetto a [parametri](#parameters), [funzioni di modello supportate](#policy-functions) o valori letterali.
**value** è associato a qualsiasi [condizione](#conditions) supportata.

> [!WARNING]
> Se il risultato di una _funzione modello_ è un errore, la valutazione dei criteri ha esito negativo. Una valutazione non riuscita è un' **negazione**implicita. Per ulteriori informazioni, vedere la pagina relativa alla [prevenzione degli errori del modello](#avoiding-template-failures).

#### <a name="value-examples"></a>Esempi d'uso di value

Questa regola dei criteri usa **value** per confrontare il risultato della funzione `resourceGroup()` e la proprietà restituita **name** rispetto a una condizione **like** di `*netrg`. La regola respinge qualsiasi risorsa che non faccia parte di **type** `Microsoft.Network/*` in qualsiasi gruppo di risorse il cui nome termina con `*netrg`.

```json
{
    "if": {
        "allOf": [{
                "value": "[resourceGroup().name]",
                "like": "*netrg"
            },
            {
                "field": "type",
                "notLike": "Microsoft.Network/*"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Questa regola dei criteri usa **value** per verificare se il risultato di più funzioni annidate corrisponde a **equals** `true`. La regola respinge qualsiasi risorsa che non dispone di almeno tre tag.

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": true
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>Evitare gli errori di modelli

L'utilizzo delle _funzioni di modello_ in **value** consente numerose funzioni annidate complesse. Se il risultato di una _funzione modello_ è un errore, la valutazione dei criteri ha esito negativo. Una valutazione non riuscita è un' **negazione**implicita. Un esempio di un **valore** che ha esito negativo in determinati scenari:

```json
{
    "policyRule": {
        "if": {
            "value": "[substring(field('name'), 0, 3)]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

La regola dei criteri di esempio precedente USA [substring ()](../../../azure-resource-manager/resource-group-template-functions-string.md#substring) per confrontare i primi tre caratteri di **nome** in **ABC**. Se il **nome** è più breve di tre caratteri, `substring()` la funzione genera un errore. Questo errore fa sì che i criteri diventino un effetto **negato** .

Utilizzare invece la funzione [if ()](../../../azure-resource-manager/resource-group-template-functions-logical.md#if) per verificare se i primi tre caratteri del **nome** corrispondono a **ABC** senza consentire a un **nome** più breve di tre caratteri di generare un errore:

```json
{
    "policyRule": {
        "if": {
            "value": "[if(greaterOrEquals(length(field('name')), 3), substring(field('name'), 0, 3), 'not starting with abc')]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

Con la regola dei criteri modificata `if()` , controlla la lunghezza del **nome** prima di provare a `substring()` ottenere un valore con meno di tre caratteri. Se il **nome** è troppo breve, viene invece restituito il valore "Not Starting with ABC" e viene confrontato con **ABC**. Una risorsa con un nome breve che non inizia con **ABC** continua ad avere esito negativo sulla regola dei criteri, ma non causa più errori durante la valutazione.

### <a name="effect"></a>Effetto

Criteri di Azure supporta i seguenti tipi di effetto:

- **Deny**: genera un evento nel log attività e nega la richiesta
- **Audit**: genera un evento di avviso nel log attività, ma non nega la richiesta
- **Append**: aggiunge il set di campi definiti alla richiesta
- **AuditIfNotExists**: abilita il controllo se una risorsa non esiste
- **DeployIfNotExists**: distribuisce una risorsa se non esiste già
- **Disabled**: non valuta le risorse per garantire la conformità alla regola dei criteri
- **EnforceRegoPolicy**: configura il controller di ammissione dell'agente criteri aperto nel servizio Azure Kubernetes (anteprima)
- **Modifica**: aggiunge, aggiorna o rimuove i tag definiti da una risorsa

In caso di **aggiunta**, è necessario specificare questi dettagli:

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

Il valore può essere una stringa o un oggetto formato JSON.

**AuditIfNotExists** e **DeployIfNotExists** valutano l'esistenza di una risorsa correlata e applicano una regola. Se la risorsa non corrisponde alla regola, l'effetto viene implementato. È possibile ad esempio richiedere che venga distribuito un Network Watcher per tutte le reti virtuali. Per altre informazioni, vedere l'esempio [Audit if extension doesn't exist](../samples/audit-ext-not-exist.md) (Controllare se l'estensione non esiste).

L'effetto **DeployIfNotExists** richiede la proprietà **roleDefinitionId** nella parte dei **dettagli** della regola dei criteri. Per altre informazioni, vedere [Correzione: configurare la definizione dei criteri](../how-to/remediate-resources.md#configure-policy-definition).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

In modo analogo, la **modifica** richiede la proprietà **roleDefinitionId** nella parte relativa ai **Dettagli** della regola dei criteri per l' [attività di correzione](../how-to/remediate-resources.md). Per **modificare** è necessaria anche una matrice di **operazioni** per definire le azioni da intraprendere sui tag delle risorse.

Per informazioni complete su ogni effetto, ordine di valutazione, proprietà ed esempi, vedere [informazioni sugli effetti dei criteri di Azure](effects.md).

### <a name="policy-functions"></a>Funzioni dei criteri

Tutte le [funzioni di modello di gestione risorse](../../../azure-resource-manager/resource-group-template-functions.md) sono disponibili per l'uso in una regola dei criteri, eccetto le funzioni e le funzioni definite dall'utente seguenti:

- copyIndex()
- deployment()
- list*
- newGuid ()
- pickZones()
- providers()
- reference()
- resourceId()
- variables()

Le funzioni seguenti sono disponibili per l'uso in una regola dei criteri, ma sono diverse da quelle usate in un modello di Azure Resource Manager:

- addDays(dateTime, numberOfDaysToAdd)
  - **DateTime**: [Required] stringa stringa nel formato DateTime universale ISO 8601' aaaa-mm-ggThh: mm: SS. fffffffZ '
  - **numberOfDaysToAdd**: [Required] numero intero di giorni da aggiungere
- utcNow (): diversamente da un modello di Gestione risorse, può essere usato all'esterno di defaultValue.
  - Restituisce una stringa impostata sulla data e l'ora correnti nel formato DateTime ISO 8601 universale ' AAAA-MM-GGThh: mm: SS. fffffffZ '

Inoltre, la funzione `field` è disponibile per le regole dei criteri. `field` viene principalmente usata con **AuditIfNotExists** e **DeployIfNotExists** per fare riferimento ai campi sulla risorsa che viene valutata. Altre informazioni sono disponibili nell'esempio [DeployIfNotExists](effects.md#deployifnotexists-example).

#### <a name="policy-function-example"></a>Esempio di funzione dei criteri

Questo esempio di regola dei criteri usa la `resourceGroup` funzione risorsa per ottenere la proprietà **name**, in combinazione con la matrice `concat` e la funzione oggetto per creare una condizione `like` che fa in modo che il nome della risorsa inizi con il nome del gruppo di risorse.

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="aliases"></a>Alias

Usare gli alias delle proprietà per accedere alle proprietà specifiche per un tipo di risorsa. Gli alias consentono di limitare le condizioni o i valori consentiti per una proprietà su una risorsa. Ogni alias esegue il mapping ai percorsi in versioni di API diverse per un tipo di risorsa specificato. Durante la valutazione dei criteri, il motore dei criteri ottiene il percorso della proprietà per la versione API specificata.

L'elenco degli alias è in costante crescita. Per scoprire quali alias sono attualmente supportati da Criteri di Azure usare uno dei metodi seguenti:

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Compute -- Microsoft.Compute)
  (Get-AzPolicyAlias -NamespaceMatch 'compute').Aliases
  ```

- Interfaccia della riga di comando di Azure

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Compute -- Microsoft.Compute)
  az provider show --namespace Microsoft.Compute --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- API REST / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Informazioni sull'alias [*]

Molti degli alias disponibili hanno una versione che viene visualizzata come un nome "normale" e un'altra a cui viene aggiunto **[\*]** . Esempio:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

L'alias ' Normal ' rappresenta il campo come valore singolo. Questo campo è per gli scenari di confronto con corrispondenza esatta quando l'intero set di valori deve essere esattamente come definito, non più e non meno.

L' **alias\*[]** rende possibile il confronto con il valore di ogni elemento nella matrice e proprietà specifiche di ogni elemento. Questo approccio consente di confrontare le proprietà degli elementi per gli scenari "If None of", "if any of" o "if all of". Utilizzando **ipRules [\*]** , un esempio convaliderebbe che ogni _azione_ è _negata_, ma non preoccupante del numero di regole esistenti o del _valore_ IP. Questa regola di esempio controlla la presenza di eventuali corrispondenze di **ipRules [\*]. Value** in **10.0.4.1** e applica **effectType** solo se non trova almeno una corrispondenza:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "10.0.4.1"
            }
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

Per ulteriori informazioni, vedere [la pagina relativa alla\*valutazione dell'alias []](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

## <a name="initiatives"></a>Iniziative

Le iniziative consentono di raggruppare più definizioni di criteri correlati per semplificare le assegnazioni e la gestione e quindi di usare un gruppo come se fosse un unico elemento. È possibile ad esempio raggruppare tutte le definizioni di criteri relativi ai tag in una singola iniziativa. Invece di assegnare ciascun criterio singolarmente, si applica l'iniziativa.

L'esempio seguente illustra come creare un'iniziativa per la gestione di due tag: `costCenter` e `productName`. Usa due criteri predefiniti per applicare il valore di tag predefinito.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

## <a name="next-steps"></a>Passaggi successivi

- Esaminare gli esempi in [esempi di criteri di Azure](../samples/index.md).
- Leggere [Informazioni sugli effetti di Criteri](effects.md).
- Informazioni su come [creare criteri a livello di codice](../how-to/programmatically-create.md).
- Informazioni su come [ottenere i dati di conformità](../how-to/getting-compliance-data.md).
- Informazioni su come monitorare e [aggiornare le risorse non](../how-to/remediate-resources.md)conformi.
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).