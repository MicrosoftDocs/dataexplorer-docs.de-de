---
title: 'Analyse Operator: Azure-Daten-Explorer'
description: In diesem Artikel wird der Analyse Operator in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 07318a64781678410374f902ff8fe5514a4bdd17
ms.sourcegitcommit: f9d3f54114fb8fab5c487b6aea9230260b85c41d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2020
ms.locfileid: "85071897"
---
# <a name="parse-operator"></a>parse-Operator

Wertet einen Zeichenfolgenausdruck aus und analysiert dessen Wert in eine oder mehrere berechnete Spalten. Bei nicht erfolgreich analysierten Zeichen folgen weisen berechnete Spalten Nullen auf. 

Weitere Informationen finden Sie unter Analyse [-Where-](parsewhereoperator.md) Operator, der nicht erfolgreich analysierte Zeichen folgen filtert.

```kusto
T | parse Text with "ActivityName=" name ", ActivityType=" type
```

**Syntax**

*T* `| parse` [ `kind=regex` [ `flags=regex_flags` ] | `simple` | `relaxed` ]- *Ausdruck* `with` `*` (*StringConstant* *ColumnName* [ `:` *ColumnType*]) `*` ...

**Argumente**

* *T*: die Eingabe Tabelle.
* Art 

    * Simple (Standard): StringConstant ist ein regulärer Zeichen folgen Wert, und die Entsprechung ist Strict. Dies bedeutet, dass alle Zeichen folgen Trennzeichen in der analysierten Zeichenfolge angezeigt werden sollen und alle erweiterten Spalten den erforderlichen Typen entsprechen müssen.
        
    * Regex: StringConstant kann ein regulärer Ausdruck sein, und die Entsprechung ist Strict. Dies bedeutet, dass alle Zeichen folgen Trennzeichen (die ein Regex für diesen Modus sein können) in der analysierten Zeichenfolge angezeigt werden und alle erweiterten Spalten den erforderlichen Typen entsprechen müssen.
    
    * Flags: Flags, die im Regex-Modus verwendet werden sollen, wie `U` ( `m` ungieriger), (mehrzeiligen Modus), `s` (Übereinstimmung mit neuer Zeile) `\n` , (ohne Beachtung `i` der Groß-/Kleinschreibung) und mehr in [RE2-Flags](re2.md).
        
    * gelockert: StringConstant ist ein regulärer Zeichen folgen Wert, und die Entsprechung wird gelockert, was bedeutet, dass alle Zeichen folgen Trennzeichen in der analysierten Zeichenfolge angezeigt werden sollen, dass erweiterte Spalten jedoch möglicherweise den erforderlichen Typen entsprechen (Erweiterte Spalten, die nicht den erforderlichen Typen entsprechen, erhalten den Wert null).

* *Expression*: ein Ausdruck, der zu einer Zeichenfolge ausgewertet wird.

* *ColumnName:* Der Name einer Spalte, der ein Wert (aus dem Zeichen folgen Ausdruck entnommen) zugewiesen werden soll. 
  
* *ColumnType:* sollte ein optionaler Skalartyp sein, der den Typ angibt, in den der Wert konvertiert werden soll (Standardmäßig ist dies String Type).

**Rückgabe**

Die Eingabe Tabelle, erweitert entsprechend der Liste der Spalten, die dem Operator bereitgestellt werden.

**Tipps**

* Verwenden [`project`](projectoperator.md) Sie, wenn Sie auch einige Spalten löschen oder umbenennen möchten.

* Verwenden Sie * im Muster, um Junk-Werte zu überspringen (kann nach Zeichen folgen Spalte nicht verwendet werden).

* Das Analyse Muster kann mit *ColumnName* beginnen und nicht nur mit *StringConstant*. 

* Wenn der analysierte *Ausdruck* nicht vom Typ "String" ist, wird er in den Typ "String" konvertiert.

* Wenn der Regex-Modus verwendet wird, gibt es eine Option zum Hinzufügen von Regex-Flags, um den gesamten in der Analyse verwendeten Regex zu steuern.

