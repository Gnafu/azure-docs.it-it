---
title: Funzioni del modello di Azure Resource Manager - Stringa | Documentazione Microsoft
description: Informazioni sulle funzioni da usare in un modello di Azure Resource Manager per operare con le stringhe.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: tomfitz
ms.openlocfilehash: c30bb47f3f35663a6ffcfc0126758eb82c9dec4e
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194770"
---
# <a name="string-functions-for-azure-resource-manager-templates"></a>Funzioni di stringa nei modelli di Azure Resource Manager

Gestione risorse fornisce le funzioni seguenti per usare le stringhe:

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [concat](#concat)
* [contains](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [empty](#empty)
* [endsWith](#endswith)
* [first](#first)
* [format](#format)
* [guid](#guid)
* [indexOf](#indexof)
* [last](#last)
* [lastIndexOf](#lastindexof)
* [length](#length)
* [newGuid](#newguid)
* [padLeft](#padleft)
* [replace](#replace)
* [skip](#skip)
* [split](#split)
* [startsWith](#startswith)
* [string](#string)
* [substring](#substring)
* [take](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [trim](#trim)
* [uniqueString](#uniquestring)
* [uri](#uri)
* [uriComponent](#uricomponent)
* [uriComponentToString](#uricomponenttostring)
* [utcNow](#utcnow)

## <a name="base64"></a>base64

`base64(inputString)`

Restituisce la rappresentazione base64 della stringa di input.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| inputString |Sì |string |Il valore da restituire come rappresentazione base64. |

### <a name="return-value"></a>Valore restituito

Stringa contenente la rappresentazione base64.

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) seguente mostra come usare la funzione base64.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Value |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Converte una rappresentazione base64 in un oggetto JSON.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| base64Value |Sì |string |Rappresentazione base64 da convertire in un oggetto JSON. |

### <a name="return-value"></a>Valore restituito

Oggetto JSON.

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) seguente usa la funzione base64ToJson per convertire un valore base64:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Value |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Converte una rappresentazione base64 in una stringa.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| base64Value |Sì |string |Rappresentazione base64 da convertire in stringa. |

### <a name="return-value"></a>Valore restituito

Stringa del valore base64 convertito.

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) seguente usa la funzione base64ToString per convertire un valore base64:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Value |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="concat"></a>concat

`concat (arg1, arg2, arg3, ...)`

Combina più valori stringa e restituisce la stringa concatenata oppure combina più matrici e restituisce la matrice concatenata.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |Stringa o matrice |Il primo valore per la concatenazione. |
| Argomenti aggiuntivi |No |string |Altri valori in ordine sequenziale per la concatenazione. |

### <a name="return-value"></a>Valore restituito
Stringa o matrice di valori concatenati.

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) seguente illustra come combinare due valori stringa e restituisce una stringa concatenata.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "prefix": {
            "type": "string",
            "defaultValue": "prefix"
        }
    },
    "resources": [],
    "outputs": {
        "concatOutput": {
            "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]",
            "type" : "string"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Value |
| ---- | ---- | ----- |
| concatOutput | String | prefix-5yj4yjf5mbg72 |

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) seguente illustra come combinare due matrici.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        "firstArray": { 
            "type": "array", 
            "defaultValue": [ 
                "1-1", 
                "1-2", 
                "1-3" 
            ] 
        },
        "secondArray": {
            "type": "array", 
            "defaultValue": [ 
                "2-1", 
                "2-2",
                "2-3" 
            ] 
        }
    },
    "resources": [
    ],
    "outputs": {
        "return": {
            "type": "array",
            "value": "[concat(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Value |
| ---- | ---- | ----- |
| return | Array | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

## <a name="contains"></a>contiene

`contains (container, itemToFind)`

Verifica se una matrice contiene un valore, se un oggetto contiene una chiave o se una stringa contiene una sottostringa. Il confronto fra stringhe fa distinzione tra maiuscole e minuscole. Tuttavia, quando si testa se un oggetto contiene una chiave, il confronto non fa distinzione tra maiuscole e minuscole.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| Contenitore |Yes |matrice, oggetto o stringa |Valore che contiene il valore da trovare. |
| itemToFind |Sì |stringa o numero intero |Valore da trovare. |

### <a name="return-value"></a>Valore restituito

**True** se l'elemento viene individuato; in caso contrario, restituisce **False**.

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) seguente mostra come usare la funzione contains con tipi diversi:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "OneTwoThree"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringTrue": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'e')]"
        },
        "stringFalse": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'z')]"
        },
        "objectTrue": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'one')]"
        },
        "objectFalse": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'a')]"
        },
        "arrayTrue": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'three')]"
        },
        "arrayFalse": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'four')]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Value |
| ---- | ---- | ----- |
| stringTrue | Booleano | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Converte un valore in un URI di dati.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| stringToConvert |Yes |string |Valore da convertire in un URI di dati. |

### <a name="return-value"></a>Valore restituito

Stringa formattata come URI di dati.

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) seguente converte un valore in un URI di dati e converte un URI di dati in una stringa:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Valore |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Hello, World! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

Converte un valore formattato come URI di dati in una stringa.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Sì |string |Valore dell'URI di dati da convertire. |

### <a name="return-value"></a>Valore restituito

Stringa contenente il valore convertito.

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) seguente converte un valore in un URI di dati e converte un URI di dati in una stringa:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Valore |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Hello, World! |

## <a name="empty"></a>vuoto

`empty(itemToTest)`

Determina se una matrice, un oggetto o una stringa sono vuoti.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| itemToTest |Sì |matrice, oggetto o stringa |Valore per verificare se è vuoto. |

### <a name="return-value"></a>Valore restituito

**True** se il valore è vuoto; in caso contrario, restituisce **False**.

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) seguente controlla se una matrice, un oggetto e una stringa sono vuoti.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": []
        },
        "testObject": {
            "type": "object",
            "defaultValue": {}
        },
        "testString": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testArray'))]"
        },
        "objectEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testObject'))]"
        },
        "stringEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testString'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Valore |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Determina se una stringa termina con un valore. Il confronto non fa distinzione tra maiuscole e minuscole.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| stringToSearch |Sì |string |Valore che contiene l'elemento da cercare. |
| stringToFind |Yes |string |Valore da trovare. |

### <a name="return-value"></a>Valore restituito

**True** se l'ultimo carattere o i caratteri della stringa corrispondono al valore; in caso contrario, restituisce **False**.

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) seguente mostra come usare le funzioni startsWith ed endsWith:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Value |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="first"></a>first

