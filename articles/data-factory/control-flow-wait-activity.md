---
title: Attività Wait in Azure Data Factory | Microsoft Docs
description: L'attività Wait sospende l'esecuzione della pipeline per il periodo specificato.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: 48a722979e61209a855dd1fec22fcdcc756ae1ce
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142427"
---
# <a name="execute-wait-activity-in-azure-data-factory"></a>Esegui attività di attesa in Azure Data Factory
Quando si usa un'attività Wait in una pipeline, la pipeline attende per il periodo di tempo specificato prima di proseguire con l'esecuzione delle attività successive. 

## <a name="syntax"></a>Sintassi

```json
{
    "name": "MyWaitActivity",
    "type": "Wait",
    "typeProperties": {
        "waitTimeInSeconds": 1
    }
}

```

## <a name="type-properties"></a>Proprietà del tipo

Proprietà | DESCRIZIONE | Valori consentiti | Obbligatoria
-------- | ----------- | -------------- | --------
name | Nome dell'attività `Wait`. | String | Yes
type | Deve essere impostata su **Wait**. | String | Yes
waitTimeInSeconds | Numero di secondi di attesa prima che la pipeline continui con l'elaborazione. | Integer | Yes

## <a name="example"></a>Esempio

> [!NOTE]
> Questa sezione include le definizioni JSON e i comandi di PowerShell di esempio per eseguire la pipeline. Per la procedura dettagliata di creazione di una pipeline di Data Factory tramite Azure PowerShell e le definizioni JSON, vedere [Esercitazione: Creare una data factory con Azure PowerShell](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-wait-activity"></a>Pipeline con attività Wait
In questo esempio, la pipeline include due attività: **Until** e **Wait**. L'attività Wait è configurata per attendere un secondo. La pipeline esegue l'attività Web in un ciclo con un periodo di attesa di un secondo tra un'esecuzione e l'altra. 

```json
{
    "name": "DoUntilPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression": {
                        "value": "@equals('Failed', coalesce(body('MyUnauthenticatedActivity')?.status, actions('MyUnauthenticatedActivity')?.status, 'null'))",
                        "type": "Expression"
                    },
                    "timeout": "00:00:01",
                    "activities": [
                        {
                            "name": "MyUnauthenticatedActivity",
                            "type": "WebActivity",
                            "typeProperties": {
                                "method": "get",
                                "url": "https://www.fake.com/",
                                "headers": {
                                    "Content-Type": "application/json"
                                }
                            },
                            "dependsOn": [
                                {
                                    "activity": "MyWaitActivity",
                                    "dependencyConditions": [ "Succeeded" ]
                                }
                            ]
                        },
                        {
                            "type": "Wait",
                            "typeProperties": {
                                "waitTimeInSeconds": 1
                            },
                            "name": "MyWaitActivity"
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ]
    }
}

```

## <a name="next-steps"></a>Passaggi successivi
Vedere altre attività del flusso di controllo supportate da Data Factory: 

- [Attività della condizione If](control-flow-if-condition-activity.md)
- [Attività ExecutePipeline](control-flow-execute-pipeline-activity.md)
- [Attività ForEach](control-flow-for-each-activity.md)
- [Attività Get Metadata](control-flow-get-metadata-activity.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività Web](control-flow-web-activity.md)
- [Attività Until](control-flow-until-activity.md)
