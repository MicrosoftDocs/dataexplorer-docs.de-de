---
title: Parse-where-Operator - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der Parse-where-Operator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/12/2020
ms.openlocfilehash: 0e44ab83242fc8aed0e46bdab1fa5a142992bfe5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511406"
---
# <a name="parse-where-operator"></a>parse-where-Operator

Wertet einen Zeichenfolgenausdruck aus und analysiert dessen Wert in eine oder mehrere berechnete Spalten. Das Ergebnis sind nur die erfolgreich analysierten Zeichenfolgen.
Siehe [parse-Operator,](parseoperator.md) der nulls für erfolglos analysierte Zeichenfolgen erzeugt.

```kusto
T | parse-where Text with "ActivityName=" name ", ActivityType=" type
```

**Syntax**

*T* `| parse-where` `kind=regex` [`flags=regex_flags`]`simple`| ] *Expression* `with` Ausdruck `*` (*StringConstant* `:` *ColumnName* [ `*` *ColumnType*]) ...

**Argumente**

* *T*: Die Eingabetabelle.

* Art: 

    * simple (standard) : StringConstant ist ein regulärer Zeichenfolgenwert, und die Übereinstimmung ist streng, was bedeutet, dass alle Zeichenfolgentrennzeichen in der analysierten Zeichenfolge angezeigt werden sollen und alle erweiterten Spalten mit den erforderlichen Typen übereinstimmen müssen.
        
    * regex : StringConstant kann ein regulärer Ausdruck sein, und die Übereinstimmung ist streng, was bedeutet, dass alle Zeichenfolgentrennzeichen (die ein Regex für diesen Modus sein können) in der analysierten Zeichenfolge angezeigt werden sollen und alle erweiterten Spalten den erforderlichen Typen entsprechen müssen.
    
    * Flags : Flags, die im `U` Regex-Modus `m` `s` wie (Ungreedy), (Mehrzeilenmodus), (neue Zeile) `\n` `i` (ohne Groß-/Kleinschreibung) und mehr in [RE2-Flags](re2.md)verwendet werden sollen.
        
* *Ausdruck*: Ein Ausdruck, der zu einer Zeichenfolge ausgewertet wird.

* *Spaltenname:* Der Name einer Spalte, der ein Wert zugewiesen werden soll (aus dem Zeichenfolgenausdruck herausgenommen). 
  
* *ColumnType:* sollte optionaler skalarer Typ sein, der den Typ angibt, in den der Wert konvertiert werden soll (standardmäßig ist es Zeichenfolgentyp).

**Rückgabe**

Die Eingabetabelle, die entsprechend der Liste der Spalten erweitert wird, die dem Operator bereitgestellt werden.
Nur erfolgreich analysierte Zeichenfolgen werden in der Ausgabe angezeigt. Zeichenfolgen, die nicht mit dem Muster übereinstimmen, werden herausgefiltert.

**Tipps**

* `parse-where`analysiert die Zeichenfolgen auf die gleiche Weise wie [die Analyse,](parseoperator.md) filtert aber zusätzlich zeichenfolgen, die nicht erfolgreich analysiert wurden.

* Verwenden [`project`](projectoperator.md) Sie diese Datei, wenn Sie auch einige Spalten löschen oder umbenennen möchten.

* Verwenden Sie * im Muster, um Junk-Werte zu überspringen (kann nach einer Zeichenfolgenspalte nicht verwendet werden)

* Das Analysemuster kann mit *ColumnName* und nicht nur mit *StringConstant*beginnen. 

* Wenn der analysierte *Ausdruck* nicht vom Typ string ist, wird er in type string konvertiert.

* Wenn der Regex-Modus verwendet wird, gibt es eine Option zum Hinzufügen von Regex-Flags, um den gesamten Regex zu steuern, der in Parse verwendet wird.

* Im Regex-Modus übersetzt parse das Muster in einen Regex und verwendet [die RE2-Syntax,](re2.md) um den Abgleich mithilfe nummerierter erfasster Gruppen zu tun, die intern behandelt werden.
  
  Diese Parse-Anweisung ist also z. B. :
  
    ```kusto
    parse-where kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    Der Regex, der intern von der `.*?<regex1>(.*?)<regex2>(\-\d+)`Analyse generiert wird, ist .
        
    - `*`wurde in `.*?`übersetzt.
        
    - `string`wurde in `.*?`übersetzt.
        
    - `long`wurde in `\-\d+`übersetzt.

**Beispiele**

Der `parse-where` Operator bietet eine `extend` optimierte Möglichkeit `extract` zu einer `string` Tabelle, indem mehrere Anwendungen für denselben Ausdruck verwendet werden.
Dies ist besonders nützlich, `string` wenn die Tabelle über eine Spalte verfügt, die mehrere Werte enthält, die`printf`Sie in`Console.WriteLine`einzelne Spalten aufteilen möchten, z. B. eine Spalte, die von einer Entwicklerablaufverfolgungsanweisung (" "/" " erstellt wurde.

Im folgenden Beispiel wird davon `EventText` ausgegangen, dass die Tabellenspalte `Traces` Zeichenfolgen des Formulars `Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})`enthält.
Der folgende Vorgang erweitert die Tabelle `resourceName` `totalSlices`um `sliceNumber` `lockTime `6 `releaseTime` `previouLockTime`Spalten: , , , , , , `Month` und `Day`. 

Hier haben nur wenige Zeichenfolgen keine vollständige Übereinstimmung.
Mit `parse`haben die berechneten Spalten Nullen:

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
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

|resourceName|totalSlices|sliceNummer|lockTime|releaseTime|previouLockTime|
|---|---|---|---|---|---|
|||||||
|||||||
|||||||
|PipelineScheduler|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|


Mit `parse-where` der Verwendung werden erfolglos analysierte Zeichenfolgen aus dem Ergebnis gefiltert:

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
| parse-where EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

|resourceName|totalSlices|sliceNummer|lockTime|releaseTime|previouLockTime|
|---|---|---|---|---|---|
|PipelineScheduler|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|


für regex-Modus mit regex-Flags:

wenn wir daran interessiert sind, den resouceName und totalSlices zu erhalten, und wir diese Abfrage verwenden:

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

In der obigen Abfrage erhalten wir kein Ergebnis, da im Standardmodus die Groß-/Kleinschreibung beachtet wird, sodass keine der Zeichenfolgen erfolgreich analysiert wurde.

Um das erforderliche Ergebnis zu erhalten, `parse-where` können wir die`i`regex-Flagge mit Groß-/Kleinschreibung ( ) ausführen.
Beachten Sie, dass nur 3 Zeichenfolgen erfolgreich analysiert werden und das Ergebnis 3 Datensätze ist (einige totalSlices enthalten ungültige ganze Zahlen): 

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

|resourceName|totalSlices|
|---|---|
|PipelineScheduler|27|
|PipelineScheduler|27|
|PipelineScheduler|27|


