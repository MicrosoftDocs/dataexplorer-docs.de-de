---
title: diffpatterns_text-Plugin - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird diffpatterns_text-Plugin in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 58f059e2346dfb3f15bff295126a14a97f10f317
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516013"
---
# <a name="diffpatterns_text-plugin"></a>diffpatterns_text Plugin

Vergleicht zwei Datensätze mit Zeichenfolgenwerten und findet Textmuster, die Unterschiede zwischen den beiden Datensätzen charakterisieren.

```kusto
T | evaluate diffpatterns_text(TextColumn, BooleanCondition)
```

Der `diffpatterns_text` gibt einen Satz von Textmustern zurück, die verschiedene Teile der Daten in den beiden Sätzen erfassen `true` (d. h. ein Muster, das einen großen Prozentsatz der Zeilen erfasst, wenn die Bedingung ist, und einen niedrigen Prozentsatz der Zeilen, wenn die Bedingung ist). `false` Die Muster werden aus aufeinander folgenden Token (getrennt durch Leerzeichen) `*` erstellt, wobei ein Token aus der Textspalte oder ein Platzhalter dargestellt wird. Jede Zeile in den Ergebnissen steht für ein Muster.

**Syntax**

`T | evaluate diffpatterns_text(`TextColumn, BooleanCondition [, MinTokens, Threshold , MaxTokens]`)` 

**Erforderliche Argumente**

* TextColumn - *column_name*

    Die zu analysierende Textspalte muss vom Typ String sein.
    
* BooleanCondition - *Boolescher Ausdruck*

    Definiert, wie die beiden Datensatzuntermengen generiert werden, um sie mit der Eingabetabelle zu vergleichen. Der Algorithmus teilt die Abfrage entsprechend der Bedingung in zwei Datensätze auf, "True" und "False", und analysiert dann die (Text-)Unterschiede zwischen ihnen. 

**Optionale Argumente**

Alle anderen Argumente sind optional, aber sie müssen wie unten angegeben sortiert werden. 

* MinTokens - 0 < *int* < 200 [Standard: 1]

    Legt die minimale Anzahl von Nicht-Wildcard-Token pro Ergebnismuster fest.

* Schwellenwert - 0,015 < *doppel* < 1 [Standard: 0,05]

    Legt die minimale Musterdifferenz (Verhältnis) zwischen den beiden Sätzen fest (siehe [diffpatterns](diffpatternsplugin.md)).

* MaxTokens - 0 < *int* [Standard: 20]

    Legt die maximale Anzahl von Token (von Anfang an) pro Ergebnismuster fest, sodass die Abfragelaufzeit verringert wird, wenn ein niedrigerer Grenzwert angegeben wird.

**Rückgabe**

Das Ergebnis der diffpatterns_text gibt die folgenden Spalten zurück:

* Count_of_True: Die Anzahl der Zeilen, die `true`dem Muster entsprechen, wenn die Bedingung ist.
* Count_of_False: Die Anzahl der Zeilen, die `false`dem Muster entsprechen, wenn die Bedingung ist.
* Percent_of_True: Der Prozentsatz der Zeilen, die dem `true`Muster aus den Zeilen entsprechen, wenn die Bedingung ist .
* Percent_of_False: Der Prozentsatz der Zeilen, die dem `false`Muster aus den Zeilen entsprechen, wenn die Bedingung ist .
* Muster: Das Textmuster, das Token aus`*`der Textzeichenfolge und ' ' für Platzhalter enthält. 

> [!NOTE]
> Die Muster sind nicht unbedingt unterschiedlich und bieten möglicherweise keine vollständige Abdeckung des Datensatzes. Die Muster können sich überlappen, und einige Zeilen stimmen möglicherweise nicht mit Mustern überein.

**Beispiel**

```kusto
StormEvents     
| where EventNarrative != "" and monthofyear(StartTime) > 1 and monthofyear(StartTime) < 9
| where EventType == "Drought" or EventType == "Extreme Cold/Wind Chill"
| evaluate diffpatterns_text(EpisodeNarrative, EventType == "Extreme Cold/Wind Chill", 2)
```
|Count_of_True|Count_of_False|Percent_of_True|Percent_of_False|Muster|
|---|---|---|---|---|
|11|0|6.29|0|Winde, die sich nach Nordwesten in * wake * eine Oberflächentrog brachte schweren Seeeffekt Schneefall nach unten Wind * Lake Superior aus|
|9|0|5.14|0|Kanadische Hochdruck besiedelt * * Region * produziert die kältesten Temperaturen seit Februar * 2006. Dauer * Gefriertemperaturen|
|0|34|0|6.24|* * * * * * * * * * * * * * * * * * * West Tennessee,|
|0|42|0|7.71|* * * * * * verursacht * * * * * * * * * im Westen Colorados. *|
|0|45|0|8.26|* * unter normal *|
|0|110|0|20.18|Unter dem Normalwert *|