`first(arg1)`

Restituisce il primo carattere della stringa o il primo elemento della matrice.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |array o string |Valore per recuperare il primo elemento o carattere. |

### <a name="return-value"></a>Valore restituito

Stringa del primo carattere o il tipo del primo elemento in una matrice (stringa, numero intero, matrice o oggetto).

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) seguente mostra come usare la prima funzione con una matrice e una stringa.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[first(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[first('One Two Three')]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Value |
| ---- | ---- | ----- |
| arrayOutput | String | one |
| stringOutput | String | O |

## <a name="format"></a>format

`format(formatString, arg1, arg2, ...)`

Crea una stringa formattata dai valori di input.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| formatString | Sì | string | Stringa in formato composito. |
| arg1 | Yes | stringa, Integer o booleano | Valore da includere nella stringa formattata. |
| Argomenti aggiuntivi | No | stringa, Integer o booleano | Valori aggiuntivi da includere nella stringa formattata. |

### <a name="remarks"></a>Note

Usare questa funzione per formattare una stringa nel modello. Usa le stesse opzioni di formattazione del metodo [System. String. Format](/dotnet/api/system.string.format) in .NET.

### <a name="examples"></a>Esempi

Il modello di esempio seguente mostra come usare la funzione Format.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "greeting": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "name": {
            "type": "string",
            "defaultValue": "User"
        },
        "numberToFormat": {
            "type": "int",
            "defaultValue": 8175133
        }
    },
    "resources": [
    ],
    "outputs": {
        "formatTest": {
            "type": "string",
            "value": "[format('{0}, {1}. Formatted number: {2:N0}', parameters('greeting'), parameters('name'), parameters('numberToFormat'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Value |
| ---- | ---- | ----- |
| formatTest | String | Hello, User. Numero formattato: 8.175.133 |

## <a name="guid"></a>GUID

`guid(baseString, ...)`

Crea un valore con il formato di un identificatore univoco globale in base ai valori specificati come parametri.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| baseString |Yes |string |Il valore usato nella funzione hash per creare il GUID. |
| parametri aggiuntivi in base alle esigenze |No |string |È possibile aggiungere tutte le stringhe necessarie per creare il valore che specifica il livello di univocità. |

### <a name="remarks"></a>Note

Questa funzione è utile quando è necessario creare un valore con il formato di un identificatore univoco globale. È possibile specificare i valori dei parametri che limitano l'ambito di univocità per il risultato. È possibile specificare se il nome è univoco nella sottoscrizione, nel gruppo di risorse o nella distribuzione.

Il valore restituito non è una stringa casuale, bensì il risultato di una funzione hash sui parametri. Il valore restituito è lungo 36 caratteri. Non è univoco a livello globale. Per creare un nuovo GUID che non si basa su tale valore hash dei parametri, usare la funzione [newGuid](#newguid) .

Gli esempi seguenti illustrano come usare guid per creare un valore univoco per livelli di uso comune.

Con ambito univoco nella sottoscrizione

```json
"[guid(subscription().subscriptionId)]"
```

Con ambito univoco nel gruppo di risorse

```json
"[guid(resourceGroup().id)]"
```

Con ambito univoco nella distribuzione per un gruppo di risorse

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>Valore restituito

Stringa contenente 36 caratteri nel formato di un identificatore univoco globale.

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) seguente restituisce risultati da guid:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "guidPerSubscription": {
            "value": "[guid(subscription().subscriptionId)]",
            "type": "string"
        },
        "guidPerResourceGroup": {
            "value": "[guid(resourceGroup().id)]",
            "type": "string"
        },
        "guidPerDeployment": {
            "value": "[guid(resourceGroup().id, deployment().name)]",
            "type": "string"
        }
    }
}
```

## <a name="indexof"></a>indexOf

`indexOf(stringToSearch, stringToFind)`

Restituisce la prima posizione di un valore all'interno di una stringa. Il confronto non fa distinzione tra maiuscole e minuscole.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| stringToSearch |Sì |string |Valore che contiene l'elemento da cercare. |
| stringToFind |Sì |string |Valore da trovare. |

### <a name="return-value"></a>Valore restituito

Numero intero che rappresenta la posizione dell'elemento da trovare. Il valore è in base zero. Se l'elemento non viene trovato, viene restituito-1.

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) seguente mostra come usare le funzioni indexOf e lastIndexOf:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Value |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

## <a name="last"></a>last

`last (arg1)`

Restituisce il primo carattere della stringa o l'ultimo elemento della matrice.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |array o string |Valore per recuperare l'ultimo elemento o carattere. |

### <a name="return-value"></a>Valore restituito

Stringa dell'ultimo carattere o il tipo dell'ultimo elemento in una matrice (stringa, numero intero, matrice o oggetto).

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) seguente mostra come usare l'ultima funzione con una matrice e una stringa.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[last(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[last('One Two Three')]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Value |
| ---- | ---- | ----- |
| arrayOutput | String | three |
| stringOutput | String | e |

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

Restituisce l'ultima posizione di un valore all'interno di una stringa. Il confronto non fa distinzione tra maiuscole e minuscole.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| stringToSearch |Sì |string |Valore che contiene l'elemento da cercare. |
| stringToFind |Sì |string |Valore da trovare. |

### <a name="return-value"></a>Valore restituito

Numero intero che rappresenta l'ultima posizione dell'elemento da trovare. Il valore è in base zero. Se l'elemento non viene trovato, viene restituito-1.

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) seguente mostra come usare le funzioni indexOf e lastIndexOf:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Value |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

## <a name="length"></a>length

`length(string)`

Restituisce il numero di caratteri in una stringa, elementi in una matrice o proprietà a livello di radice in un oggetto.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| arg1 |Sì |Array, String o Object |Matrice da usare per ottenere il numero di elementi, la stringa da usare per ottenere il numero di caratteri o l'oggetto da usare per ottenere il numero di proprietà a livello di radice. |

### <a name="return-value"></a>Valore restituito

Numero intero 

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) seguente mostra come usare la funzione length con una matrice e una stringa:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "stringToTest": {
            "type": "string",
            "defaultValue": "One Two Three"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {
                "propA": "one",
                "propB": "two",
                "propC": "three",
                "propD": {
                    "propD-1": "sub",
                    "propD-2": "sub"
                }
            }
        }
    },
    "resources": [],
    "outputs": {
        "arrayLength": {
            "type": "int",
            "value": "[length(parameters('arrayToTest'))]"
        },
        "stringLength": {
            "type": "int",
            "value": "[length(parameters('stringToTest'))]"
        },
        "objectLength": {
            "type": "int",
            "value": "[length(parameters('objectToTest'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Valore |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

## <a name="newguid"></a>newGuid

`newGuid()`

Restituisce un valore nel formato di un identificatore univoco globale. **Questa funzione può essere usata solo nel valore predefinito per un parametro.**

### <a name="remarks"></a>Note

Questa funzione può essere usata solo all'interno di un'espressione per il valore predefinito di un parametro. L'uso di questa funzione in qualsiasi altra posizione in un modello restituisce un errore. La funzione non è consentita in altre parti del modello perché restituisce un valore diverso ogni volta che viene chiamato. La distribuzione dello stesso modello con gli stessi parametri non produrrebbe in modo affidabile gli stessi risultati.

La funzione newGuid differisce dalla funzione [GUID](#guid) perché non accetta parametri. Quando si chiama GUID con lo stesso parametro, viene restituito ogni volta lo stesso identificatore. Utilizzare il GUID quando è necessario generare in modo affidabile lo stesso GUID per un ambiente specifico. Usare newGuid quando è necessario un identificatore diverso ogni volta, ad esempio la distribuzione di risorse in un ambiente di test.

Se si usa l' [opzione per ridistribuire una distribuzione riuscita in precedenza](resource-group-template-deploy-rest.md#redeploy-when-deployment-fails)e la distribuzione precedente include un parametro che usa newGuid, il parametro non viene rivalutato. Al contrario, il valore del parametro della distribuzione precedente viene riutilizzato automaticamente nella distribuzione di rollback.

In un ambiente di test, potrebbe essere necessario distribuire ripetutamente le risorse che risiedono solo per un breve periodo di tempo. Anziché costruire nomi univoci, è possibile usare newGuid con [uniqueString](#uniquestring) per creare nomi univoci.

Prestare attenzione nella ridistribuzione di un modello che si basa sulla funzione newGuid per un valore predefinito. Quando si esegue la ridistribuzione e non si fornisce un valore per il parametro, la funzione viene rivalutata. Se si desidera aggiornare una risorsa esistente anziché crearne una nuova, passare il valore del parametro dalla distribuzione precedente.

### <a name="return-value"></a>Valore restituito

Stringa contenente 36 caratteri nel formato di un identificatore univoco globale.

### <a name="examples"></a>Esempi

Il modello di esempio seguente mostra un parametro con un nuovo identificatore.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "guidValue": {
            "type": "string",
            "defaultValue": "[newGuid()]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "guidOutput": {
            "type": "string",
            "value": "[parameters('guidValue')]"
        }
    }
}
```

L'output dell'esempio precedente varia per ogni distribuzione, ma sarà simile a:

| NOME | Type | Value |
| ---- | ---- | ----- |
| guidOutput | string | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

Nell'esempio seguente viene usata la funzione newGuid per creare un nome univoco per un account di archiviazione. Questo modello potrebbe funzionare per l'ambiente di test in cui l'account di archiviazione esiste per un breve periodo di tempo e non viene ridistribuito.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "guidValue": {
            "type": "string",
            "defaultValue": "[newGuid()]"
        }
    },
    "variables": {
        "storageName": "[concat('storage', uniqueString(parameters('guidValue')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "West US",
            "apiVersion": "2018-07-01",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {}
        }
    ],
    "outputs": {
        "nameOutput": {
            "type": "string",
            "value": "[variables('storageName')]"
        }
    }
}
```

L'output dell'esempio precedente varia per ogni distribuzione, ma sarà simile a:

| NOME | Type | Valore |
| ---- | ---- | ----- |
| nameOutput | string | storagenziwvyru7uxie |


## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

Restituisce una stringa allineata a destra mediante l'aggiunta di caratteri a sinistra, fino a raggiungere la lunghezza totale specificata.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| valueToPad |Yes |Stringa o numero intero |Il valore per eseguire l'allineamento a destra. |
| totalLength |Sì |int |Numero totale di caratteri della stringa restituita. |
| paddingCharacter |No |Carattere singolo |Il carattere da utilizzare per la spaziatura interna a sinistra, fino a raggiungere la lunghezza totale. Il valore predefinito è uno spazio. |

Se la stringa originale è più lunga rispetto al numero di caratteri di riempimento, non vengono aggiunti caratteri.

### <a name="return-value"></a>Valore restituito

Stringa contenente come minimo il numero di caratteri specificati.

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) seguente mostra come il valore del parametro fornito dall'utente viene completato aggiungendo il carattere zero finché la stringa non raggiunge il numero totale di caratteri. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123"
        }
    },
    "resources": [],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[padLeft(parameters('testString'),10,'0')]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Value |
| ---- | ---- | ----- |
| stringOutput | String | 0000000123 |

## <a name="replace"></a>replace

`replace(originalString, oldString, newString)`

Restituisce una nuova stringa con tutte le istanze di una stringa sostituita con un'altra stringa.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| originalString |Sì |string |Valore che contiene tutte le istanze di una stringa sostituita con un'altra stringa. |
| oldString |Sì |string |Stringa da rimuovere dalla stringa originale. |
| newString |Sì |string |Stringa da aggiungere al posto della stringa rimossa. |

### <a name="return-value"></a>Valore restituito

Stringa con i caratteri sostituiti.

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) seguente illustra come rimuovere tutti i trattini dalla stringa fornita dall'utente e come sostituire parte della stringa con un'altra stringa.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123-123-1234"
        }
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'-', '')]"
        },
        "secondOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'1234', 'xxxx')]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Value |
| ---- | ---- | ----- |
| firstOutput | String | 1231231234 |
| secondOutput | String | 123-123-xxxx |

## <a name="skip"></a>ignora

`skip(originalValue, numberToSkip)`

Restituisce una stringa con tutti i caratteri dopo il numero specificato di caratteri o una matrice con tutti gli elementi dopo il numero specificato di elementi.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| originalValue |Sì |array o string |Stringa o matrice da usare per i valori da ignorare. |
| numberToSkip |Sì |int |Numero di elementi o caratteri da ignorare. Se il valore è minore o uguale a 0, vengono restituiti tutti gli elementi o i caratteri nel valore. Se è maggiore della lunghezza della matrice o della stringa, viene restituita una matrice o una stringa vuota. |

### <a name="return-value"></a>Valore restituito

Stringa o matrice.

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) seguente ignora il numero di elementi specificato nella matrice e il numero di caratteri specificato in una stringa.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToSkip": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToSkip": {
            "type": "int",
            "defaultValue": 4
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[skip(parameters('testArray'),parameters('elementsToSkip'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[skip(parameters('testString'),parameters('charactersToSkip'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Value |
| ---- | ---- | ----- |
| arrayOutput | Array | ["three"] |
| stringOutput | String | two three |

## <a name="split"></a>split

`split(inputString, delimiter)`

Restituisce una matrice di stringhe che contiene le sottostringhe della stringa di input delimitate dai delimitatori specificati.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| inputString |Sì |string |Stringa da dividere. |
| delimiter |Sì |Stringa o matrice di stringhe |Il delimitatore da usare per dividere la stringa. |

### <a name="return-value"></a>Valore restituito

Matrice di stringhe.

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) seguente mostra come suddividere la stringa di input usando una virgola, oppure una virgola o un punto e virgola.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstString": {
            "type": "string",
            "defaultValue": "one,two,three"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "one;two,three"
        }
    },
    "variables": {
        "delimiters": [ ",", ";" ]
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "array",
            "value": "[split(parameters('firstString'),',')]"
        },
        "secondOutput": {
            "type": "array",
            "value": "[split(parameters('secondString'),variables('delimiters'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Valore |
| ---- | ---- | ----- |
| firstOutput | Array | ["one", "two", "three"] |
| secondOutput | Array | ["one", "two", "three"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Determina se una stringa inizia con un valore. Il confronto non fa distinzione tra maiuscole e minuscole.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| stringToSearch |Sì |string |Valore che contiene l'elemento da cercare. |
| stringToFind |Sì |string |Valore da trovare. |

### <a name="return-value"></a>Valore restituito

**True** se il primo carattere o i caratteri della stringa corrispondono al valore; in caso contrario, restituisce **False**.

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) seguente mostra come usare le funzioni startsWith ed endsWith:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Value |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="string"></a>string

`string(valueToConvert)`

Converte il valore specificato in una stringa.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| valueToConvert |Sì | Any |Valore da convertire in stringa. È possibile convertire qualsiasi tipo di valore, inclusi gli oggetti e le matrici. |

### <a name="return-value"></a>Valore restituito

Stringa del valore convertito.

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) seguente mostra come convertire diversi tipi di valori di stringa:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testObject": {
            "type": "object",
            "defaultValue": {
                "valueA": 10,
                "valueB": "Example Text"
            }
        },
        "testArray": {
            "type": "array",
            "defaultValue": [
                "a",
                "b",
                "c"
            ]
        },
        "testInt": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "resources": [],
    "outputs": {
        "objectOutput": {
            "type": "string",
            "value": "[string(parameters('testObject'))]"
        },
        "arrayOutput": {
            "type": "string",
            "value": "[string(parameters('testArray'))]"
        },
        "intOutput": {
            "type": "string",
            "value": "[string(parameters('testInt'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Value |
| ---- | ---- | ----- |
| objectOutput | String | {"valueA":10,"valueB":"Example Text"} |
| arrayOutput | String | ["a","b","c"] |
| intOutput | String | 5 |

## <a name="substring"></a>substring

`substring(stringToParse, startIndex, length)`

Restituisce una sottostringa che inizia nella posizione del carattere specificato e contiene il numero di caratteri specificato.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| stringToParse |Sì |string |La stringa originale da cui estrarre la sottostringa. |
| startIndex |No |int |La posizione del carattere iniziale in base zero della sottostringa. |
| length |No |int |Il numero di caratteri della sottostringa. Deve fare riferimento a una posizione nella stringa. Deve essere uguale a o maggiore di zero. |

### <a name="return-value"></a>Valore restituito

La sottostringa. Oppure una stringa vuota se la lunghezza è pari a zero.

### <a name="remarks"></a>Note

La funzione ha esito negativo quando la sottostringa si estende oltre la fine della stringa o quando la lunghezza è minore di zero. L'esempio seguente ha esito negativo e visualizza l'errore "I parametri index e length devono fare riferimento a una posizione all'interno della stringa. Il parametro index: '0', lunghezza del parametro: '11', la lunghezza del parametro string: '10'.".

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) seguente estrae una sottostringa da un parametro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        }
    },
    "resources": [],
    "outputs": {
        "substringOutput": {
            "value": "[substring(parameters('testString'), 4, 3)]",
            "type": "string"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Value |
| ---- | ---- | ----- |
| substringOutput | String | two |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

Restituisce una stringa con il numero specificato di caratteri dall'inizio della stringa o una matrice con il numero specificato di elementi dall'inizio della matrice.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| originalValue |Sì |array o string |Stringa o matrice da cui prendere gli elementi. |
| numberToTake |Yes |int |Numero di elementi o caratteri da prendere. Se il valore è minore o uguale a 0, viene restituita una stringa o un matrice vuota. Se è maggiore della lunghezza della stringa o della matrice specificata, vengono restituiti tutti gli elementi della matrice o della stringa. |

### <a name="return-value"></a>Valore restituito

Stringa o matrice.

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) seguente prende il numero specificato di elementi dalla matrice e di caratteri dalla stringa.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToTake": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToTake": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[take(parameters('testArray'),parameters('elementsToTake'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[take(parameters('testString'),parameters('charactersToTake'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Value |
| ---- | ---- | ----- |
| arrayOutput | Array | ["one", "two"] |
| stringOutput | String | sì |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

Converte la stringa specificata in caratteri minuscoli.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| stringToChange |Yes |string |Il valore da convertire in lettere minuscole. |

### <a name="return-value"></a>Valore restituito

La stringa convertita in lettere minuscole.

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) seguente converte il valore del parametro in lettere maiuscole e minuscole.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Valore |
| ---- | ---- | ----- |
| toLowerOutput | String | one two three |
| toUpperOutput | String | ONE TWO THREE |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

Converte la stringa specificata in lettere maiuscole.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| stringToChange |Yes |string |Il valore da convertire in lettere maiuscole. |

### <a name="return-value"></a>Valore restituito

La stringa convertita in lettere maiuscole.

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) seguente converte il valore del parametro in lettere maiuscole e minuscole.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Value |
| ---- | ---- | ----- |
| toLowerOutput | String | one two three |
| toUpperOutput | String | ONE TWO THREE |

## <a name="trim"></a>trim

`trim (stringToTrim)`

Rimuove tutti i caratteri di spazi vuoti iniziali e finali dalla stringa specificata.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| stringToTrim |Yes |string |Il valore da tagliare. |

### <a name="return-value"></a>Valore restituito

La stringa senza spazi vuoti iniziali e finali.

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) seguente elimina i caratteri spazi vuoti dal parametro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "    one two three   "
        }
    },
    "resources": [],
    "outputs": {
        "return": {
            "type": "string",
            "value": "[trim(parameters('testString'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Valore |
| ---- | ---- | ----- |
| return | String | one two three |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

Crea una stringa hash deterministica in base ai valori forniti come parametri. 

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| baseString |Sì |string |Il valore usato nella funzione hash per creare una stringa univoca. |
| parametri aggiuntivi in base alle esigenze |No |string |È possibile aggiungere tutte le stringhe necessarie per creare il valore che specifica il livello di univocità. |

### <a name="remarks"></a>Note

Questa funzione è utile quando è necessario creare un nome univoco per una risorsa. È possibile specificare i valori dei parametri che limitano l'ambito di univocità per il risultato. È possibile specificare se il nome è univoco nella sottoscrizione, nel gruppo di risorse o nella distribuzione. 

Il valore restituito non è una stringa casuale, bensì il risultato di una funzione hash. Il valore restituito ha una lunghezza di 13 caratteri. Non è univoco a livello globale. È possibile combinare il valore con un prefisso dalla convenzione di denominazione scelta per creare un nome significativo. L'esempio seguente illustra il formato del valore restituito. Il valore effettivo varia in base ai parametri forniti.

    tcvhiyu5h2o5o

Gli esempi seguenti mostrano come usare uniqueString per creare un valore univoco per livelli di uso comune.

Con ambito univoco nella sottoscrizione

```json
"[uniqueString(subscription().subscriptionId)]"
```

Con ambito univoco nel gruppo di risorse

```json
"[uniqueString(resourceGroup().id)]"
```

Con ambito univoco nella distribuzione per un gruppo di risorse

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

Nell'esempio seguente viene illustrato come creare un nome univoco per un account di archiviazione in base al gruppo di risorse. All'interno del gruppo di risorse, il nome non è univoco se costruito nello stesso modo.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

Se è necessario creare un nuovo nome univoco ogni volta che si distribuisce un modello e non si intende aggiornare la risorsa, è possibile usare la funzione [UtcNow](#utcnow) con uniqueString. Questo approccio può essere utilizzato in un ambiente di testing. Per un esempio, vedere [UtcNow](#utcnow).

### <a name="return-value"></a>Valore restituito

Stringa contenente 13 caratteri.

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) seguente restituisce risultati da uniqueString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "uniqueRG": {
            "value": "[uniqueString(resourceGroup().id)]",
            "type" : "string"
        },
        "uniqueDeploy": {
            "value": "[uniqueString(resourceGroup().id, deployment().name)]",
            "type" : "string"
        }
    }
}
```

