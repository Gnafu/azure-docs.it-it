---
title: Interfaccia della riga di comando di Azure Service Fabric - sfctl mesh code-package-log | Microsoft Docs
description: Descrive i comandi sfctl mesh code-package-log dell'interfaccia della riga di comando di Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: b1949f87dcdb1e3d9fe8e7fd08d8d8ba3b8203a0
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036440"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Consente di ottenere i log per il contenitore del pacchetto di codice specificato per la replica del servizio specificata.

## <a name="commands"></a>Comandi:

|Comando|DESCRIZIONE|
| --- | --- |
| get | Ottiene i log dal contenitore. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh code-package-log get
Ottiene i log dal contenitore.

Ottiene i log per il contenitore del pacchetto di codice specificato per la replica del servizio.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --app-name --application-name [obbligatorio] | Il nome dell'applicazione. |
| --code-package-name           [obbligatorio] | Nome del pacchetto di codice del servizio. |
| --replica-name                [obbligatorio] | Nome della replica di Service Fabric. |
| --service-name                [obbligatorio] | Il nome del servizio. |
| --tail | Numero di righe da visualizzare alla fine dei log. Il valore predefinito è 100. "all" per visualizzare i log completi. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |


## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).