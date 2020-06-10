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
ms.openlocfilehash: f5ea896d4701aa5aec1b22c1ff20853aea18f065
ms.sourcegitcommit: be1bbd62040ef83c08e800215443ffee21cb4219
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664941"
---
# <a name="materialize"></a>materialize()

Ermöglicht das Zwischenspeichern eines Unterabfrage Ergebnisses während der Abfrage Ausführung auf eine Weise, mit der andere Unterabfragen auf das Teilergebnis verweisen können.
 
**Syntax**

`materialize(`*expression*`)`

**Argumente**

* *Ausdruck*: der tabellarische Ausdruck, der während der Abfrage Ausführung ausgewertet und zwischengespeichert werden soll.

**Tipps**

* Verwenden Sie materialisieren mit Join oder Union, wenn ihre Operanden gegenseitige Unterabfragen verfügen, die einmal ausgeführt werden können. Weitere Informationen finden Sie in den folgenden Beispielen.

* Auch hilfreich in Szenarien, in denen Verzweigungsabschnitte für „join/union“ benötigt werden.

* Die Materialisierung kann nur in Let-Anweisungen verwendet werden, wenn Sie dem zwischengespeicherten Ergebnis einen Namen geben.

**Hinweis**

* "Materialize" hat eine Cache Größenbeschränkung von **5 GB**. 
  Diese Beschränkung erfolgt pro Cluster Knoten und ist für alle gleichzeitig ausgeführten Abfragen gegenseitig.
  Wenn eine Abfrage verwendet `materialize()` und der Cache keine weiteren Daten enthalten kann, wird die Abfrage mit einem Fehler abgebrochen.

**Beispiele**

Im folgenden Beispiel wird gezeigt `materialize()` , wie verwendet werden kann, um die Leistung der Abfrage zu verbessern.
Der Ausdruck `_detailed_data` wird mithilfe der `materialize()` Funktion definiert und wird daher nur einmal berechnet.

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

|Zustand|EventType|Eventprozent|Ereignisse|
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
* Anzahl der unterschiedlichen Werte im Satz (DCount)
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