## <a name="uri"></a>URI

`uri (baseUri, relativeUri)`

Crea un URI assoluto combinando la baseUri e la stringa relativeUri.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| baseUri |Yes |string |La stringa URI di base. |
| relativeUri |Sì |string |La stringa URI relativa da aggiungere alla stringa di URI di base. |

Il valore per il parametro **baseUri** può includere un file specifico, ma solo il percorso di base viene usato per costruire l'URI. Ad esempio, se si trasmette `http://contoso.com/resources/azuredeploy.json` come parametro baseUri, viene restituito l'URI di base `http://contoso.com/resources/`.

### <a name="return-value"></a>Valore restituito

Stringa che rappresenta l'URI assoluto dei valori di base e relativi.

### <a name="examples"></a>Esempi

Nell'esempio seguente viene illustrato come costruire un collegamento a un modello annidato in base al valore del modello padre.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) seguente mostra come usare uri, uriComponent e uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Valore |
| ---- | ---- | ----- |
| uriOutput | String | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | String | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | String | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

Codifica un URI.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | DESCRIZIONE |
|:--- |:--- |:--- |:--- |
| stringToEncode |Sì |string |Valore da codificare. |

### <a name="return-value"></a>Valore restituito

Stringa del valore URI codificato.

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) seguente mostra come usare uri, uriComponent e uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Value |
| ---- | ---- | ----- |
| uriOutput | String | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | String | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | String | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

