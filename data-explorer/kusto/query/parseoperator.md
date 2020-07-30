---
title: 'Analyse Operator: Azure-Daten-Explorer'
description: In diesem Artikel wird der Analyse Operator in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f9dc6e49e9e3d04aadb5aecf8507b7132d8a366a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346319"
---
# <a name="parse-operator"></a>parse-Operator

Wertet einen Zeichenfolgenausdruck aus und analysiert dessen Wert in eine oder mehrere berechnete Spalten. Die berechneten Spalten verfügen über Nullen für nicht erfolgreich analysierte Zeichen folgen.
Weitere Informationen finden Sie unter dem [Operator "Bise-WHERE](parsewhereoperator.md)".

```kusto
T | parse Text with "ActivityName=" name ", ActivityType=" type
```

## <a name="syntax"></a>Syntax

*T* `| parse` [ `kind=regex` [ `flags=regex_flags` ] | `simple` | `relaxed` ]- *Ausdruck* `with` `*` (*StringConstant* *ColumnName* [ `:` *ColumnType*]) `*` ...

## <a name="arguments"></a>Argumente

* *T*: die Eingabe Tabelle.
* Art

    * Simple (Standard): StringConstant ist ein regulärer Zeichen folgen Wert, und die Entsprechung ist "Strict". Alle Zeichen folgen Trennzeichen sollten in der analysierten Zeichenfolge angezeigt werden, und alle erweiterten Spalten müssen mit den erforderlichen Typen identisch sein.
        
    * Regex: "StringConstant" kann ein regulärer Ausdruck sein, und die Entsprechung ist "Strict". Alle Zeichen folgen Trennzeichen, die ein Regex für diesen Modus sein können, sollten in der analysierten Zeichenfolge angezeigt werden, und alle erweiterten Spalten müssen mit den erforderlichen Typen identisch sein.
    
    * Flags: Flags, die im Regex-Modus verwendet werden sollen, wie `U` (ungierig), `m` (mehrzeiligen Modus), `s` (Übereinstimmung mit neuer Zeile) `\n` , (ohne Beachtung `i` der Groß-/Kleinschreibung) in [RE2-Flags](re2.md).
        
    * gelockert: StringConstant ist ein regulärer Zeichen folgen Wert, und die Übereinstimmung wird gelockert. Alle Zeichen folgen Trennzeichen sollten in der analysierten Zeichenfolge angezeigt werden, aber erweiterte Spalten können teilweise mit den erforderlichen Typen verglichen werden. Erweiterte Spalten, die nicht den erforderlichen Typen entsprechen, erhalten den Wert NULL.

* *Expression*: ein Ausdruck, der zu einer Zeichenfolge ausgewertet wird.

* *ColumnName:* Der Name einer Spalte, der ein Wert zugewiesen werden soll, der aus dem Zeichen folgen Ausdruck extrahiert wird. 
  
* *ColumnType:* Optionale. Der Skalarwert, der den Typ angibt, in den der Wert konvertiert werden soll. Der Standardwert ist der- `string` Typ.

## <a name="returns"></a>Rückgabe

Die Eingabe Tabelle, erweitert entsprechend der Liste der Spalten, die dem Operator bereitgestellt werden.

**Tipps**

* Verwenden [`project`](projectoperator.md) Sie, wenn Sie auch einige Spalten löschen oder umbenennen möchten.

* Verwenden Sie * im Muster, um Junk-Werte zu überspringen. 

    > [!NOTE] 
    > Der `*` kann nicht nach einer `string` Typspalte verwendet werden.

* Das Analyse Muster kann mit *ColumnName* beginnen und nicht nur mit *StringConstant*.

* Wenn der analysierte *Ausdruck* nicht vom Typ `string` ist, wird er in den Typ konvertiert `string` .

* Wenn der Regex-Modus verwendet wird, gibt es eine Option zum Hinzufügen von Regex-Flags, um den gesamten in der Analyse verwendeten Regex-Wert zu steuern.

* Im Regex-Modus übersetzt die Analyse das Muster in ein Regex. Verwenden Sie die [RE2-Syntax](re2.md) , um den Abgleich durchzuführen, und verwenden Sie nummerierte, intern behandelte Gruppen.
    Zum Beispiel:

    ```kusto
    parse kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    In der Analyse Anweisung ist der Regex, der intern von der Analyse generiert wird, `.*?<regex1>(.*?)<regex2>(\-\d+)` .
        
    * `*`wurde in übersetzt `.*?` .
        
    * `string`wurde in übersetzt `.*?` .
        
    * `long`wurde in übersetzt `\-\d+` .

## <a name="examples"></a>Beispiele

Der- `parse` Operator bietet eine optimierte Methode für `extend` eine Tabelle, indem mehrere `extract` Anwendungen für denselben `string` Ausdruck verwendet werden. Dieses Ergebnis ist hilfreich, wenn die Tabelle eine `string` Spalte enthält, die mehrere Werte enthält, die Sie in einzelne Spalten unterbrechen möchten. Beispielsweise eine Spalte, die von einer Entwickler `printf` -Trace-Anweisung (""/" `Console.WriteLine` ") erstellt wurde.

Nehmen Sie im folgenden Beispiel an, dass die Spalte `EventText` der Tabelle Zeichen folgen `Traces` des Formulars enthält `Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})` .
Durch den-Vorgang wird die Tabelle mit sechs Spalten erweitert: `resourceName` , `totalSlices` , `sliceNumber` , `lockTime ` , `releaseTime` , `previousLockTime` , `Month` und `Day` . 

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
| parse EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

|resourceName|totalslices|slicennummer|Sperr Zeit|releasetime|previouslocktime|
|---|---|---|---|---|---|
|Pipelinescheduler|27|15|02/17/2016 08:40:00|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|Pipelinescheduler|27|23|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|Pipelinescheduler|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|Pipelinescheduler|27|16|02/17/2016 08:41:00 (17.02.2016)|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|Pipelinescheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|

**Für den Regex-Modus**

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
| parse kind = regex EventText with "(.*?)[a-zA-Z]*=" resourceName @", totalSlices=\s*\d+\s*.*?sliceNumber=" sliceNumber:long  ".*?(previous)?lockTime=" lockTime ".*?releaseTime=" releaseTime ".*?previousLockTime=" previousLockTime:date "\\)"  
| project resourceName , sliceNumber , lockTime , releaseTime , previousLockTime
```

