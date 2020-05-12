---
title: 'diffpatterns-Plug-in: Azure-Daten-Explorer'
description: Dieser Artikel beschreibt das diffpatterns-Plug-in in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b3dece66f3bafae989643afd418557aeaaa7d746
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225036"
---
# <a name="diff-patterns-plugin"></a>Vergleichs Muster-Plugin

Vergleicht zwei Datasets derselben Struktur und findet Muster diskreter Attribute (Dimensionen), die Unterschiede zwischen den beiden Datasets charakterisieren.
 `Diffpatterns`wurde entwickelt, um Fehler zu analysieren (z. b. durch Vergleichen von Fehlern mit nicht-Fehlern in einem bestimmten Zeitrahmen), kann jedoch möglicherweise Unterschiede zwischen zwei Datensätzen derselben Struktur finden. 

```kusto
T | evaluate diffpatterns(splitColumn)
```


**Syntax**

`T | evaluate diffpatterns(SplitColumn, SplitValueA, SplitValueB [, WeightColumn, Threshold, MaxDimensions, CustomWildcard, ...])` 

**Erforderliche Argumente**

* SplitColumn - *column_name*

    Weist den Algorithmus an, wie die Abfrage in Datasets geteilt wird. Gemäß den angegebenen Werten für die Argumente SplitValueA und SplitValueB (siehe unten) teilt der Algorithmus die Abfrage in zwei Datasets („A“ und „B“) und analysiert ihre Unterschiede. Die split-Spalte muss mindestens zwei eindeutige Werte enthalten.

* SplitValueA - *string*

    Eine Zeichenfolgendarstellung für einen der Werte in SplitColumn, der angegeben wurde. Alle Zeilen, die diesen Wert in der SplitColumn aufweisen, gelten als DataSet "A".

* SplitValueB - *string*

    Eine Zeichenfolgendarstellung für einen der Werte in SplitColumn, der angegeben wurde. Alle Zeilen, die diesen Wert in der SplitColumn aufweisen, gelten als DataSet "B".

    Ein Beispiel: `T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure") `

**Optionale Argumente**

Alle anderen Argumente sind optional, aber sie müssen wie unten angegeben sortiert werden. Um anzugeben, dass der Standardwert verwendet werden sollte, können Sie eine Tilde („~“) angeben (siehe Beispiele unten).

* WeightColumn - *column_name*

    Berücksichtigt jede Zeile in der Eingabe gemäß dem angegebenen Gewicht (standardmäßig verfügt jede Spalte über eine Gewichtung von „1“). Das-Argument muss ein Name einer numerischen Spalte sein (z. b `int` `long` .,, `real` ).
    Eine übliche Nutzung einer Gewichtungsspalte besteht darin, die Stichprobenerstellung oder die Bucket-Zuordnung/Aggregation der Daten zu berücksichtigen, die bereits in die einzelnen Zeilen eingebettet sind.
    
    Ein Beispiel: `T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", sample_Count) `

* Schwellenwert-0,015 < *Double* < 1 [Standardwert: 0,05]

    Legt die minimale Musterdifferenz (Verhältnis) zwischen den beiden Datasets fest.

    Beispiel: `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", 0.04)`

* Maxdimensions-0 < *int* [Standardwert: unbegrenzt]

    Legt die maximale Anzahl nicht korrelierter Dimensionen pro Ergebnis Muster fest. Wenn Sie ein Limit angeben, verringern Sie die Abfrage Laufzeit.

    Beispiel: `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", "~", 3)`

* Customwildcard- *"any-value-per-Type"*

    Legt den Platzhalterwert für einen bestimmten Typ in der Ergebnistabelle fest, der angibt, dass das aktuelle Muster keine Einschränkung für diese Spalte besitzt.
    Der Standard ist „null“, da der Standard eine leere Zeichenfolge ist. Wenn der Standardwert ein in den Daten funktionierender Wert ist, sollte ein anderer Platzhalter Wert verwendet werden (z `*` . b.).
    Unten finden Sie ein Beispiel hierzu.

    Ein Beispiel: `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", "~", "~", int(-1), double(-1), long(0), datetime(1900-1-1))`

**Rückgabe**

