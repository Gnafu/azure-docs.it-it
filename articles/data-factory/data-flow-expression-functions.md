---
title: Funzioni di espressione nella funzionalità flusso di dati di mapping di Azure Data Factory
description: Informazioni sulle funzioni di espressione in mapping del flusso di dati.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: aa2bfd09bba863f416c75a3e4f5a9c2523b51541
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018538"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Espressioni di trasformazione dei dati nel flusso di dati di mapping 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="expression-functions"></a>Funzioni di espressione

In Data Factory utilizzare il linguaggio delle espressioni della funzionalità del flusso di dati di mapping per configurare le trasformazioni dei dati.

___
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Valore assoluto di un numero.
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
___
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcola un valore inverso del coseno* ``acos(1) -> 0.0``
___
### <code>add</code>
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Aggiunge una coppia di stringhe o numeri. Aggiunge una data a un numero di giorni. Aggiunge una durata a un timestamp. Aggiunge una matrice di tipo simile a un'altra. Uguale all'operatore +* ``add(10, 20) -> 30``
* ``10 + 20 -> 30``
* ``add('ice', 'cream') -> 'icecream'``
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``
* ``add(toDate('2012-12-12'), 3) -> toDate('2012-12-15')``
* ``toDate('2012-12-12') + 3 -> toDate('2012-12-15')``
* ``[10, 20] + [30, 40] -> [10, 20, 30, 40]``
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``
___
### <code>addDays</code>
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Aggiunge giorni a una data o a un timestamp. Uguale all'operatore + per date* ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')``
___
### <code>addMonths</code>
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral, [<i>&lt;value3&gt;</i> : string]) => datetime</b></code><br/><br/>
Aggiungere mesi a una data o un timestamp. Facoltativamente, è possibile passare un fuso orario* ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')``
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> toTimestamp('2016-08-31 10:10:10')``
___
### <code>and</code>
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Operatore AND logico. Uguale a & &* ``and(true, false) -> false``
* ``true && false -> false``
___
### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcola un valore del seno inverso* ``asin(0) -> 0.0``
___
### <code>atan</code>
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcola un valore tangente inverso* ``atan(0) -> 0.0``
___
### <code>atan2</code>
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Restituisce l'angolo, espresso in radianti, tra l'asse x positivo di un piano e il punto fornito dalle coordinate* ``atan2(0, 0) -> 0.0``
___
### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Ottiene la media dei valori di una colonna.* ``avg(sales)``
___
### <code>avgIf</code>
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
In base a un criterio viene ottenuta la media dei valori di una colonna* ``avgIf(region == 'West', sales)``
___
### <code>byName</code>
<code><b>byName(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
Seleziona un valore di colonna in base al nome nel flusso. È possibile passare un nome di flusso facoltativo come secondo argomento. Se sono presenti più corrispondenze, viene restituita la prima corrispondenza. Se nessuna corrispondenza restituisce un valore NULL. Il valore restituito deve essere di tipo convertito da una delle funzioni di conversione dei tipi (TO_DATE, TO_STRING...).  I nomi di colonna noti in fase di progettazione devono essere risolti solo in base al nome. Gli input calcolati non sono supportati, ma è possibile usare le sostituzioni di parametro* ``toString(byName('parent'))``
* ``toLong(byName('income'))``
* ``toBoolean(byName('foster'))``
* ``toLong(byName($debtCol))``
* ``toString(byName('Bogus Column'))``
* ``toString(byName('Bogus Column', 'DeriveStream'))``
___
### <code>byPosition</code>
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
Seleziona un valore di colonna in base alla relativa posizione (basata su 1) nel flusso. Se la posizione è fuori limite, viene restituito un valore NULL. Il valore restituito deve essere di tipo convertito da una delle funzioni di conversione dei tipi (TO_DATE, TO_STRING...) Gli input calcolati non sono supportati, ma è possibile usare le sostituzioni di parametro* ``toString(byPosition(1))``
* ``toDecimal(byPosition(2), 10, 2)``
* ``toBoolean(byName(4))``
* ``toString(byName($colName))``
* ``toString(byPosition(1234))``
___
### <code>case</code>
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
In base alle condizioni alternative, applica un valore o l'altro. Se il numero di input è pari, l'altro viene impostato come NULL per l'ultima condizione.* ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``
* ``case(10 + 20 == 25, 'bojjus', 'do' < 'go', 'gunchus') -> 'gunchus'``
* ``isNull(case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus')) -> true``
* ``case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus', 'dumbo') -> 'dumbo'``
___
### <code>cbrt</code>
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcolare la radice del cubo di un numero* ``cbrt(8) -> 2.0``
___
### <code>ceil</code>
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Restituisce l'Integer più piccolo non inferiore al numero* ``ceil(-0.1) -> 0``
___
### <code>coalesce</code>
<code><b>coalesce(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Restituisce il primo valore not null da un set di input. Tutti gli input devono essere dello stesso tipo* ``coalesce(10, 20) -> 10``
* ``coalesce(toString(null), toString(null), 'dumbo', 'bo', 'go') -> 'dumbo'``
___
### <code>compare</code>
<code><b>compare(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => integer</b></code><br/><br/>
Confronta due valori dello stesso tipo. Restituisce un numero intero negativo se value1 < value2, 0 se value1 = = value2, valore positivo se value1 > value2* ``(compare(12, 24) < 1) -> true``
* ``(compare('dumbo', 'dum') > 0) -> true``
___
### <code>concat</code>
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Concatena un numero variabile di stringhe. Uguale all'operatore + con le stringhe * ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'``
* ``'dataflow' + 'is' + 'awesome' -> 'dataflowisawesome'``
* ``isNull('sql' + null) -> true``
___
### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Concatena un numero variabile di stringhe con un separatore. Il primo parametro è il separatore* ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'``
* ``isNull(concatWS(null, 'dataflow', 'is', 'awesome')) -> true``
* ``concatWS(' is ', 'dataflow', 'awesome') -> 'dataflow is awesome'``
___
### <code>cos</code>
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcola un valore coseno* ``cos(10) -> -0.8390715290764524``
___
### <code>cosh</code>
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcola un coseno iperbolico di un valore* ``cosh(0) -> 1.0``
___
### <code>count</code>
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
Ottiene il conteggio aggregato dei valori. Se si specifica la colonna o le colonne facoltative, i valori NULL nel conteggio verranno ignorati* ``count(custId)``
* ``count(custId, custName)``
* ``count()``
* ``count(iif(isNull(custId), 1, NULL))``
___
### <code>countDistinct</code>
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
Ottiene il conteggio aggregato di valori distinti di un set di colonne.* ``countDistinct(custId, custName)``
___
### <code>countIf</code>
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
In base a un criterio, ottiene il conteggio aggregato dei valori. Se la colonna facoltativa è specificata, ignora i valori NULL nel conteggio* ``countIf(state == 'CA' && commission < 10000, name)``
___
### <code>covariancePopulation</code>
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ottiene la covarianza della popolazione tra due colonne* ``covariancePopulation(sales, profit)``
___
### <code>covariancePopulationIf</code>
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
In base a un criterio, ottiene la covarianza della popolazione di due colonne* ``covariancePopulationIf(region == 'West', sales)``
___
### <code>covarianceSample</code>
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ottiene la covarianza di esempio di due colonne* ``covarianceSample(sales, profit)``
___
### <code>covarianceSampleIf</code>
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
In base a un criterio, ottiene la covarianza di esempio di due colonne* ``covarianceSampleIf(region == 'West', sales, profit)``
___
### <code>crc32</code>
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
Calcola l'hash CRC32 di un set di colonne di diversi tipi di dati primitivi, data una lunghezza in bit che può avere solo i valori 0(256), 224, 256, 384 e 512. Può essere usato per calcolare un'impronta digitale per una riga* ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L``
___
### <code>cumeDist</code>
<code><b>cumeDist() => integer</b></code><br/><br/>
La funzione CumeDist calcola la posizione di un valore rispetto a tutti i valori nella partizione. Il risultato è il numero di righe precedenti o uguali alla riga corrente nell'ordinamento della partizione diviso per il numero totale di righe nella partizione di finestra. Qualsiasi valore Tie nell'ordinamento restituirà la stessa posizione.
* ``cumeDist()``
___
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Ottiene la data corrente quando viene avviata l'esecuzione del processo. È possibile passare un fuso orario facoltativo nel formato 'GMT', 'PST', 'UTC', 'America/Cayman'. Per impostazione predefinita viene usato il fuso orario locale.
* ``currentDate() == toDate('2250-12-31') -> false``
* ``currentDate('PST')  == toDate('2250-12-31') -> false``
___
### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
Ottiene il timestamp corrente quando l'esecuzione del processo inizia con il fuso orario locale* ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false``
___
### <code>currentUTC</code>
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
Ottiene il timestamp corrente come UTC. È possibile passare un fuso orario facoltativo nel formato 'GMT', 'PST', 'UTC', 'America/Cayman'. Il valore predefinito è il fuso orario corrente* ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false``
* ``currentUTC() != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Ottiene il giorno del mese in base a una data* ``dayOfMonth(toDate('2018-06-08')) -> 8``
___
### <code>dayOfWeek</code>
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Ottiene il giorno della settimana in base a una data specificata. 1-domenica, 2-lunedì..., 7-sabato* ``dayOfWeek(toDate('2018-06-08')) -> 6``
___
### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Ottiene il giorno dell'anno in base a una data* ``dayOfYear(toDate('2016-04-09')) -> 100``
___
### <code>days</code>
<code><b>days(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Durata in millisecondi per il numero di giorni* ``days(2) -> 172800000L``
___
### <code>degrees</code>
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Converte i radianti in gradi* ``degrees(3.141592653589793) -> 180``
___
### <code>denseRank</code>
<code><b>denseRank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Calcola la classificazione di un valore in un gruppo di valori. Come risultato si ha uno più il numero di righe precedenti o uguali alla riga corrente nell'ordinamento della partizione. I valori non produrranno spazi nella sequenza. Il rango dense funziona anche quando i dati non sono ordinati e cercano modifiche nei valori* ``denseRank(salesQtr, salesAmt)``
___
### <code>divide</code>
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Divide coppie di numeri. Uguale all'operatore/* ``divide(20, 10) -> 2``
* ``20 / 10 -> 2``
___
### <code>endsWith</code>
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Controlla se la stringa termina con la stringa fornita* ``endsWith('dumbo', 'mbo') -> true``
___
### <code>equals</code>
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operatore equals di confronto. Uguale all'operatore = =* ``equals(12, 24) -> false``
* ``12 == 24 -> false``
* ``'bad' == 'bad' -> true``
* ``isNull('good' == toString(null)) -> true``
* ``isNull(null == null) -> true``
___
### <code>equalsIgnoreCase</code>
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
Operatore equals di confronto senza distinzione tra maiuscole e minuscole. Uguale all'operatore < = >* ``'abc'<=>'Abc' -> true``
* ``equalsIgnoreCase('abc', 'Abc') -> true``
___
### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
Calcola il fattoriale di un numero* ``factorial(5) -> 120``
___
### <code>false</code>
<code><b>false() => boolean</b></code><br/><br/>
Restituisce sempre un valore false. Utilizzare la sintassi della funzione (false ()) se è presente una colonna denominata ' false '* ``(10 + 20 > 30) -> false``
* ``(10 + 20 > 30) -> false()``
___
### <code>first</code>
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Ottiene il primo valore di un gruppo di colonne. Se il secondo parametro ignoreNulls viene omesso, viene presupposto false* ``first(sales)``
* ``first(sales, false)``
___
### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Restituisce il valore integer più grande non maggiore del numero* ``floor(-0.1) -> -1``
___
### <code>fromUTC</code>
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Esegue la conversione al timestamp dall'ora UTC. Facoltativamente, è possibile passare il fuso orario nel formato ' GMT ',' PST ',' UTC ',' America/Cayman '. Il valore predefinito è il fuso orario corrente* ``fromUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operatore greater di confronto. Uguale all'operatore >* ``greater(12, 24) -> false``
* ``('dumbo' > 'dum') -> true``
* ``(toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS') > toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``
___
### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operatore greater than o equal di confronto. Uguale all'operatore > =* ``greaterOrEqual(12, 12) -> true``
* ``('dumbo' >= 'dum') -> true``
___
### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Restituisce il valore più elevato tra l'elenco di valori come input ignorando i valori null. Restituisce null se tutti gli input sono null* ``greatest(10, 30, 15, 20) -> 30``
* ``greatest(10, toInteger(null), 20) -> 20``
* ``greatest(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2011-12-12')``
* ``greatest(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS'), toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')``
___
### <code>hasColumn</code>
<code><b>hasColumn(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => boolean</b></code><br/><br/>
Verifica la presenza di un valore di colonna in base al nome nel flusso. È possibile passare un nome di flusso facoltativo come secondo argomento.  I nomi di colonna noti in fase di progettazione devono essere risolti solo in base al nome. Gli input calcolati non sono supportati, ma è possibile usare le sostituzioni di parametro* ``hasColumn('parent')``
___
### <code>hour</code>
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Ottiene il valore dell'ora di un timestamp. È possibile passare un fuso orario facoltativo nel formato 'GMT', 'PST', 'UTC', 'America/Cayman'. Per impostazione predefinita viene usato il fuso orario locale.
* ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 12``
___
### <code>hours</code>
<code><b>hours(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Durata in millisecondi per il numero di ore* ``hours(2) -> 7200000L``
___
### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
In base alla condizione specifica, applica un valore o l'altro. Se other non è specificato, viene considerato NULL. Entrambi i valori devono essere compatibili (numeric, String...)* ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``
* ``iif(10 > 30, 'dumbo', 'gumbo') -> 'gumbo'``
* ``iif(month(toDate('2018-12-01')) == 12, 345.12, 102.67) -> 345.12``
___
### <code>iifNull</code>
<code><b>iifNull(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => any</b></code><br/><br/>
Verifica se il valore non è NULL e restituisce else. restituisce l'alternativa. Esegue il test di tutti gli input finché non trova il primo valore non null* ``iifNull(10, 20) -> 10``
* ``iifNull(null, 20, 40) -> 20``
* ``iifNull('bojjus', 'bo', 'dumbo') -> 'dumbo'``
___
### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
Verifica se un elemento è presente nella matrice* ``in([10, 20, 30], 10) -> true``
* ``in(['good', 'kid'], 'bad') -> false``
___
### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Converte la prima lettera di ogni parola in lettere maiuscole. Le parole vengono identificate come separate da spazi vuoti* ``initCap('cool iceCREAM') -> 'Cool Icecream'``
___
### <code>instr</code>
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
Trova la posizione (in base 1) della sottostringa all'interno di una stringa. Se non viene trovato, viene restituito 0.* ``instr('dumbo', 'mbo') -> 3``
* ``instr('microsoft', 'o') -> 5``
* ``instr('good', 'bad') -> 0``
___
### <code>isDelete</code>
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Controlla se la riga è contrassegnata per l'eliminazione. Per le trasformazioni che impiegano più di un flusso di input, è possibile passare l'indice (in base 1) del flusso. Il valore predefinito per l'indice del flusso è 1* ``isDelete()``
* ``isDelete(1)``
___
### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Controlla se la riga è contrassegnata come errore. Per le trasformazioni che impiegano più di un flusso di input, è possibile passare l'indice (in base 1) del flusso. Il valore predefinito per l'indice del flusso è 1* ``isError()``
* ``isError(1)``
___
### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Controlla se la riga è contrassegnata per essere ignorata. Per le trasformazioni che impiegano più di un flusso di input, è possibile passare l'indice (in base 1) del flusso. Il valore predefinito per l'indice del flusso è 1* ``isIgnore()``
* ``isIgnore(1)``
___
### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Controlla se la riga è contrassegnata per l'inserimento. Per le trasformazioni che impiegano più di un flusso di input, è possibile passare l'indice (in base 1) del flusso. Il valore predefinito per l'indice del flusso è 1* ``isInsert()``
* ``isInsert(1)``
___
### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Controlla se per la riga viene trovata una corrispondenza. Per le trasformazioni che impiegano più di un flusso di input, è possibile passare l'indice (in base 1) del flusso. Il valore predefinito per l'indice del flusso è 1* ``isMatch()``
* ``isMatch(1)``
___
### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Verifica se il valore è NULL.* ``isNull(NULL()) -> true``
* ``isNull('') -> false``
___
### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Controlla se la riga è contrassegnata per l'aggiornamento. Per le trasformazioni che impiegano più di un flusso di input, è possibile passare l'indice (in base 1) del flusso. Il valore predefinito per l'indice del flusso è 1* ``isUpdate()``
* ``isUpdate(1)``
___
### <code>isUpsert</code>
<code><b>isUpsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Controlla se la riga è contrassegnata per l'inserimento. Per le trasformazioni che impiegano più di un flusso di input, è possibile passare l'indice (in base 1) del flusso. Il valore predefinito per l'indice del flusso è 1* ``isUpsert()``
* ``isUpsert(1)``
___
### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Ottiene l'curtosi di una colonna* ``kurtosis(sales)``
___
### <code>kurtosisIf</code>
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
In base a un criterio, ottiene il curtosi di una colonna* ``kurtosisIf(region == 'West', sales)``
___
### <code>lag</code>
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Ottiene il valore del primo parametro valutato in n righe prima della riga corrente. Il secondo parametro è il numero di righe di cui eseguire la ricerca e il valore predefinito è 1. Se non sono presenti tutte le righe, viene restituito un valore null a meno che non venga specificato un valore predefinito* ``lag(amount, 2)``
* ``lag(amount, 2000, 100)``
___
### <code>last</code>
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Ottiene l'ultimo valore di un gruppo di colonne. Se il secondo parametro ignoreNulls viene omesso, viene presupposto false* ``last(sales)``
* ``last(sales, false)``
___
### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
Ottiene l'ultima data del mese in base a una data* ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')``
___
### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Ottiene il valore del primo parametro valutato in n righe dopo la riga corrente. Il secondo parametro è il numero di righe da ricercare e il valore predefinito è 1. Se non sono presenti tutte le righe, viene restituito un valore null a meno che non venga specificato un valore predefinito* ``lead(amount, 2)``
* ``lead(amount, 2000, 100)``
___
### <code>least</code>
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Operatore lesser than o equal di confronto. Uguale all'operatore < =* ``least(10, 30, 15, 20) -> 10``
* ``least(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2000-12-12')``
___
### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Estrae una sottostringa iniziale in corrispondenza dell'indice 1 con il numero di caratteri specificato. Uguale a substring (Str, 1, n)* ``left('bojjus', 2) -> 'bo'``
* ``left('bojjus', 20) -> 'bojjus'``
___
### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
Restituisce la lunghezza della stringa.* ``length('dumbo') -> 5``
___
### <code>lesser</code>
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operatore less di confronto. Uguale all'operatore <* ``lesser(12, 24) -> true``
* ``('abcd' < 'abc') -> false``
* ``(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') < toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``
___
### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operatore lesser than o equal di confronto. Uguale all'operatore < =* ``lesserOrEqual(12, 12) -> true``
* ``('dumbo' <= 'dum') -> false``
___
### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
Ottiene la distanza Levenshtein tra due stringhe* ``levenshtein('boys', 'girls') -> 4``
___
### <code>like</code>
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Il modello è una stringa che corrisponde letteralmente a una stringa. Le eccezioni sono i simboli speciali seguenti: _ corrisponde a un carattere qualsiasi nell'input (simile a. nelle espressioni regolari posix) % corrisponde a zero o più caratteri nell'input (simile a .* nelle espressioni regolari posix).
Il carattere di escape è ". Se un carattere di escape precede un simbolo speciale o un altro carattere di escape, per il carattere successivo viene stabilita una corrispondenza letterale. Non è possibile aggiungere caratteri di escape a qualsiasi altro carattere.
* ``like('icecream', 'ice%') -> true``
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Trova la posizione (in base 1) della sottostringa all'interno di una stringa a partire da una determinata posizione. Se si omette la posizione, viene considerato l'inizio della stringa. Se non viene trovato, viene restituito 0.* ``locate('mbo', 'dumbo') -> 3``
* ``locate('o', 'microsoft', 6) -> 7``
* ``locate('bad', 'good') -> 0``
___
### <code>log</code>
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
Calcola il valore del logaritmo. È possibile specificare una base facoltativa, altrimenti un numero di Eulero, se usato* ``log(100, 10) -> 2``
___
### <code>log10</code>
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcola il valore di log in base a 10 base* ``log10(100) -> 2``
___
### <code>lower</code>
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Minuscole una stringa* ``lower('GunChus') -> 'gunchus'``
___
### <code>lpad</code>
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Riempie a sinistra la stringa in base al riempimento specificato fino a raggiungere una determinata lunghezza. Se la stringa è uguale o maggiore della lunghezza, viene * ``lpad('dumbo', 10, '-') -> '-----dumbo'`` tagliata alla lunghezza 
* ``lpad('dumbo', 4, '-') -> 'dumb'`` 
* '' LPAD (' Dumbo ', 8,' < >')->' < ><dumbo'``
___
### <code>LTrim</code>
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Rimuove i caratteri iniziali dal lato sinistro di una stringa. Se il secondo parametro non è specificato, rimuove lo spazio vuoto. In caso contrario, viene ritagliato qualsiasi carattere specificato nel secondo parametro* ``ltrim('  dumbo  ') -> 'dumbo  '``
* ``ltrim('!--!du!mbo!', '-!') -> 'du!mbo!'``
___
### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Ottiene il valore massimo di una colonna.* ``max(sales)``
___
### <code>maxIf</code>
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
In base a un criterio, ottiene il valore massimo di una colonna* ``maxIf(region == 'West', sales)``
___
### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Calcola il digest MD5 di un set di colonne di diversi tipi di dati primitivi e restituisce una stringa esadecimale a 32 caratteri. Può essere usato per calcolare un'impronta digitale per una riga* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'``
___
### <code>mean</code>
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Ottiene la media dei valori di una colonna. Uguale a AVG* ``mean(sales)``
___
### <code>meanIf</code>
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
In base a un criterio, ottiene la media dei valori di una colonna. Uguale a avgIf* ``meanIf(region == 'West', sales)``
___
### <code>millisecond</code>
<code><b>millisecond(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Ottiene il valore dei millisecondi di una data. È possibile passare un fuso orario facoltativo nel formato 'GMT', 'PST', 'UTC', 'America/Cayman'. Per impostazione predefinita viene usato il fuso orario locale.
* ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
___
### <code>milliseconds</code>
<code><b>milliseconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Durata in millisecondi per il numero di millisecondi* ``seconds(2) -> 2L``
___
### <code>min</code>
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Ottiene il valore minimo di una colonna * ``min(sales)``
___
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
In base a un criterio, ottiene il valore minimo di una colonna* ``minIf(region == 'West', sales)``
___
### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Sottrae numeri. Sottrarre un numero di giorni da una data. Sottrarre durata da un timestamp. Sottrarre due timestamp per ottenere la differenza in millisecondi. Uguale all'operatore-* ``minus(20, 10) -> 10``
* ``20 - 10 -> 10``
* ``minus(toDate('2012-12-15'), 3) -> toDate('2012-12-12')``
* ``toDate('2012-12-15') - 3 -> toDate('2012-12-12')``
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``
* ``toTimestamp('2019-02-03 05:21:34.851', 'yyyy-MM-dd HH:mm:ss.SSS') - toTimestamp('2019-02-03 05:21:36.923', 'yyyy-MM-dd HH:mm:ss.SSS') -> -2072``
___
### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Ottiene il valore dei minuti di un timestamp. È possibile passare un fuso orario facoltativo nel formato 'GMT', 'PST', 'UTC', 'America/Cayman'. Per impostazione predefinita viene usato il fuso orario locale.
* ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 58``
___
### <code>minutes</code>
<code><b>minutes(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Durata in millisecondi per il numero di minuti* ``minutes(2) -> 120000L``
___
### <code>mod</code>
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Calcola il modulo di coppie di numeri. Uguale all'operatore%* ``mod(20, 8) -> 4``
* ``20 % 8 -> 4``
___
### <code>month</code>
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Ottiene il valore del mese di una data o un timestamp* ``month(toDate('2012-8-8')) -> 8``
___
### <code>monthsBetween</code>
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;roundoff&gt;</i> : boolean], [<i>&lt;time zone&gt;</i> : string]) => double</b></code><br/><br/>
Ottiene il numero di mesi tra due date. È possibile arrotondare il calcolo. È possibile passare un fuso orario facoltativo nel formato ' GMT ',' PST ',' UTC ',' America/Cayman '. Per impostazione predefinita viene usato il fuso orario locale.
* ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``
___
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Moltiplica coppie di numeri. Uguale all'operatore ** ``multiply(20, 10) -> 200``
* ``20 * 10 -> 200``
___
### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
La funzione NTile divide le righe per ogni partizione di finestra in `n` bucket compresi tra 1 e un valore massimo `n`. I valori dei bucket si differenzieranno per un massimo di 1. Se il numero di righe nella partizione non può essere diviso equamente in base al numero di bucket, i valori rimanenti verranno distribuiti uno per bucket, a partire dal primo bucket. La funzione NTile è utile per il calcolo di tertiles, quartili, decili e altre statistiche di riepilogo comuni. La funzione calcola due variabili durante l'inizializzazione: Alla dimensione di un bucket regolare verrà aggiunta una riga aggiuntiva. Entrambe le variabili si basano sulle dimensioni della partizione corrente. Durante il processo di calcolo, la funzione tiene traccia del numero di righe corrente, del numero di bucket corrente e del numero di righe in corrispondenza del quale il bucket cambierà (bucketThreshold). Quando il numero di righe corrente raggiunge la soglia del bucket, il valore del bucket viene incrementato di uno e la soglia viene aumentata in base alla dimensione del bucket (più un valore aggiuntivo, in caso di riempimento del bucket corrente).
* ``nTile()``
* ``nTile(numOfBuckets)``
___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Nega un numero. Converte i numeri positivi in negativo e viceversa* ``negate(13) -> -13``
___
### <code>nextSequence</code>
<code><b>nextSequence() => long</b></code><br/><br/>
Restituisce la sequenza univoca successiva. Il numero è consecutivo solo all'interno di una partizione ed è preceduto da partitionId* ``nextSequence() == 12313112 -> false``
___
### <code>normalize</code>
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
Normalizzare il valore stringa per separare i caratteri Unicode accentati* ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'``
___
### <code>not</code>
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
Operatore di negazione logica* ``not(true) -> false``
* ``not(10 == 20) -> true``
___
### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operatore not equals di confronto. Uguale a! = (operatore)* ``12 != 24 -> true``
* ``'bojjus' != 'bo' + 'jjus' -> false``
___
### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
Restituisce un valore NULL. Usare la sintassi della funzione (null()) se è presente una colonna denominata 'null'. Qualsiasi operazione utilizzata da comporterà un valore NULL* ``isNull('dumbo' + null) -> true``
* ``isNull(10 * null) -> true``
* ``isNull('') -> false``
* ``isNull(10 + 20) -> false``
* ``isNull(10/0) -> true``
___
### <code>or</code>
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Operatore OR logico. Uguale a | |* ``or(true, false) -> true``
* ``true || false -> true``
___
### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Calcola il modulo positivo di coppie di numeri.
* ``pmod(-20, 8) -> 4``
___
### <code>power</code>
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Eleva un numero alla potenza di un altro* ``power(10, 2) -> 100``
___
### <code>rank</code>
<code><b>rank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Calcola la classificazione di un valore in un gruppo di valori. Come risultato si ha uno più il numero di righe precedenti o uguali alla riga corrente nell'ordinamento della partizione. I valori produrranno spazi nella sequenza. Rango funziona anche quando i dati non sono ordinati e cerca le modifiche nei valori* ``rank(salesQtr, salesAmt)``
___
### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
Estrae una sottostringa corrispondente per un modello di espressione regolare specificato. L'ultimo parametro identifica il gruppo di corrispondenza e, se omesso, viene usato il valore predefinito 1. Usare '<regex>' (virgolette indietro) per trovare la corrispondenza con una stringa senza Escape* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``
___
### <code>regexMatch</code>
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
Verifica se la stringa corrisponde al modello di espressione regolare specificato. Usare '<regex>' (virgolette indietro) per trovare la corrispondenza con una stringa senza Escape* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``
___
### <code>regexReplace</code>
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Sostituire tutte le occorrenze di un modello Regex con un'altra sottostringa nella stringa specificata usare<regex>'' (virgolette indietro) per trovare la corrispondenza con una stringa senza Escape* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``
___
### <code>regexSplit</code>
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
Suddivide una stringa in base a un delimitatore in base all'espressione regolare e restituisce una matrice di stringhe* ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']``
* ``regexSplit('bojjusAgunchusBdumboC', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo', '']``
* ``(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[1]) -> 'bojjus'``
* ``isNull(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[20]) -> true``
___
### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, [<i>&lt;substring to replace&gt;</i> : string]) => string</b></code><br/><br/>
Sostituire tutte le occorrenze di una sottostringa con un'altra sottostringa nella stringa specificata. Se l'ultimo parametro viene omesso, il valore predefinito è una stringa vuota* ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``
* ``replace('doggie dog', 'dog', '') -> 'gie '``
* ``replace('doggie dog', 'dog') -> 'gie '``
___
### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Inverte una stringa* ``reverse('gunchus') -> 'suhcnug'``
___
### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Estrae una sottostringa finale con il numero di caratteri specificato. Uguale a substring (Str, LENGTH (STR)-n, n)* ``right('bojjus', 2) -> 'us'``
* ``right('bojjus', 20) -> 'bojjus'``
___
### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Controlla se la stringa corrisponde al modello Regex specificato* ``rlike('200.50', `(\d+).(\d+)`) -> true``
* ``rlike('bogus', `M[0-9]+.*`) -> false``
___
### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
Arrotonda un numero in base a una scala facoltativa e una modalità di arrotondamento facoltativa. Se la scala viene omessa, il valore predefinito è 0.  Se la modalità viene omessa, il valore predefinito è ROUND_HALF_UP (5). I valori per l'arrotondamento includono 1-ROUND_UP 2-ROUND_DOWN 3-ROUND_CEILING 4-ROUND_FLOOR 5-ROUND_HALF_UP 6-ROUND_HALF_DOWN 7-ROUND_HALF_EVEN 8-ROUND_UNNECESSARY* ``round(100.123) -> 100.0``
* ``round(2.5, 0) -> 3.0``
* ``round(5.3999999999999995, 2, 7) -> 5.40``
___
### <code>rowNumber</code>
<code><b>rowNumber() => integer</b></code><br/><br/>
Assegna una numerazione di righe sequenziali per le righe in una finestra che inizia con 1* ``rowNumber()``
___
### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Riempie a destra la stringa in base al riempimento specificato fino a raggiungere una determinata lunghezza. Se la stringa è uguale o maggiore della lunghezza, viene tagliata fino alla lunghezza * ``rpad('dumbo', 10, '-') -> 'dumbo-----'``RTRIM 
* ``rpad('dumbo', 4, '-') -> 'dumb'`` 
* ``rpad('dumbo', 8, '<>') -> 'dumbo<><'`` 
___
### <code>rtrim</code></code>
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Rimuove i caratteri finali dal lato destro di una stringa. Se il secondo parametro non è specificato, rimuove lo spazio vuoto. In caso contrario, viene ritagliato qualsiasi carattere specificato nel secondo parametro* ``rtrim('  dumbo  ') -> '  dumbo'``
* ``rtrim('!--!du!mbo!', '-!') -> '!--!du!mbo'``
___
### <code>second</code>
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Ottiene il valore dei secondi di una data. È possibile passare un fuso orario facoltativo nel formato 'GMT', 'PST', 'UTC', 'America/Cayman'. Per impostazione predefinita viene usato il fuso orario locale.
* ``second(toTimestamp('2009-07-30 12:58:59')) -> 59``
___
### <code>seconds</code>
<code><b>seconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Durata in millisecondi per il numero di secondi* ``seconds(2) -> 2000L``
___
### <code>sha1</code>
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Calcola il digest SHA-1 di un set di colonne di diversi tipi di dati primitivi e restituisce una stringa esadecimale a 40 caratteri. Può essere usato per calcolare un'impronta digitale per una riga* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'``
___
### <code>sha2</code>
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Calcola il digest SHA-2 del set di colonne di tipi di dati primitivi variabili in base a una lunghezza di bit che può essere solo di valori 0 (256), 224, 256, 384, 512. Può essere usato per calcolare un'impronta digitale per una riga* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'``
___
### <code>sin</code>
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcola un valore seno* ``sin(2) -> 0.9092974268256817``
___
### <code>sinh</code>
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcola un valore del seno iperbolico* ``sinh(0) -> 0.0``
___
### <code>skewness</code>
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Ottiene l'inclinazione di una colonna* ``skewness(sales)``
___
### <code>skewnessIf</code>
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
In base a un criterio, ottiene l'inclinazione di una colonna* ``skewnessIf(region == 'West', sales)``
___
### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
Estrae un subset di una matrice da una posizione. La posizione è in base 1. Se la lunghezza viene omessa, il valore predefinito è fine della stringa* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``
* ``slice([10, 20, 30, 40], 2)[1] -> 20``
* ``isNull(slice([10, 20, 30, 40], 2)[0]) -> true``
* ``isNull(slice([10, 20, 30, 40], 2)[20]) -> true``
* ``slice(['a', 'b', 'c', 'd'], 8) -> []``
___
### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Ottiene il codice SOUNDEX per la stringa.* ``soundex('genius') -> 'G520'``
___
### <code>split</code>
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
Suddivide una stringa in base a un delimitatore e restituisce una matrice di stringhe* ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']``
* ``split('bojjus,guchus,dumbo', '|') -> ['bojjus,guchus,dumbo']``
* ``split('bojjus, guchus, dumbo', ', ') -> ['bojjus', 'guchus', 'dumbo']``
* ``split('bojjus, guchus, dumbo', ', ')[1] -> 'bojjus'``
* ``isNull(split('bojjus, guchus, dumbo', ', ')[0]) -> true``
* ``isNull(split('bojjus, guchus, dumbo', ', ')[20]) -> true``
* ``split('bojjusguchusdumbo', ',') -> ['bojjusguchusdumbo']``
___
### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcola la radice quadrata di un numero* ``sqrt(9) -> 3``
___
### <code>startsWith</code>
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Verifica se la stringa inizia con la stringa fornita* ``startsWith('dumbo', 'du') -> true``
___
### <code>stddev</code>
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Ottiene la deviazione standard di una colonna* ``stdDev(sales)``
___
### <code>stddevIf</code>
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
In base a un criterio, ottiene la deviazione standard di una colonna* ``stddevIf(region == 'West', sales)``
___
### <code>stddevPopulation</code>
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Ottiene la deviazione standard della popolazione di una colonna* ``stddevPopulation(sales)``
___
### <code>stddevPopulationIf</code>
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
In base a un criterio, ottiene la deviazione standard della popolazione di una colonna* ``stddevPopulationIf(region == 'West', sales)``
___
### <code>stddevSample</code>
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Ottiene la deviazione standard di esempio di una colonna* ``stddevSample(sales)``
___
### <code>stddevSampleIf</code>
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
In base a un criterio, ottiene la deviazione standard di esempio di una colonna* ``stddevSampleIf(region == 'West', sales)``
___
### <code>subDays</code>
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Sottrarre i mesi da una data o un timestamp. Uguale all'operatore-per date* ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')``
___
### <code>subMonths</code>
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Sottrarre i mesi da una data o un timestamp* ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')``
___
### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
Estrae una sottostringa di una determinata lunghezza da una posizione. La posizione è in base 1. Se la lunghezza viene omessa, il valore predefinito è fine della stringa* ``substring('Cat in the hat', 5, 2) -> 'in'``
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``
* ``substring('Cat in the hat', 5) -> 'in the hat'``
* ``substring('Cat in the hat', 100, 100) -> ''``
___
### <code>sum</code>
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Ottiene la somma aggregata di una colonna numerica* ``sum(col)``
___
### <code>sumDistinct</code>
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Ottiene la somma aggregata di valori distinti di una colonna numerica* ``sumDistinct(col)``
___
### <code>sumDistinctIf</code>
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
In base a criteri, ottiene la somma aggregata di una colonna numerica. La condizione può essere basata su qualsiasi colonna* ``sumDistinctIf(state == 'CA' && commission < 10000, sales)``
* ``sumDistinctIf(true, sales)``
___
### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
In base a criteri, ottiene la somma aggregata di una colonna numerica. La condizione può essere basata su qualsiasi colonna* ``sumIf(state == 'CA' && commission < 10000, sales)``
* ``sumIf(true, sales)``
___
### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcola un valore tangente* ``tan(0) -> 0.0``
___
### <code>tanh</code>
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcola un valore di tangente iperbolico* ``tanh(0) -> 0.0``
___
### <code>toBinary</code>
<code><b>toBinary(<i>&lt;value1&gt;</i> : any) => binary</b></code><br/><br/>
Converte qualsiasi stringa numerica/data/timestamp/stringa in una rappresentazione binaria* ``toBinary(3) -> [0x11]``
___
### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
Converte un valore di (' T',' true ',' y ',' Yes ',' 1') in true e (' f ',' false ',' n'',' No ',' 0') in false e NULL per qualsiasi altro valore* ``toBoolean('true') -> true``
* ``toBoolean('n') -> false``
* ``isNull(toBoolean('truthy')) -> true``
___
### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
Converte la stringa di data di input in date utilizzando un formato di data di input facoltativo. Vedere SimpleDateFormat di Java per i formati disponibili. Se il formato della data di input viene omesso, il formato predefinito è aaaa-[M] M-[d] d. Formati accettati: [aaaa, aaaa-[M] M, aaaa-[M] M-[d] d, aaaa-[M] M-[d] dT *]* ``toDate('2012-8-18') -> toDate('2012-08-18')``
* ``toDate('12/18/2012', 'MM/dd/yyyy') -> toDate('2012-12-18')``
___
### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
Converte un valore numerico o stringa in un valore decimale. Se precisione e scalabilità non sono specificate, per la conversione viene usato il valore predefinito (10,2). Per la conversione è possibile usare un formato decimale Java facoltativo. Un formato facoltativo per le impostazioni locali sotto forma di linguaggio BCP47, ad esempio en-US, de, zh-CN* ``toDecimal(123.45) -> 123.45``
* ``toDecimal('123.45', 8, 4) -> 123.4500``
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``
* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``
___
### <code>toDouble</code>
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
Converte un valore numerico o stringa in un valore double. Per la conversione è possibile usare un formato decimale Java facoltativo. Un formato facoltativo per le impostazioni locali sotto forma di linguaggio BCP47, ad esempio en-US, de, zh-CN* ``toDouble(123.45) -> 123.45``
* ``toDouble('123.45') -> 123.45``
* ``toDouble('$123.45', '$###.00') -> 123.45``
* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``
___
### <code>toFloat</code>
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
Converte un valore numerico o stringa in un valore float. Per la conversione è possibile usare un formato decimale Java facoltativo. Tronca qualsiasi valore Double* ``toFloat(123.45) -> 123.45f``
* ``toFloat('123.45') -> 123.45f``
* ``toFloat('$123.45', '$###.00') -> 123.45f``
___
### <code>toInteger</code>
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code><br/><br/>
Converte qualsiasi valore numerico o stringa in un valore integer. Per la conversione è possibile usare un formato decimale Java facoltativo. Tronca qualsiasi tipo Long, float, Double* ``toInteger(123) -> 123``
* ``toInteger('123') -> 123``
* ``toInteger('$123', '$###') -> 123``
___
### <code>toLong</code>
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code><br/><br/>
Converte un valore numerico o stringa in un valore long. Per la conversione è possibile usare un formato decimale Java facoltativo. Tronca gli eventuali valori float, Double* ``toLong(123) -> 123``
* ``toLong('123') -> 123``
* ``toLong('$123', '$###') -> 123``
___
### <code>toShort</code>
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
Converte un valore numerico o stringa in un valore short. Per la conversione è possibile usare un formato decimale Java facoltativo. Tronca qualsiasi Integer, Long, float, Double* ``toShort(123) -> 123``
* ``toShort('123') -> 123``
* ``toShort('$123', '$###') -> 123``
___
### <code>toString</code>
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
Converte un tipo di dati primitivo in una stringa. Per numeri e date è possibile specificare un formato. Se non è specificato, viene selezionato il valore predefinito del sistema. Per i numeri viene usato il formato decimale Java. Vedere SimpleDateFormat Java per tutti i formati di data possibili; il formato predefinito è AAAA-MM-GG* ``toString(10) -> '10'``
* ``toString('engineer') -> 'engineer'``
* ``toString(123456.789, '##,###.##') -> '123,456.79'``
* ``toString(123.78, '000000.000') -> '000123.780'``
* ``toString(12345, '##0.#####E0') -> '12.345E3'``
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``
* ``isNull(toString(toDate('2018-12-31', 'MM/dd/yy'))) -> true``
* ``toString(4 == 20) -> 'false'``
___
### <code>toTimestamp</code>
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
Converte una stringa in un timestamp in base a un formato timestamp facoltativo. Vedere Java SimpleDateFormat per tutti i formati possibili. Se il timestamp viene omesso, viene utilizzato il modello predefinito. aaaa-[M] M-[d] d HH: mm: SS [. f...]. È possibile passare un fuso orario facoltativo nel formato ' GMT ',' PST ',' UTC ',' America/Cayman '. Timestamp supporta la precisione fino al millisecondo con valore 999* ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')``
* ``toTimestamp('2016-12-31T00:12:00', 'yyyy-MM-dd\'T\'HH:mm:ss', 'PST') -> toTimestamp('2016-12-31 00:12:00')``
* ``toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss') -> toTimestamp('2016-12-31 00:12:00')``
* ``millisecond(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
___
### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Converte il timestamp in UTC. È possibile passare un fuso orario facoltativo nel formato 'GMT', 'PST', 'UTC', 'America/Cayman'. Il valore predefinito è il fuso orario corrente* ``toUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Sostituisce un set di caratteri con un altro set di caratteri nella stringa. I caratteri hanno una sostituzione da 1 a 1* ``translate('(bojjus)', '()', '[]') -> '[bojjus]'``
* ``translate('(gunchus)', '()', '[') -> '[gunchus'``
___
### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Rimuove i caratteri iniziali e finali di una stringa. Se il secondo parametro non è specificato, rimuove lo spazio vuoto. In caso contrario, viene ritagliato qualsiasi carattere specificato nel secondo parametro* ``trim('  dumbo  ') -> 'dumbo'``
* ``trim('!--!du!mbo!', '-!') -> 'du!mbo'``
___
### <code>true</code>
<code><b>true() => boolean</b></code><br/><br/>
Restituisce sempre un valore true. Usare la sintassi della funzione (true ()) se è presente una colonna denominata ' true '* ``(10 + 20 == 30) -> true``
* ``(10 + 20 == 30) -> true()``
___
### <code>typeMatch</code>
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
Trova corrispondenze tra tipi di colonne. Può essere utilizzato solo nelle espressioni di modello. Number corrisponde a short, Integer, Long, Double, float o Decimal, le corrispondenze integrali Short, Integer, Long, frazionarie corrisponde a Double, float, Decimal e DateTime corrisponde alla data o al tipo di timestamp* ``typeMatch(type, 'number')``
* ``typeMatch('date', 'datetime')``
___
### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Maiuscole di una stringa* ``upper('bojjus') -> 'BOJJUS'``
___
### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Ottiene la varianza di una colonna* ``variance(sales)``
___
### <code>varianceIf</code>
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
In base a un criterio, ottiene la varianza di una colonna* ``varianceIf(region == 'West', sales)``
___
### <code>variancePopulation</code>
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Ottiene la varianza della popolazione di una colonna* ``variancePopulation(sales)``
___
### <code>variancePopulationIf</code>
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
In base a un criterio, ottiene la varianza della popolazione di una colonna* ``variancePopulationIf(region == 'West', sales)``
___
### <code>varianceSample</code>
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Ottiene la varianza non distorta di una colonna* ``varianceSample(sales)``
___
### <code>varianceSampleIf</code>
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
In base a un criterio, ottiene la varianza non distorta di una colonna* ``varianceSampleIf(region == 'West', sales)``
___
### <code>weekOfYear</code>
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Ottiene la settimana dell'anno in base a una data* ``weekOfYear(toDate('2008-02-20')) -> 8``
___
### <code>weeks</code>
<code><b>weeks(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Durata in millisecondi per il numero di settimane* ``weeks(2) -> 1209600000L``
___
### <code>xor</code>
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Operatore XOR logico. Uguale all'operatore ^* ``xor(true, false) -> true``
* ``xor(true, true) -> false``
* ``true ^ false -> true``
___
### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Ottiene il valore dell'anno di una data* ``year(toDate('2012-8-8')) -> 2012``

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come usare il generatore di espressioni](concepts-data-flow-expression-builder.md).
