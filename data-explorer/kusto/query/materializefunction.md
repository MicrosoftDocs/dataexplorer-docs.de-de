---
title: Materialize ()-Azure Daten-Explorer
description: Dieser Artikel beschreibt die Funktion "Materialize ()" in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/06/2020
ms.openlocfilehash: 0e08857e01ffa3da1bcb23d16d1df4908336f76f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346863"
---
# <a name="materialize"></a>materialize()

Ermöglicht das Zwischenspeichern eines Unterabfrage Ergebnisses während der Abfrage Ausführung auf eine Weise, mit der andere Unterabfragen auf das Teilergebnis verweisen können.
 
## <a name="syntax"></a>Syntax

`materialize(`*expression*`)`

## <a name="arguments"></a>Argumente

* *Ausdruck*: der tabellarische Ausdruck, der während der Abfrage Ausführung ausgewertet und zwischengespeichert werden soll.

> [!NOTE]
> "Materialize" hat eine Cache Größenbeschränkung von **5 GB**. Diese Beschränkung erfolgt pro Cluster Knoten und ist für alle gleichzeitig ausgeführten Abfragen gegenseitig. Wenn eine Abfrage verwendet `materialize()` und der Cache keine weiteren Daten enthalten kann, wird die Abfrage mit einem Fehler abgebrochen.

>[!TIP]
>
>* Pushen Sie alle möglichen Operatoren, die das materialisierte DataSet verringern, und behalten Sie die Semantik der Abfrage bei. Verwenden Sie z. b. Filter, oder projizieren Sie nur erforderliche Spalten.
>* Verwenden Sie materialisieren mit Join oder Union, wenn ihre Operanden gegenseitige Unterabfragen verfügen, die einmal ausgeführt werden können. Z. b. Join/Union Fork Legs. Siehe [Beispiel für die Verwendung des Join-Operators](#examples-of-query-performance-improvement).
>* Die Materialisierung kann nur in Let-Anweisungen verwendet werden, wenn Sie dem zwischengespeicherten Ergebnis einen Namen geben. Siehe [Beispiel für die Verwendung von Let-Anweisungen](#examples-of-using-materialize)).

## <a name="examples-of-query-performance-improvement"></a>Beispiele für die Verbesserung der Abfrageleistung

Im folgenden Beispiel wird gezeigt `materialize()` , wie verwendet werden kann, um die Leistung der Abfrage zu verbessern.
Der Ausdruck `_detailed_data` wird mithilfe der `materialize()` -Funktion definiert und wird daher nur einmal berechnet.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let _detailed_data = materialize(StormEvents | summarize Events=count() by State, EventType);
_detailed_data
| summarize TotalStateEvents=sum(Events) by State
| join (_detailed_data) on State
| extend EventPercentage = Events*100.0 / TotalStateEvents
| project State, EventType, EventPercentage, Events
| top 10 by EventPercentage
```

|Zustand|EventType|Eventprozent|Events|
|---|---|---|---|
|Hawaii-Wasser|Wasser Spout|100|2|
|Lake Ontario|Seeman Gewitter Wind|100|8|
|Golf von Alaska|Wasser Spout|100|4|
|Atlantik, Norden|Seeman Gewitter Wind|95.2127659574468|179|
|Lake-Galerie|Seeman Gewitter Wind|92.5925925925926|25|
|E Pazifik|Wasser Spout|90|9|
|Lake Michigan|Seeman Gewitter Wind|85.1648351648352|155|
|Lake-Huron|Seeman Gewitter Wind|79.3650793650794|50|
|Golf von Mexiko|Seeman Gewitter Wind|71.7504332755633|414|
|IRONMAN|Hoch Surfen|70.0218818380744|320|


Im folgenden Beispiel wird eine Reihe von Zufallszahlen generiert und berechnet: 
* Anzahl der unterschiedlichen Werte im Satz ( `Dcount` )
* die obersten drei Werte in der Gruppe. 
* die Summe aller Werte in der Gruppe. 
 
Dieser Vorgang kann mithilfe von [Batches](batches.md) und materialisieren ausgeführt werden:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let randomSet = 
    materialize(
        range x from 1 to 3000000 step 1
        | project value = rand(10000000));
randomSet | summarize Dcount=dcount(value);
randomSet | top 3 by value;
randomSet | summarize Sum=sum(value)
```

Resultset 1:  

|DCount|
|---|
|2578351|

Resultset 2: 

|value|
|---|
|9999998|
|9999998|
|9999997|

Resultset 3: 

|SUM|
|---|
|15002960543563|

## <a name="examples-of-using-materialize"></a>Beispiele für die Verwendung von Materialize ()

> [!TIP]
> Materialisieren Sie die Spalte bei der Erfassungs Zeit, wenn die meisten Ihrer Abfragen Felder aus dynamischen Objekten in Millionen von Zeilen extrahieren.

`let`Verwenden Sie die [Materialize ()-Funktion](./materializefunction.md), um die-Anweisung mit einem Wert zu verwenden, den Sie mehrmals verwenden. Versuchen Sie, alle möglichen Operatoren zu übermitteln, die das materialisierte DataSet verringern, und behalten Sie trotzdem die Semantik der Abfrage bei. Verwenden Sie z. b. Filter, oder projizieren Sie nur erforderliche Spalten.

```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource1)), (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource2))
```

Der Filter für `Text` ist gegenseitig und kann auf den materialisieren-Ausdruck geschoben werden.
Die Abfrage benötigt nur die Spalten `Timestamp` , `Text` , `Resource1` und `Resource2` . Projizieren Sie diese Spalten in den materialisierten Ausdruck.
    
```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text !has "somestring"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | summarize dcount(Resource1)), (materializedData
    | summarize dcount(Resource2))
```
    
Wenn die Filter nicht identisch sind, wie in der folgenden Abfrage:  

```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
 ```

Wenn der kombinierte Filter das materialisierte Ergebnis drastisch reduziert, kombinieren Sie beide Filter für das materialisierte Ergebnis mit einem logischen `or` Ausdruck wie in der folgenden Abfrage. Behalten Sie jedoch die Filter in den einzelnen Union-Beinen bei, um die Semantik der Abfrage beizubehalten.
     
```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text has "String1" or Text has "String2"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
```
    
