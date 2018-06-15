---
title: Condividere le visualizzazioni personalizzate di Azure Time Series Insights con gli URL con parametri | Microsoft Docs
description: Questo articolo descrive come sviluppare URL con parametri in Azure Time Series Insights, per poter condividere facilmente una visualizzazione di un cliente.
ms.service: time-series-insights
services: time-series-insights
author: MarkMcGeeAtAquent
ms.author: MarkMcGeeAtAquent
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: conceptual
ms.workload: big-data
ms.date: 11/21/2017
ms.openlocfilehash: 7ac3267412163f402813024f490c11579b8fdf8e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656755"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Condividere una visualizzazione personalizzata usando un URL con parametri

Per condividere una visualizzazione personalizzata nell'utilità di esplorazione di Time Series Insights, è possibile creare a livello di codice un URL con parametri della visualizzazione personalizzata.

L'utilità di esplorazione di Time Series Insights supporta i parametri di query dell'URL per specificare le visualizzazioni nell'esperienza direttamente dall'URL.  Ad esempio, usando solo l'URL, è possibile specificare un ambiente di destinazione, un predicato di ricerca e l'intervallo di tempo desiderato. Quando un utente fa clic sull'URL personalizzato, l'interfaccia fornisce un collegamento diretto a tale asset nel portale di Time Series Insights.  Vengono applicati i criteri di accesso ai dati. 

## <a name="environment-id"></a>ID di ambiente

Il parametro `environmentId=<guid>` specifica l'ID dell'ambiente di destinazione.  Si tratta di un componente del nome FQDN di accesso ai dati che è possibile trovare nell'angolo in alto a destra della panoramica dell'ambiente nel portale di Azure e  che include tutto ciò che precede `env.timeseries.azure.com`. Un parametro ID di ambiente di esempio è `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Tempo

È possibile specificare valori assoluti o relativi con un URL con parametri.

### <a name="absolute-time-values"></a>Valori assoluti

Per i valori assoluti, usare i parametri `from=<integer>` e `to=<integer>`. 

`from=<integer>` è un valore in millisecondi JavaScript indicante l'ora di inizio dell'intervallo di ricerca.

`to=<integer>` è un valore in millisecondi JavaScript indicante l'ora di fine dell'intervallo di ricerca. 

Per identificare i millisecondi JavaScript per una data, vedere [Epoch & Unix Timestamp Converter](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html) (Convertitore di timestamp Epoch e Unix).

### <a name="relative-time-values"></a>Valori relativi

Per un valore relativo, usare `relativeMillis=<value>`, dove *value* indica in millisecondi JavaScript i dati più recenti del back-end.

`&relativeMillis=3600000` ad esempio visualizza gli ultimi 60 minuti di dati.

I valori accettati corrispondono al menu **quick time** (tempi rapidi) dell'utilità di esplorazione di Time Series Insights e includono i seguenti:

- 1800000 (ultimi 30 minuti)
- 3600000 (ultimi 60 minuti)
- 10800000 (ultime 3 ore)
- 21600000 (ultime 6 ore)
- 43200000 (ultime 12 ore)
- 86400000 (ultime 24 ore)
- 604800000 (ultimi 7 giorni)
- 2592000000 (ultime 30 ore)

### <a name="optional-parameters"></a>Parametri facoltativi

Il parametro `timeSeriesDefinitions=<collection of term objects>` specifica i termini di una visualizzazione di Time Series Insights, dove:

- "name":"<string>"
  - Nome del *termine*.
- "splitBy":"<string>"
  - Nome della colonna *in base a cui dividere*.
- "measureName":"<string>"
  - Nome della colonna della *misura*.
- "predicate":"<string>"
  - Clausola *where* per i filtri lato server.
-  "useSum":"true"
  - Si tratta di un parametro facoltativo che specifica l'uso della somma per la misura.  Si noti che se "Events" (Eventi) è la misura selezionata, per impostazione predefinita è selezionato il parametro count (Conteggio).  Se "Events" (Eventi) non è selezionato, per impostazione predefinita è selezionato il parametro average (Media).  

Il parametro 'multiChartStack=<true/false>' consente l'impilamento nel grafico, mentre il parametro 'multiChartSameScale=<true/false>' abilita la stessa scala dell'asse Y in tutti i termini di un parametro facoltativo.  

- 'multiChartStack=false'
  - 'True' è abilitato per impostazione predefinita, quindi passare 'false' per impilare.
- 'multiChartStack=false&multiChartSameScale=true' 
  - È necessario abilitare l'impilamento per usare la stessa scala dell'asse Y in tutti i termini.  L'impilamento è 'false' per impostazione predefinita, quindi la funzionalità verrà abilitata passando 'true'.  
  
'timeBucketUnit=<Unit>&timeBucketSize=<integer>' consente di modificare il dispositivo di scorrimento dell'intervallo per fornire una vista più granulare o più semplice e aggregata del grafico.  
- 'timeBucketUnit=<Unit>&timeBucketSize=<integer>'
  - Unità = giorni, ore, minuti, secondi, millisecondi.  Scrivere sempre in lettere maiuscole l'unità.
  - Definire il numero di unità, passando l'intero desiderato per timeBucketSize.  Si noti che l'arco temporale è di 7 giorni.  
  
Il parametro 'timezoneOffset=<integer>' consente di impostare il fuso orario per il grafico da visualizzare come differenza dall'ora UTC.  
  - 'timezoneOffset=-<integer>'
    - L'intero viene sempre espresso in millisecondi.  
    - Si noti che questa funzionalità è leggermente diversa da quella abilitata nell'ambiente di esplorazione TSI, nel quale è possibile scegliere l'ora locale (ora del browser) o l'ora UTC.  
 
### <a name="examples"></a>Esempi

Ad esempio, per aggiungere le definizioni di serie temporale come parametro URL, è possibile usare il codice seguente:

```https
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Usando queste definizioni di serie temporali per 

- ID di ambiente
- ultimi 60 minuti di dati
- termini (F1PressureID, F2TempStation e F3VibrationPL) che includono i parametri facoltativi
 
è possibile costruire l'URL con parametri seguente per una visualizzazione:

```https
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Se si è usata l'utilità di esplorazione di Time Series Insights per creare la visualizzazione descritta dall'URL precedente, il risultato sarà simile al seguente:

![Termini dell'utilità di esplorazione di Time Series Insights](media/parameterized-url/url1.png)

La visualizzazione completa (incluso il grafico) sarà simile alla seguente:

![Visualizzazione grafico](media/parameterized-url/url2.png)

## <a name="next-steps"></a>Passaggi successivi
[Eseguire query sui dati con C#](time-series-insights-query-data-csharp.md)