`Diffpatterns`Gibt einen kleinen Satz von Mustern zurück, die verschiedene Teile der Daten in den beiden Mengen erfassen (d. h. ein Muster, das einen großen Prozentsatz der Zeilen im ersten DataSet und einen niedrigen Prozentsatz der Zeilen in der zweiten Menge erfasst). Jede Zeile in den Ergebnissen steht für ein Muster.

Das Ergebnis von `diffpatterns` gibt die folgenden Spalten zurück:

* Segmentid: die Identität, die dem Muster in der aktuellen Abfrage zugewiesen ist (Hinweis: IDs sind bei sich wiederholenden Abfragen nicht garantiert identisch).

* Count: die Anzahl der Zeilen, die vom Muster in Set a aufgezeichnet werden (Set a ist die Entsprechung von `where tostring(splitColumn) == SplitValueA` ).

* Zähltb: die Anzahl der Zeilen, die vom Muster in Set b aufgezeichnet werden (Set b ist das Äquivalent von `where tostring(splitColumn) == SplitValueB` ).

* Prozentua: der Prozentsatz der Zeilen in festgelegt durch das Muster (100,0 * count/count (Seta)).

* Anteilswert: der Prozentsatz der Zeilen in Set B, der durch das Muster aufgezeichnet wird (100,0 * count/count (SeTB)).

* Prozentudiffab: der absolute Prozentpunkt Unterschied zwischen A und B (| Prozentuv (prozentuv) |) ist das wichtigste Maß der Bedeutung von Mustern bei der Beschreibung des Unterschieds zwischen den beiden Sätzen.

* Rest der Spalten: sind das ursprüngliche Schema der Eingabe und beschreiben das Muster, jede Zeile (Muster) leitet die Schnittmenge der nicht Platzhalter Werte der Spalten (Entsprechung von `where col1==val1 and col2==val2 and ... colN=valN` für jeden nicht Platzhalter Wert in der Zeile) erneut.

Bei jedem Muster enthalten Spalten, die nicht im Muster (d. h. ohne Einschränkung für einen bestimmten Wert) festgelegt sind, einen Platzhalter Wert, der standardmäßig NULL ist. Informationen dazu, wie Platzhalter manuell geändert werden können, finden Sie im Abschnitt "Argumente" unten.

* Hinweis: die Muster sind häufig nicht verschieden. Sie können sich überlappen und in der Regel nicht alle ursprünglichen Zeilen abdecken. Einige Zeilen fallen ggf. nicht in eines der Muster.


**Tipps**

Verwenden Sie [Where](./whereoperator.md) und [Project](./projectoperator.md) in der eingabepipe, um die Daten auf die für Sie interessanten Daten zu reduzieren.

Wenn Sie eine interessante Zeile finden, können Sie dafür einen Drilldown durchführen, indem Sie die jeweiligen Werte dem `where` -Filter hinzufügen.

* Hinweis: `diffpatterns` zielt darauf ab, bedeutende Muster zu finden (die Teile des Daten Unterschieds zwischen den Sätzen erfassen) und nicht für zeilenweise Unterschiede gedacht ist.

**Beispiel**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , 1 , 0)
| project State , EventType , Source , Damage, DamageCrops
| evaluate diffpatterns(Damage, "0", "1" )
```

|SegmentId|CountA|CountB|PercentA|PercentB|PercentDiffAB|Bundesland/Kanton|EventType|`Source`|DamageCrops|
|---|---|---|---|---|---|---|---|---|---|
|0|2278|93|49,8|7.1|42,7||Hagel||0|
|1|779|512|17,03|39,08|22,05||Sturm|||
|2|1.098|118|24,01|9,01|15|||Ausgebildeter „Spotter“|0|
|3|136|158|2,97|12,06|9,09|||Zeitung||
|4|359|214|7,85|16,34|8,49||Überschwemmung|||
|5|50|122|1,09|9,31|8,22|IOWA||||
|6|655|279|14,32|21,3|6,98|||Strafverfolgungsbehörden||
|7|150|117|3,28|8,93|5,65||Hochwasser|||
|8|362|176|7,91|13,44|5,52|||Katastrophenschutz||