Restituisce una stringa di un valore URI codificato.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Sì |string |Valore URI codificato da convertire in stringa. |

### <a name="return-value"></a>Valore restituito

Stringa decodificata del valore URI codificato.

### <a name="examples"></a>Esempi

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) seguente mostra come usare uri, uriComponent e uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| NOME | Type | Valore |
| ---- | ---- | ----- |
| uriOutput | String | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | String | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | String | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Restituisce il valore DateTime corrente (UTC) nel formato specificato. Se non viene specificato alcun formato, viene usato il formato ISO 8601 (yyyyMMddTHHmmssZ). **Questa funzione può essere usata solo nel valore predefinito per un parametro.**

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| format |No |string |Valore URI codificato da convertire in stringa. Utilizzare [stringhe di formato standard](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [stringhe di formato personalizzate](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Note

Questa funzione può essere usata solo all'interno di un'espressione per il valore predefinito di un parametro. L'uso di questa funzione in qualsiasi altra posizione in un modello restituisce un errore. La funzione non è consentita in altre parti del modello perché restituisce un valore diverso ogni volta che viene chiamato. La distribuzione dello stesso modello con gli stessi parametri non produrrebbe in modo affidabile gli stessi risultati.

Se si usa l' [opzione per ridistribuire una distribuzione riuscita in precedenza](resource-group-template-deploy-rest.md#redeploy-when-deployment-fails)e la distribuzione precedente include un parametro che usa UtcNow, il parametro non viene rivalutato. Al contrario, il valore del parametro della distribuzione precedente viene riutilizzato automaticamente nella distribuzione di rollback.

Prestare attenzione nella ridistribuzione di un modello che si basa sulla funzione utcNow per un valore predefinito. Quando si esegue la ridistribuzione e non si fornisce un valore per il parametro, la funzione viene rivalutata. Se si desidera aggiornare una risorsa esistente anziché crearne una nuova, passare il valore del parametro dalla distribuzione precedente.

### <a name="return-value"></a>Valore restituito

Valore DateTime UTC corrente.

### <a name="examples"></a>Esempi

Il modello di esempio seguente mostra formati diversi per il valore DateTime.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcValue": {
            "type": "string",
            "defaultValue": "[utcNow()]"
        },
        "utcShortValue": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "utcCustomValue": {
            "type": "string",
            "defaultValue": "[utcNow('M d')]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "utcOutput": {
            "type": "string",
            "value": "[parameters('utcValue')]"
        },
        "utcShortOutput": {
            "type": "string",
            "value": "[parameters('utcShortValue')]"
        },
        "utcCustomOutput": {
            "type": "string",
            "value": "[parameters('utcCustomValue')]"
        }
    }
}
```

L'output dell'esempio precedente varia per ogni distribuzione, ma sarà simile a:

| Name | Type | Value |
| ---- | ---- | ----- |
| utcOutput | string | 20190305T175318Z |
| utcShortOutput | string | 03/05/2019 |
| utcCustomOutput | string | 3 5 |

Nell'esempio seguente viene illustrato come utilizzare un valore della funzione quando si imposta un valore di tag.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "rgName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "name": "[parameters('rgName')]",
            "location": "westeurope",
            "tags":{
                "createdDate": "[parameters('utcShort')]"
            },
            "properties":{}
        }
    ],
    "outputs": {
        "utcShort": {
            "type": "string",
            "value": "[parameters('utcShort')]"
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi
* Per una descrizione delle sezioni in un modello di Azure Resource Manager, vedere [Creazione di modelli di Azure Resource Manager](resource-group-authoring-templates.md).
* Per unire più modelli, vedere [Uso di modelli collegati con Azure Resource Manager](resource-group-linked-templates.md).
* Per eseguire un'iterazione di un numero di volte specificato durante la creazione di un tipo di risorsa, vedere [Creare più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md).
* Per informazioni su come distribuire il modello che è stato creato, vedere [Distribuire un'applicazione con un modello di Azure Resource Manager](resource-group-template-deploy.md).

