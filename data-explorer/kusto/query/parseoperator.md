---
title: 'parse-Operator: Azure Data Explorer'
description: In diesem Artikel wird der parse-Operator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: a9da3735df9299b387188157bbae3d561f5de631
ms.sourcegitcommit: f20619fac91f9bb2e6507cac10d41fb8425218e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/29/2020
ms.locfileid: "97811768"
---
# <a name="parse-operator"></a>parse-Operator

Wertet einen Zeichenfolgenausdruck aus und analysiert dessen Wert in eine oder mehrere berechnete Spalten. Die berechneten Spalten verfügen über NULL-Werte für nicht erfolgreich analysierte Zeichenfolgen. Wenn keine Zeilen verwendet werden müssen, bei denen die Analyse nicht erfolgreich ist, sollten Sie den [Operator „parse-where“](parsewhereoperator.md) bevorzugen.

```kusto
T | parse Text with "ActivityName=" name ", ActivityType=" type
```

## <a name="syntax"></a>Syntax

*T* `| parse` [`kind=regex` [`flags=regex_flags`] |`simple`|`relaxed`] *Expression* `with` `*` (*StringConstant* *ColumnName* [`:` *ColumnType*]) `*`...

## <a name="arguments"></a>Argumente

* *T*: Die Eingabetabelle.
* kind:

    * simple (Standardwert): StringConstant ist ein regulärer Zeichenfolgenwert, und die Übereinstimmung ist „strict“. Alle Zeichenfolgentrennzeichen sollten in der analysierten Zeichenfolge vorkommen, und alle erweiterten Spalten müssen mit den erforderlichen Typen übereinstimmen.
        
    * regex: StringConstant kann ein regulärer Ausdruck sein, und die Übereinstimmung ist „strict“. Alle Zeichenfolgentrennzeichen, die für diesen Modus reguläre Ausdrücke sein können, sollten in der analysierten Zeichenfolge vorkommen, und alle erweiterten Spalten müssen mit den erforderlichen Typen übereinstimmen.
    
    * flags: Flags, die im RegEx-Modus verwendet werden, etwa `U` (Ungreedy), `m` (mehrzeiliger Modus), `s` (Übereinstimmung mit neuer Zeile `\n`), `i` (Groß-/Kleinschreibung wird nicht beachtet) in [RE2-Flags](re2.md).
        
    * relaxed: StringConstant ist ein regulärer Zeichenfolgenwert, und die Übereinstimmung ist „relaxed“. Alle Zeichenfolgentrennzeichen sollten in der analysierten Zeichenfolge vorkommen, aber erweiterte Spalten können teilweise mit den erforderlichen Typen übereinstimmen. Erweiterte Spalten, die nicht den erforderlichen Typen entsprechen, erhalten den Wert NULL.

* *Expression* (Ausdruck): Ein Ausdruck ein, der in eine Zeichenfolge ausgewertet wird.

* *ColumnName:* Der Name einer Spalte, der ein Wert zugewiesen werden soll, extrahiert aus dem Zeichenfolgenausdruck. 
  
* *ColumnType:* Optional. Der Skalarwert, der den Typ angibt, in den der Wert konvertiert werden soll. Der Standardwert ist der `string`-Typ.

## <a name="returns"></a>Gibt zurück

Die Eingabetabelle, erweitert gemäß der Liste der Spalten, die für den Operator bereitgestellt werden.

**Tipps**

* Verwenden Sie [`project`](projectoperator.md), wenn Sie einige Spalten auch löschen oder umbenennen möchten.

* Verwenden Sie * im Muster, um Junkwerte zu überspringen. 

    > [!NOTE] 
    > `*` kann nicht nach einer Spalte vom Typ `string` verwendet werden.

* Das Analysemuster kann mit *ColumnName* beginnen, nicht nur mit *StringConstant*.

* Wenn der analysierte *Ausdruck* nicht vom Typ `string` ist, wird er in den Typ `string` konvertiert.

* Wenn der Regex-Modus verwendet wird, gibt es eine Option zum Hinzufügen von Regex-Flags, um die gesamten in der Analyse verwendeten regulären Ausdrücke zu steuern.