|resourceName|slicennummer|Sperr Zeit|releasetime|previouslocktime|
|---|---|---|---|---|
|Pipelinescheduler|15|02/17/2016 08:40:00, |02/17/2016 08:40:00, |2016-02-17 08:39:00.0000000|
|Pipelinescheduler|23|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|Pipelinescheduler|20|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|Pipelinescheduler|16|02/17/2016 08:41:00, |02/17/2016 08:41:00, |2016-02-17 08:40:00.0000000|
|Pipelinescheduler|22|02/17/2016 08:41:01, |02/17/2016 08:41:00, |2016-02-17 08:40:01.0000000|

**Für den Regex-Modus mit Regex-Flags**

Wenn Sie nur den resourceName erhalten möchten, verwenden Sie die folgende Abfrage:

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

Die erwarteten Ergebnisse werden nicht angezeigt, da der Standardmodus gieriger ist.
Wenn Sie über einige Datensätze verfügen, bei denen *resourceName* manchmal als Kleinbuchstaben und manchmal auch als Großbuchstaben angezeigt wird, erhalten Sie möglicherweise NULL-Werte für einige Werte.

Um das gewünschte Ergebnis zu erhalten, führen Sie die Abfrage mit dem nicht gierigen aus, und deaktivieren Sie die Unterscheidung nach `U` Groß-/Kleinschreibung `i` .

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

Wenn die analysierte Zeichenfolge Zeilenumbrüche enthält, verwenden Sie das-Flag `s` , um den Text zu analysieren.

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

**Gelockerter Modus**

In diesem Beispiel für den gelockerten Modus müssen die erweiterte *totalslices* -Spalte den Typ aufweisen `long` . In der analysierten Zeichenfolge weist Sie jedoch den Wert *nonvalidlongvalue*auf.
In der erweiterten *releasetime* -Spalte kann der Wert *nonvaliddatetime* nicht als *DateTime*-Wert analysiert werden.
Diese beiden erweiterten Spalten erhalten den Wert NULL, während die anderen, z. b. *slicenenumber*, weiterhin die richtigen Werte erhalten.

Wenn Sie Options *Kind = Simple* für dieselbe Abfrage verwenden, erhalten Sie für alle erweiterten Spalten den Wert NULL. Diese Option ist für erweiterte Spalten streng und ist der Unterschied zwischen dem gelockerten und dem einfachen Modus.

 > [!NOTE] 
 > Im gelockerten Modus können erweiterte Spalten teilweise abgeglichen werden.

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
| parse kind=relaxed EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *
| project-away EventText
```

|resourceName|totalslices|slicennummer|Sperr Zeit|releasetime|previouslocktime|
|---|---|---|---|---|---|
|Pipelinescheduler|27|15|02/17/2016 08:40:00||2016-02-17 08:39:00.0000000|
|Pipelinescheduler|27|23|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|Pipelinescheduler||20|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|Pipelinescheduler||16|02/17/2016 08:41:00 (17.02.2016)|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|Pipelinescheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|
 
