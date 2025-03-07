---
title: Attività di convalida in Azure Data Factory | Microsoft Docs
description: L'attività di convalida non continua l'esecuzione della pipeline fino a quando non convalida il set di dati collegato con determinati criteri specificati dall'utente.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 77fdab04e03429d135875cb2ef223e8c23d312a2
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141614"
---
# <a name="validation-activity-in-azure-data-factory"></a>Attività di convalida in Azure Data Factory
È possibile usare una convalida in una pipeline per garantire che la pipeline continui a essere eseguita solo dopo aver convalidato il riferimento al set di dati allegato, che soddisfa i criteri specificati oppure che sia stato raggiunto il timeout.


## <a name="syntax"></a>Sintassi

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>Proprietà del tipo

Proprietà | DESCRIZIONE | Valori consentiti | Obbligatoria
-------- | ----------- | -------------- | --------
name | Nome dell'attività' Validation ' | String | Sì |
type | Deve essere impostato su **Validation**. | String | Sì |
dataset | L'attività bloccherà l'esecuzione fino a quando non viene convalidato il riferimento al set di dati esistente e soddisfa i criteri specificati oppure è stato raggiunto il timeout. Il set di dati fornito deve supportare la proprietà "MinimumSize" o "ChildItems". | Riferimento a DataSet | Yes |
timeout | Specifica il timeout per l'attività da eseguire. Se non viene specificato alcun valore, il valore predefinito è 7 giorni ("7.00:00:00"). Il formato è d. hh: mm: SS | String | No |
sospensione | Ritardo in secondi tra i tentativi di convalida. Se non viene specificato alcun valore, il valore predefinito è 10 secondi. | Integer | No |
childItems | Controlla se la cartella contiene elementi figlio. Può essere impostato su-true: Verificare che la cartella esista e che includa elementi. Si blocca fino a quando almeno un elemento non è presente nella cartella o viene raggiunto il valore di timeout.-false: Verificare che la cartella esista e che sia vuota. Blocca fino a quando la cartella non è vuota o fino al raggiungimento del valore di timeout. Se non viene specificato alcun valore, l'attività si bloccherà fino a quando la cartella non esiste o fino al raggiungimento del timeout. | Boolean | No |
minimumSize | Dimensioni minime di un file in byte. Se non viene specificato alcun valore, il valore predefinito è 0 byte | Integer | No |


## <a name="next-steps"></a>Passaggi successivi
Vedere altre attività del flusso di controllo supportate da Data Factory:

- [Attività della condizione If](control-flow-if-condition-activity.md)
- [Attività ExecutePipeline](control-flow-execute-pipeline-activity.md)
- [Attività ForEach](control-flow-for-each-activity.md)
- [Attività Get Metadata](control-flow-get-metadata-activity.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività Web](control-flow-web-activity.md)
- [Attività Until](control-flow-until-activity.md)
