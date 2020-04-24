---
title: Analyseoperator - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der Analyseoperator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: beb51ac80810e5d14013e9b3571d759bb7b09125
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511508"
---
# <a name="parse-operator"></a>parse-Operator

Wertet einen Zeichenfolgenausdruck aus und analysiert dessen Wert in eine oder mehrere berechnete Spalten. Bei nicht erfolgreich analysierten Zeichenfolgen haben berechnete Spalten NULLs.
Siehe [parse-where-Operator,](parsewhereoperator.md) der erfolglos analysierte Zeichenfolgen herausfiltert.

```kusto
T | parse Text with "ActivityName=" name ", ActivityType=" type
```

**Syntax**

*T* `| parse` `kind=regex` [`flags=regex_flags`[`simple` ] | | `*` `*` *Expression* `with` `:` *ColumnType**StringConstant* ] Ausdruck ( StringConstant ColumnName [ ColumnType ]) ... *ColumnName* `relaxed`

**Argumente**

* *T*: Die Eingabetabelle.
* Art: 

    * simple (standard) : StringConstant ist ein regulärer Zeichenfolgenwert, und die Übereinstimmung ist streng, was bedeutet, dass alle Zeichenfolgentrennzeichen in der analysierten Zeichenfolge angezeigt werden sollen und alle erweiterten Spalten mit den erforderlichen Typen übereinstimmen müssen.
        
    * regex : StringConstant kann ein regulärer Ausdruck sein, und die Übereinstimmung ist streng, was bedeutet, dass alle Zeichenfolgentrennzeichen (die ein Regex für diesen Modus sein können) in der analysierten Zeichenfolge angezeigt werden sollen und alle erweiterten Spalten den erforderlichen Typen entsprechen müssen.
    
    * Flags : Flags, die im `U` Regex-Modus `m` `s` wie (Ungreedy), (Mehrzeilenmodus), (neue Zeile) `\n` `i` (ohne Groß-/Kleinschreibung) und mehr in [RE2-Flags](re2.md)verwendet werden sollen.
        
    * relaxed : StringConstant ist ein regulärer Zeichenfolgenwert, und die Übereinstimmung wird gelockert, was bedeutet, dass alle Zeichenfolgentrennzeichen in der analysierten Zeichenfolge angezeigt werden sollten, aber erweiterte Spalten können teilweise mit den erforderlichen Typen übereinstimmen (erweiterte Spalten, die nicht mit den erforderlichen Typen übereinstimmen, erhalten den Wert null).

* *Ausdruck*: Ein Ausdruck, der zu einer Zeichenfolge ausgewertet wird.

* *Spaltenname:* Der Name einer Spalte, der ein Wert zugewiesen werden soll (aus dem Zeichenfolgenausdruck herausgenommen). 
  
* *ColumnType:* sollte optionaler skalarer Typ sein, der den Typ angibt, in den der Wert konvertiert werden soll (standardmäßig ist es Zeichenfolgentyp).

**Rückgabe**

Die Eingabetabelle, die entsprechend der Liste der Spalten erweitert wird, die dem Operator bereitgestellt werden.

**Tipps**

* Verwenden [`project`](projectoperator.md) Sie diese Datei, wenn Sie auch einige Spalten löschen oder umbenennen möchten.

* Verwenden Sie * im Muster, um Junk-Werte zu überspringen (kann nach einer Zeichenfolgenspalte nicht verwendet werden)

* Das Analysemuster kann mit *ColumnName* und nicht nur mit *StringConstant*beginnen. 

* Wenn der analysierte *Ausdruck* nicht vom Typ string ist, wird er in type string konvertiert.

* Wenn der Regex-Modus verwendet wird, gibt es eine Option zum Hinzufügen von Regex-Flags, um den gesamten Regex zu steuern, der in Parse verwendet wird.

* Im Regex-Modus übersetzt parse das Muster in einen Regex und verwendet [die RE2-Syntax,](re2.md) um den Abgleich mithilfe nummerierter erfasster Gruppen zu tun, die intern behandelt werden.
  Diese Parse-Anweisung ist also z. B. :
  
    ```kusto
    parse kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    Der Regex, der intern von der `.*?<regex1>(.*?)<regex2>(\-\d+)`Analyse generiert wird, ist .
        
    - `*`wurde in `.*?`übersetzt.
        
    - `string`wurde in `.*?`übersetzt.
        
    - `long`wurde in `\-\d+`übersetzt.

**Beispiele**

Der `parse` Operator bietet eine `extend` optimierte Möglichkeit `extract` zu einer `string` Tabelle, indem mehrere Anwendungen für denselben Ausdruck verwendet werden.
Dies ist besonders nützlich, `string` wenn die Tabelle über eine Spalte verfügt, die mehrere Werte enthält, die`printf`Sie in`Console.WriteLine`einzelne Spalten aufteilen möchten, z. B. eine Spalte, die von einer Entwicklerablaufverfolgungsanweisung (" "/" " erstellt wurde.

Im folgenden Beispiel wird davon `EventText` ausgegangen, dass die Tabellenspalte `Traces` Zeichenfolgen des Formulars `Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})`enthält.
Der folgende Vorgang erweitert die Tabelle `resourceName` `totalSlices`um `sliceNumber` `lockTime `6 `releaseTime` `previouLockTime`Spalten: , , , , , , `Month` und `Day`. 


```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces  
| parse EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