* Im Regex-Modus übersetzt die Analyse das Muster in ein Regex und verwendet die [RE2-Syntax](re2.md) , um die Übereinstimmung mit nummerierten erfassten Gruppen zu erreichen, die intern behandelt werden.
  Diese Analyse Anweisung z. b.:
  
    ```kusto
    parse kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    Der Regex, der von der Analyse intern generiert wird, ist `.*?<regex1>(.*?)<regex2>(\-\d+)` .
        
    - `*`wurde in übersetzt `.*?` .
        
    - `string`wurde in übersetzt `.*?` .
        
    - `long`wurde in übersetzt `\-\d+` .

**Beispiele**

Der- `parse` Operator bietet eine optimierte Methode für `extend` eine Tabelle, indem mehrere `extract` Anwendungen für denselben `string` Ausdruck verwendet werden.
Dies ist besonders hilfreich, wenn die Tabelle eine `string` Spalte enthält, die mehrere Werte enthält, die Sie in einzelne Spalten unterbrechen möchten, z. b. eine Spalte, die von einer Entwickler-Ablauf Verfolgungs Anweisung (" `printf` "/" `Console.WriteLine` ") erstellt wurde.

Nehmen Sie im folgenden Beispiel an, dass die Spalte `EventText` der Tabelle Zeichen folgen `Traces` des Formulars enthält `Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})` .
Mit dem folgenden Vorgang wird die Tabelle mit 6 Spalten erweitert: `resourceName` , `totalSlices` , `sliceNumber` , `lockTime ` , `releaseTime` , `previouLockTime` `Month` und `Day` . 

<!-- csl: https://help.kusto.windows.net/Samples -->
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

|resourceName|totalslices|slicennummer|Sperr Zeit|releasetime|previoulocktime|
|---|---|---|---|---|---|
|Pipelinescheduler|27|15|02/17/2016 08:40:00|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|Pipelinescheduler|27|23|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|Pipelinescheduler|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|Pipelinescheduler|27|16|02/17/2016 08:41:00 (17.02.2016)|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|Pipelinescheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|

für den Regex-Modus:

<!-- csl: https://help.kusto.windows.net/Samples -->
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

|resourceName|slicennummer|Sperr Zeit|releasetime|previoulocktime|
|---|---|---|---|---|
|Pipelinescheduler|15|02/17/2016 08:40:00, |02/17/2016 08:40:00, |2016-02-17 08:39:00.0000000|
|Pipelinescheduler|23|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|Pipelinescheduler|20|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|Pipelinescheduler|16|02/17/2016 08:41:00, |02/17/2016 08:41:00, |2016-02-17 08:40:00.0000000|
|Pipelinescheduler|22|02/17/2016 08:41:01, |02/17/2016 08:41:00, |2016-02-17 08:40:01.0000000|


für den Regex-Modus mit Regex-Flags:

Wenn Sie nur den resourceName erhalten möchten und diese Abfrage verwendet wird:

<!-- csl: https://help.kusto.windows.net/Samples -->
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
|Pipelinescheduler, totalslices = 27, slicengber = 23, Locktime = 02/17/2016 08:40:01, releasetime = 02/17/2016 08:40:01|
|Pipelinescheduler, totalslices = 27, slicenumschlag = 15, Locktime = 02/17/2016 08:40:00, releasetime = 02/17/2016 08:40:00|
|Pipelinescheduler, totalslices = 27, slicengber = 20, Locktime = 02/17/2016 08:40:01, releasetime = 02/17/2016 08:40:01|
|Pipelinescheduler, totalslices = 27, slicengber = 22, Locktime = 02/17/2016 08:41:01, releasetime = 02/17/2016 08:41:00|
|Pipelinescheduler, totalslices = 27, slicengber = 16, Locktime = 02/17/2016 08:41:00, releasetime = 02/17/2016 08:41:00|

Wir erhalten jedoch nicht die erwarteten Ergebnisse, da der Standardmodus gieriger ist.
oder auch wenn einige Datensätze vorhanden sind, bei denen der resourceName manchmal in Großbuchstaben vorkommt, damit wir ggf. Nullen für einige Werte erhalten.
um das gewünschte Ergebnis zu erhalten, können wir dieses mit dem nicht gierigen () ausführen und die Unterscheidung nach `U` Groß-/Kleinschreibung ( `i` ) Regex-Flags deaktivieren:

<!-- csl: https://help.kusto.windows.net/Samples -->
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
|Pipelinescheduler|
|Pipelinescheduler|
|Pipelinescheduler|
|Pipelinescheduler|
|Pipelinescheduler|


Wenn die analysierte Zeichenfolge Zeilenumbrüche enthält, sollten Sie das-Flag verwenden, `s` um den Text erwartungsgemäß zu analysieren:

<!-- csl: https://help.kusto.windows.net/Samples -->
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

|resourceName|totalslices|Sperr Zeit|releasetime|previouslocktime|
|---|---|---|---|---|
|Pipelinescheduler<br>|27|2016-02-17 08:40:00.0000000|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|Pipelinescheduler<br>|27|2016-02-17 08:40:01.0000000|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|Pipelinescheduler<br>|27|2016-02-17 08:40:01.0000000|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|Pipelinescheduler<br>|27|2016-02-17 08:41:00.0000000|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|Pipelinescheduler<br>|27|2016-02-17 08:41:01.0000000|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|


in diesem Beispiel für einen gelockerten Modus: totalslices erweiterte Spalten müssen den Typ Long aufweisen, aber in der analysierten Zeichenfolge weist Sie den Wert nonvalidlongvalue auf.
die erweiterte releasetime-Spalte weist das gleiche Problem auf. der Wert nonvaliddatetime kann nicht als DateTime-Wert analysiert werden.
in diesem Fall erhalten diese beiden erweiterten Spalten den Wert NULL, während die anderen Werte (wie slicenenumber) immer noch die richtigen Werte abrufen.

die Verwendung von Kind = Simple für die gleiche Abfrage gibt für alle erweiterten Spalten NULL an, da Sie für erweiterte Spalten strikt ist (Dies ist der Unterschied zwischen dem gelockerten und dem einfachen Modus, im gelockerten Modus können erweiterte Spalten teilweise abgeglichen werden).

<!-- csl: https://help.kusto.windows.net/Samples -->
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

|resourceName|totalslices|slicennummer|Sperr Zeit|releasetime|previoulocktime|
|---|---|---|---|---|---|
|Pipelinescheduler|27|15|02/17/2016 08:40:00||2016-02-17 08:39:00.0000000|
|Pipelinescheduler|27|23|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|Pipelinescheduler||20|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|Pipelinescheduler||16|02/17/2016 08:41:00 (17.02.2016)|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|Pipelinescheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|
