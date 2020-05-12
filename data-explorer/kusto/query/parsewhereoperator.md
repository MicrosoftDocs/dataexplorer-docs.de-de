---
title: Analyse-Where-Operator-Azure Daten-Explorer
description: In diesem Artikel wird der Operator "Analyse-where" in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/12/2020
ms.openlocfilehash: c0ee38fe77c0957b9ba7fd589115eee20be6a649
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83224849"
---
# <a name="parse-where-operator"></a>parse-where-Operator

Wertet einen Zeichen folgen Ausdruck aus und analysiert seinen Wert in mindestens eine berechnete Spalte. Das Ergebnis ist nur die erfolgreich analysierten Zeichen folgen.
Weitere Informationen finden Sie unter Analyse [Operator](parseoperator.md), der Nullen für nicht erfolgreich analysierte Zeichen folgen erzeugt.

```kusto
T | parse-where Text with "ActivityName=" name ", ActivityType=" type
```

**Syntax**

*T* `| parse-where` [ `kind=regex` [ `flags=regex_flags` ] | `simple` ]- *Ausdruck* `with` `*` (*StringConstant* *ColumnName* [ `:` *ColumnType*]) `*` ...

**Argumente**

* *T*: die Eingabe Tabelle.

* *Art*: 

    * *Simple* (Standard): StringConstant ist ein regulärer Zeichen folgen Wert, und die Entsprechung ist "Strict". Alle Zeichen folgen Trennzeichen sollten in der analysierten Zeichenfolge angezeigt werden, und alle erweiterten Spalten müssen mit den erforderlichen Typen identisch sein.
        
    * *Regex*: "StringConstant" kann ein regulärer Ausdruck sein, und die Entsprechung ist "Strict". Alle Zeichen folgen Trennzeichen sollten in der analysierten Zeichenfolge angezeigt werden, und alle erweiterten Spalten müssen mit den erforderlichen Typen identisch sein. Zeichen folgen Trennzeichen können ein Regex für diesen Modus sein.
    
    * *Flags*: für den Regex-Modus zu verwendende Flags: `U` (ungierig), `m` (mehrzeiligen Modus), `s` (Übereinstimmung mit neuer Zeile), (keine Unterscheidung nach `\n` `i` Groß-/Kleinschreibung), weitere Flags können in [RE2-Flags](re2.md)gefunden werden.
        
* *Expression*: ein Ausdruck, der zu einer Zeichenfolge ausgewertet wird.

* *ColumnName:* Der Name einer Spalte, die einem Wert zugewiesen wird, der aus dem Zeichen folgen Ausdruck entnommen wurde. 
  
* *ColumnType:* sollte ein optionaler Skalartyp sein, der den Typ angibt, in den der Wert konvertiert werden soll. Der Standardwert ist String Type.

**Rückgabe**

Die Eingabe Tabelle, die entsprechend der Liste der Spalten erweitert wird, die dem Operator bereitgestellt werden.

> [!Note] 
> Nur erfolgreich analysierte Zeichen folgen werden in der Ausgabe angezeigt. Zeichen folgen, die nicht dem Muster entsprechen, werden herausgefiltert.

**Tipps**

* `parse-where`analysiert die Zeichen folgen auf die gleiche Weise wie die Analyse und filtert Zeichen [folgen, die](parseoperator.md)nicht erfolgreich analysiert wurden.

* Verwenden Sie [Project](projectoperator.md) , wenn Sie auch einige Spalten löschen oder umbenennen möchten.

* Verwenden Sie * im Muster, um Junk-Werte zu überspringen. Dieser Wert kann nach einer Zeichen folgen Spalte nicht verwendet werden.

* Das Analyse Muster kann zusätzlich zu *StringConstant*mit *ColumnName*beginnen. 

* Wenn der analysierte *Ausdruck* nicht vom Typ "String" ist, wird er in den Typ "String" konvertiert.

* Wenn der Regex-Modus verwendet wird, können Sie Regex-Flags hinzufügen, um den gesamten in der Analyse verwendeten regulären Ausdruck zu steuern.

