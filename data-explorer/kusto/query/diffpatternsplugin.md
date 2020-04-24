---
title: diffpatterns-Plugin - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird das diffpatterns-Plugin in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fc4d1c7441e02eeeb1d90e1f8c0f2a521e3793da
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515996"
---
# <a name="diffpatterns-plugin"></a>diffpatterns Plugin

```kusto
T | evaluate diffpatterns(splitColumn)
```
Vergleicht zwei Datensätze mit derselben Struktur und findet Muster diskreter Attribute (Dimensionen), die Unterschiede zwischen den beiden Datensätzen charakterisieren. Diffpatterns wurde als Hilfe bei der Analyse von Fehlern entwickelt (z.B. per Vergleich von Fehlern mit Nicht-Fehlern in einem bestimmten Zeitraum). Es ist damit ggf. aber auch möglich, Unterschiede zwischen zwei beliebigen Datasets mit derselben Struktur zu ermitteln. 

**Syntax**

`T | evaluate diffpatterns(`SplitColumn, SplitValueA, SplitValueB [, WeightColumn, Threshold, MaxDimensions, CustomWildcard, ...]`)` 

**Erforderliche Argumente**

* SplitColumn - *column_name*

    Weist den Algorithmus an, wie die Abfrage in Datasets geteilt wird. Gemäß den angegebenen Werten für die Argumente SplitValueA und SplitValueB (siehe unten) teilt der Algorithmus die Abfrage in zwei Datasets („A“ und „B“) und analysiert ihre Unterschiede. Die split-Spalte muss mindestens zwei eindeutige Werte enthalten.

* SplitValueA - *string*

    Eine Zeichenfolgendarstellung für einen der Werte in SplitColumn, der angegeben wurde. Alle Zeilen, die diesen Wert in SplitColumn enthalten, werden als Dataset „A“ angesehen.

* SplitValueB - *string*

    Eine Zeichenfolgendarstellung für einen der Werte in SplitColumn, der angegeben wurde. Alle Zeilen, die diesen Wert in ihrer SplitColumn haben, werden als Datensatz "B" betrachtet.

    Ein Beispiel: `T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure") `

**Optionale Argumente**

Alle anderen Argumente sind optional, aber sie müssen wie unten angegeben sortiert werden. Um anzugeben, dass der Standardwert verwendet werden sollte, können Sie eine Tilde („~“) angeben (siehe Beispiele unten).

* WeightColumn - *column_name*

    Berücksichtigt jede Zeile in der Eingabe gemäß dem angegebenen Gewicht (standardmäßig verfügt jede Spalte über eine Gewichtung von „1“). Das Argument muss ein Name einer numerischen Spalte sein (z.B. int, long, real).
    Eine übliche Nutzung einer Gewichtungsspalte besteht darin, die Stichprobenerstellung oder die Bucket-Zuordnung/Aggregation der Daten zu berücksichtigen, die bereits in die einzelnen Zeilen eingebettet sind.
    
    Ein Beispiel: `T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", sample_Count) `

* Schwellenwert - 0,015 < *doppel* < 1 [Standard: 0,05]

    Legt die minimale Musterdifferenz (Verhältnis) zwischen den beiden Datasets fest.

    Beispiel: `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", 0.04)`

* MaxDimensions - 0 < *int* [Standard: unbegrenzt]

    Legt die maximale Anzahl von nicht korrelierten Dimensionen pro Ergebnismuster fest, und es wird ein Grenzwert angegeben, um die Laufzeit von Abfragen zu verringern.

    Beispiel: `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", "~", 3)`

* CustomWildcard - *"any-value-per-type"*

    Legt den Platzhalterwert für einen bestimmten Typ in der Ergebnistabelle fest, der angibt, dass das aktuelle Muster keine Einschränkung für diese Spalte besitzt.
    Der Standard ist „null“, da der Standard eine leere Zeichenfolge ist. Wenn der Standardwert ein tragfähiger Wert in den Daten ist, `*`sollte ein anderer Platzhalterwert verwendet werden (z. B. ).
    Unten finden Sie ein Beispiel hierzu.

    Ein Beispiel: `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", "~", "~", int(-1), double(-1), long(0), datetime(1900-1-1))`

**Rückgabe**

Diffpatterns gibt eine (normalerweise kleine) Gruppe von Mustern zurück, in denen unterschiedliche Teile der Daten in den beiden Datasets erfasst werden (also ein Muster, bei dem ein größerer Prozentsatz der Zeilen im ersten Dataset und ein geringerer Prozentsatz der Zeilen im zweiten Dataset erfasst wird). Jede Zeile in den Ergebnissen steht für ein Muster.

Das Ergebnis von diffpatterns gibt die folgenden Spalten zurück:

* SegmentId: die id, die dem Muster in der aktuellen Abfrage zugewiesen ist (Hinweis: IDs sind bei wiederholten Abfragen nicht garantiert identisch).

* CountA: die Anzahl der Zeilen, die vom Muster in `where tostring(splitColumn) == SplitValueA`Satz A erfasst werden (Satz A entspricht ).

* CountB: die Anzahl der Zeilen, die vom Muster in `where tostring(splitColumn) == SplitValueB`Satz B erfasst werden (Set B entspricht ).

* PercentA: der Prozentsatz der Zeilen in Satz A, der vom Muster erfasst wird ( 100.0 * CountA / count(SetA) ).

* ProzentB: der Prozentsatz der Zeilen in Satz B, die vom Muster erfasst werden ( 100.0 * CountB / count(SetB) ).

* PercentDiffAB: die absolute Prozentpunktdifferenz zwischen A und B ( | PercentA - Prozentb| ) ist das wichtigste Maß für die Signifikanz von Mustern bei der Beschreibung des Unterschieds zwischen den beiden Sätzen.

* Rest der Spalten: sind das ursprüngliche Schema der Eingabe und beschreiben das Muster, jede Zeile (Muster) reresents `where col1==val1 and col2==val2 and ... colN=valN` den Schnittpunkt der Nicht-Wildcard-Werte der Spalten (äquivalent für jeden Nicht-Wildcard-Wert in der Zeile).

Für jedes Muster enthalten Spalten, die nicht im Muster festgelegt sind (d. h. ohne Einschränkung eines bestimmten Werts), einen Platzhalterwert, der standardmäßig null ist (siehe abschnitt Argumente unten, wie Platzhalter manuell geändert werden können).

* Hinweis: Die Muster sind in der Regel nicht unterschiedlich, sie können sich überlappen und decken in der Regel nicht alle ursprünglichen Zeilen ab. Einige Zeilen fallen ggf. nicht in eines der Muster.


**Tipps**

Verwenden Sie [wo](./whereoperator.md) und [projizieren](./projectoperator.md) Sie in der Eingabepipe, um die Daten auf das zu reduzieren, was Sie interessieren.

Wenn Sie eine interessante Zeile finden, können Sie dafür einen Drilldown durchführen, indem Sie die jeweiligen Werte dem `where` -Filter hinzufügen.

* Anmerkung: diffpatterns zielt darauf ab, signifikante Muster zu finden (die Teile der Datendifferenz zwischen den Sätzen erfassen) und ist nicht für Zeilen-für-Zeilen-Unterschiede gedacht.

**Beispiel**

```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , 1 , 0)
| project State , EventType , Source , Damage, DamageCrops
| evaluate diffpatterns(Damage, "0", "1" )
```
|SegmentId|CountA|CountB|PercentA|PercentB|PercentDiffAB|State|EventType|`Source`|DamageCrops|
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

