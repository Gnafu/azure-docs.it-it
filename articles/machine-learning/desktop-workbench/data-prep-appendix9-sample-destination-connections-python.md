---
title: Destinazioni/output di esempio possibili con la preparazione dei dati di Microsoft Azure Machine Learning | Microsoft Docs
description: Questo documento fornisce un set di esempi di destinazioni/output di dati personalizzati con la preparazione dei dati di Microsoft Azure Machine Learning
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 3e827c56caa787ae9fc33119810eea0f268f0120
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958422"
---
# <a name="sample-of-destination-connections-python"></a>Esempio di connessioni di destinazione (Python) 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Prima di leggere questa appendice, leggere la [panoramica dell'estendibilità di Python](data-prep-python-extensibility-overview.md).


## <a name="write-to-excel"></a>Scrivere in Excel 


La scrittura in Excel richiede una raccolta aggiuntiva; l'aggiunta di nuove raccolte è descritta nella panoramica dell'estendibilità. `openpyxl` è la raccolta che è necessario aggiungere.

Prima della scrittura in Excel potrebbero essere necessarie alcune altre modifiche. Alcuni tipi di dati usati nella preparazione dei dati non sono supportati in alcuni formati di destinazione. Se ad esempio esistono oggetti "Error", questi non verranno serializzati correttamente in Excel. È quindi necessaria una trasformazione "Replace Error Values" (Sostituisci valori di errore) che rimuove gli errori da tutte le colonne prima di provare a scrivere in Excel.

Se tutto il lavoro precedente è stato completato, la riga seguente scrive la tabella dati in un unico foglio di un documento di Excel. Aggiungere una trasformazione Transform Dataflow - Script (Trasforma flusso di dati - Script) e quindi immettere il codice seguente in una sezione di espressione.


### <a name="on-windows"></a>In Windows 
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

### <a name="on-macosos-x"></a>In macOS/OS X ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```