* Im Regex-Modus übersetzt die Analyse das Muster in einen regulären Ausdruck. Verwenden Sie [RE2-Syntax](re2.md), um den Abgleich durchzuführen, und nummerierte erfasste Gruppen, die intern verarbeitet werden.
    Zum Beispiel:

    ```kusto
    parse kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    In der Analyseanweisung ist der reguläre Ausdruck, der intern von der Analyse generiert wird, `.*?<regex1>(.*?)<regex2>(\-\d+)`.
        
    * `*` wurde in `.*?` übersetzt.
        
    * `string` wurde in `.*?` übersetzt.
        
    * `long` wurde in `\-\d+` übersetzt.

## <a name="examples"></a>Beispiele

Der `parse`-Operator bietet eine optimierte Möglichkeit zum `extend` einer Tabelle, indem mehrere `extract`-Anwendungen für denselben `string`-Ausdruck verwendet werden. Dieses Ergebnis ist nützlich, wenn die Tabelle eine `string`-Spalte aufweist, die mehrere Werte enthält, die Sie in einzelne Spalten aufteilen möchten. Beispielsweise eine Spalte, die von einer Entwicklerablaufverfolgungs-Anweisung ("`printf`"/"`Console.WriteLine`") erstellt wurde.

Nehmen Sie im folgenden Beispiel an, dass die Spalte `EventText` der Tabelle `Traces` Zeichenfolgen der Form `Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})` enthält.
Durch den Vorgang wird die Tabelle um sechs Spalten erweitert: `resourceName`, `totalSlices`, `sliceNumber`, `lockTime `, `releaseTime` und `previousLockTime`. 

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

|resourceName|totalSlices|sliceNumber|lockTime|ReleaseTime|previousLockTime|
|---|---|---|---|---|---|
|PipelineScheduler|27|15|02/17/2016 08:40:00|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|PipelineScheduler|27|23|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler|27|16|02/17/2016 08:41:00 (17.02.2016)|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|

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

|resourceName|sliceNumber|lockTime|ReleaseTime|previousLockTime|
|---|---|---|---|---|
|PipelineScheduler|15|02/17/2016 08:40:00, |02/17/2016 08:40:00, |2016-02-17 08:39:00.0000000|
|PipelineScheduler|23|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|PipelineScheduler|20|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|PipelineScheduler|16|02/17/2016 08:41:00, |02/17/2016 08:41:00, |2016-02-17 08:40:00.0000000|
|PipelineScheduler|22|02/17/2016 08:41:01, |02/17/2016 08:41:00, |2016-02-17 08:40:01.0000000|

**Für den Regex-Modus mit Regex-Flags**

Wenn Sie nur resourceName abrufen möchten, verwenden Sie die folgende Abfrage:

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
|PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01|
|PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00|
|PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01|
|PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00|
|PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00|

Sie erhalten nicht die erwarteten Ergebnisse, da der Standardmodus „greedy“ ist.
Wenn Sie über einige Datensätze verfügen, die *resourceName* manchmal in Kleinbuchstaben und manchmal in Großbuchstaben enthalten, erhalten Sie möglicherweise NULL-Werte für einige Werte.

Um das gewünschte Ergebnis abzurufen, führen Sie die Abfrage mit `U` („non-greedy“) aus, und deaktivieren Sie `i`-Regex-Flags, für die zwischen Groß-/Kleinschreibung unterschieden wird.

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
|PipelineScheduler|
|PipelineScheduler|
|PipelineScheduler|
|PipelineScheduler|
|PipelineScheduler|

Wenn die analysierte Zeichenfolge Zeilenvorschübe enthält, verwenden Sie das Flag `s`, um den Text zu analysieren.

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

|resourceName|totalSlices|lockTime|ReleaseTime|previousLockTime|
|---|---|---|---|---|
|PipelineScheduler<br>|27|2016-02-17 08:40:00.0000000|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:40:01.0000000|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:40:01.0000000|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:41:00.0000000|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:41:01.0000000|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|

**Relaxed-Modus**

In diesem Beispiel für den Relaxed-Modus muss die erweiterte Spalte *totalSlices* den Typ `long` aufweisen. In der analysierten Zeichenfolge weist sie jedoch den Wert *nonValidLongValue* auf.
In der erweiterten Spalte *releaseTime* kann der Wert *nonValidDateTime* nicht als *datetime* analysiert werden.
Diese beiden erweiterten Spalten erhalten den Wert NULL, während die anderen (z. B. *sliceNumber*) weiterhin die richtigen Werte erhalten.

Wenn Sie die Option *kind = simple* für dieselbe unten gezeigte Abfrage verwenden, erhalten Sie für alle erweiterten Spalten den Wert NULL. Diese Option ist für erweiterte Spalten „strict“ und stellt den Unterschied zwischen dem Relaxed- und dem Simple-Modus dar.

 > [!NOTE] 
 > Im Relaxed-Modus können erweiterte Spalten teilweise abgeglichen werden.

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

|resourceName|totalSlices|sliceNumber|lockTime|ReleaseTime|previousLockTime|
|---|---|---|---|---|---|
|PipelineScheduler|27|15|02/17/2016 08:40:00||2016-02-17 08:39:00.0000000|
|PipelineScheduler|27|23|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|PipelineScheduler||20|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|PipelineScheduler||16|02/17/2016 08:41:00 (17.02.2016)|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|
 