* Im Regex-Modus übersetzt die Analyse das Muster in eine Regex-Syntax und verwendet die [RE2-Syntax](re2.md) , um die Übereinstimmung mit nummerierten aufgezeichneten Gruppen zu erreichen, die intern behandelt werden.
  
  Diese Analyse Anweisung z. b.:
  
    ```kusto
    parse-where kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    Der Regex, der von der Analyse intern generiert wird, ist `.*?<regex1>(.*?)<regex2>(\-\d+)` .
        
    - `*`wurde in übersetzt `.*?` .
        
    - `string`wurde in übersetzt `.*?` .
        
    - `long`wurde in übersetzt `\-\d+` .

## <a name="examples"></a>Beispiele

Der- `parse-where` Operator bietet eine optimierte Methode für `extend` eine Tabelle, indem mehrere `extract` Anwendungen für denselben `string` Ausdruck verwendet werden. Dies ist besonders hilfreich, wenn die Tabelle eine `string` Spalte enthält, die mehrere Werte enthält, die Sie in einzelne Spalten unterbrechen möchten. Beispielsweise können Sie eine Spalte, die von einer Entwickler `printf` -Trace-Anweisung (""/"") erstellt wurde, unterbrechen `Console.WriteLine` .

### <a name="using-parse"></a>Verwenden von `parse`

Im folgenden Beispiel enthält die-Spalte der-Tabelle Zeichen folgen in der `EventText` `Traces` Form `Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})` . Mit dem folgenden Vorgang wird die Tabelle mit sechs Spalten erweitert: `resourceName` , `totalSlices` , `sliceNumber` , `lockTime ` , `releaseTime` , `previouLockTime` , `Month` und `Day` . 

Einige der Zeichen folgen haben keine vollständige Entsprechung.

Mithilfe von `parse` haben die berechneten Spalten Nullen.

```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=invalid_number, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=invalid_datetime, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=invalid_number, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces  
| parse EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

|resourceName|totalslices|slicennummer|Sperr Zeit|releasetime|previouslocktime|
|---|---|---|---|---|---|
|||||||
|||||||
|||||||
|Pipelinescheduler|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|Pipelinescheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|

### <a name="using-parse-where"></a>Verwenden von `parse-where` 

Durch die Verwendung von ' Analyse-WHERE ' werden nicht erfolgreich analysierte Zeichen folgen aus dem Ergebnis herausgefiltert.

```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=invalid_number, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=invalid_datetime, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=invalid_number, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces  
| parse-where EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

|resourceName|totalslices|slicennummer|Sperr Zeit|releasetime|previouslocktime|
|---|---|---|---|---|---|
|Pipelinescheduler|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|Pipelinescheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|


### <a name="regex-mode-using-regex-flags"></a>Regex-Modus mit Regex-Flags

Verwenden Sie zum Abfragen von resourceName und totalslices die folgende Abfrage:

```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=non_valid_integer, sliceNumber=11, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=non_valid_integer, sliceNumber=44, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse-where kind = regex EventText with * "RESOURCENAME=" resourceName "," * "totalSlices=" totalSlices:long "," *
| project resourceName, totalSlices
```

### <a name="parse-where-with-case-insensitive-regex-flag"></a>`parse-where`ohne Beachtung der Groß-/Kleinschreibung von Regex

In der obigen Abfrage hat der Standardmodus die Groß-/Kleinschreibung beachtet, sodass die Zeichen folgen erfolgreich analysiert wurden. Es wurde kein Ergebnis abgerufen.

Um das erforderliche Ergebnis zu erhalten, führen Sie `parse-where` mit dem Regex-Flag für die Groß-/Kleinschreibung ( `i` ) aus.

Es werden nur drei Zeichen folgen erfolgreich analysiert, sodass das Ergebnis drei Datensätze ist (einige totalslices enthalten ungültige ganze Zahlen).

```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=non_valid_integer, sliceNumber=11, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=non_valid_integer, sliceNumber=44, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse-where kind = regex flags=i EventText with * "RESOURCENAME=" resourceName "," * "totalSlices=" totalSlices:long "," *
| project resourceName, totalSlices
```

|resourceName|totalslices|
|---|---|
|Pipelinescheduler|27|
|Pipelinescheduler|27|
|Pipelinescheduler|27|