|resourceName|totalSlices|sliceNummer|lockTime|releaseTime|previouLockTime|
|---|---|---|---|---|---|
|PipelineScheduler|27|15|02/17/2016 08:40:00|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|PipelineScheduler|27|23|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler|27|16|02/17/2016 08:41:00 (17.02.2016)|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|

für Regex-Modus :

```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces  
| parse kind = regex EventText with "(.*?)[a-zA-Z]*=" resourceName @", totalSlices=\s*\d+\s*.*?sliceNumber=" sliceNumber:long  ".*?(previous)?lockTime=" lockTime ".*?releaseTime=" releaseTime ".*?previousLockTime=" previouLockTime:date "\\)"  
| project resourceName , sliceNumber , lockTime , releaseTime , previouLockTime
```

|resourceName|sliceNummer|lockTime|releaseTime|previouLockTime|
|---|---|---|---|---|
|PipelineScheduler|15|02/17/2016 08:40:00, |02/17/2016 08:40:00, |2016-02-17 08:39:00.0000000|
|PipelineScheduler|23|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|PipelineScheduler|20|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|PipelineScheduler|16|02/17/2016 08:41:00, |02/17/2016 08:41:00, |2016-02-17 08:40:00.0000000|
|PipelineScheduler|22|02/17/2016 08:41:01, |02/17/2016 08:41:00, |2016-02-17 08:40:01.0000000|


für regex-Modus mit regex-Flags:

wenn wir daran interessiert sind, nur den resourceName zu erhalten, und wir diese Abfrage verwenden:

```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind = regex  EventText with * "resourceName=" resourceName ',' *
| project resourceName
```

|resourceName|
|---|
|PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=17.02.2016 08:40:01|
|PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00|
|PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=17.02.2016 08:40:01|
|PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=17.02.2016 08:41:00|
|PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00|

Aber wir erhalten nicht die erwarteten Ergebnisse, da der Standardmodus gierig ist.
oder auch wenn wir nur wenige Datensätze hatten, bei denen der resourceName manchmal in Kleinbuchstaben angezeigt wird, sodass wir für einige Werte Nullen erhalten können.
Um das gewünschte Ergebnis zu erhalten, können wir dieses mit`U`dem nicht gierigen`i`( ) ausführen und die Groß-/Kleinschreibung ( ) regex-Flags deaktivieren:

```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind = regex flags = Ui EventText with * "RESOURCENAME=" resourceName ',' *
| project resourceName
```

|resourceName|
|---|
|PipelineScheduler|
|PipelineScheduler|
|PipelineScheduler|
|PipelineScheduler|
|PipelineScheduler|


Wenn die analysierte Zeichenfolge Zeilenlinien hat, `s` sollten Sie das Flag verwenden, um den Text wie erwartet zu analysieren:

```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=23\nlockTime=02/17/2016 08:40:01\nreleaseTime=02/17/2016 08:40:01\npreviousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=15\nlockTime=02/17/2016 08:40:00\nreleaseTime=02/17/2016 08:40:00\npreviousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=20\nlockTime=02/17/2016 08:40:01\nreleaseTime=02/17/2016 08:40:01\npreviousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=22\nlockTime=02/17/2016 08:41:01\nreleaseTime=02/17/2016 08:41:00\npreviousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=16\nlockTime=02/17/2016 08:41:00\nreleaseTime=02/17/2016 08:41:00\npreviousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind=regex flags=s EventText with * "resourceName=" resourceName:string "(.*?)totalSlices=" totalSlices:long "(.*?)lockTime=" lockTime:datetime "(.*?)releaseTime=" releaseTime:datetime "(.*?)previousLockTime=" previousLockTime:datetime "\\)" 
| project-away EventText
```

|resourceName|totalSlices|lockTime|releaseTime|previousLockTime|
|---|---|---|---|---|
|PipelineScheduler<br>|27|2016-02-17 08:40:00.0000000|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:40:01.0000000|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:40:01.0000000|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:41:00.0000000|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:41:01.0000000|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|


In diesem Beispiel für den entspannten Modus: die erweiterte Spalte totalSlices muss vom Typ long sein, aber in der analysierten Zeichenfolge hat sie den Wert nonValidLongValue.
ReleaseTime extended Spalte hat das gleiche Problem, der Wert nonValidDateTime kann nicht als datetime analysiert werden.
In diesem Fall erhalten diese beiden erweiterten Spalten den Wert null, während die anderen (wie sliceNumber) immer noch die richtigen Werte abrufen.

Mit kind = simple für die gleiche Abfrage unten gibt null für alle erweiterten Spalten, da es streng auf erweiterte Spalten (das ist der Unterschied zwischen entspannten und einfachen Modus, im entspannten Modus, erweiterte Spalten können teilweise abgeglichen werden).

```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=nonValidDateTime 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=nonValidDateTime, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=nonValidLongValue, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=nonValidDateTime 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=nonValidLongValue, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind=relaxed EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *
| project-away EventText
```

|resourceName|totalSlices|sliceNummer|lockTime|releaseTime|previouLockTime|
|---|---|---|---|---|---|
|PipelineScheduler|27|15|02/17/2016 08:40:00||2016-02-17 08:39:00.0000000|
|PipelineScheduler|27|23|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|PipelineScheduler||20|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|PipelineScheduler||16|02/17/2016 08:41:00 (17.02.2016)|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|