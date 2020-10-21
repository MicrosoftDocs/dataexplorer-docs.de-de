---
title: 'diffpatterns_text-Plug-in: Azure Daten-Explorer'
description: In diesem Artikel wird diffpatterns_text-Plug-in in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9efa144335369c3d450fadb7ac92e3dec4c87865
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249256"
---
# <a name="diffpatterns_text-plugin"></a>Plug-In „diffpatterns_text“

Vergleicht zwei Datasets von Zeichen folgen Werten und findet Textmuster, die Unterschiede zwischen den beiden Datasets charakterisieren.

```kusto
T | evaluate diffpatterns_text(TextColumn, BooleanCondition)
```

`diffpatterns_text`Gibt einen Satz von Textmustern zurück, die verschiedene Teile der Daten in den beiden Mengen erfassen (d. h. ein Muster, das einen großen Prozentsatz der Zeilen erfasst, wenn die Bedingung ist, `true` und einen niedrigen Prozentsatz der Zeilen, wenn die Bedingung ist `false` ). Die Muster werden aus aufeinander folgenden Token (getrennt durch Leerzeichen) erstellt, mit einem Token aus der Text Spalte oder einem, das einen Platzhalter `*` darstellt. Jede Zeile in den Ergebnissen steht für ein Muster.

## <a name="syntax"></a>Syntax

`T | evaluate diffpatterns_text(`TextColumn, booleancondition [, mintokens, Threshold, maxtokens]`)` 

## <a name="arguments"></a>Argumente

### <a name="required-arguments"></a>Erforderliche Argumente

* TextColumn- *column_name*

    Die zu analysierende Text Spalte muss den Typ "String" aufweisen.
    
* Booleancondition- *boolescher Ausdruck*

    Definiert, wie die zwei Daten Satz Teilmengen generiert werden, die mit der Eingabe Tabelle verglichen werden sollen. Der Algorithmus teilt die Abfrage nach der Bedingung in zwei Datasets auf: "true" und "false" und analysiert dann die (Text-) Unterschiede zwischen Ihnen. 

### <a name="optional-arguments"></a>Optionale Argumente

Alle anderen Argumente sind optional, aber sie müssen wie unten angegeben sortiert werden. 

* Mintokens-0 < *int* < 200 [Standardwert: 1]

    Legt die minimale Anzahl von Token ohne Platzhalter pro Ergebnis Muster fest.

* Schwellenwert-0,015 < *Double* < 1 [Standardwert: 0,05]

    Legt den minimalen Muster Unterschied (Verhältnis) zwischen den beiden Mengen fest (siehe [diffpatterns](diffpatternsplugin.md)).

* Maxtokens-0 < *int* [Standardwert: 20]

    Legt die maximale Anzahl von Token (beginnend mit dem Anfang) pro Ergebnis Muster fest. Wenn Sie eine niedrigere Grenze angeben, wird die Abfrage Laufzeit verringert.

## <a name="returns"></a>Rückgabe

Das Ergebnis diffpatterns_text gibt die folgenden Spalten zurück:

* Count_of_True: die Anzahl der Zeilen, die mit dem Muster übereinstimmen, wenn die Bedingung ist `true` .
* Count_of_False: die Anzahl der Zeilen, die mit dem Muster übereinstimmen, wenn die Bedingung ist `false` .
* Percent_of_True: der Prozentsatz der Zeilen, die mit dem Muster aus den Zeilen übereinstimmen, wenn die Bedingung ist `true` .
* Percent_of_False: der Prozentsatz der Zeilen, die mit dem Muster aus den Zeilen übereinstimmen, wenn die Bedingung ist `false` .
* Pattern: das Textmuster, das Tokens aus der Text Zeichenfolge und ' ' für Platzhalter enthält `*` . 

> [!NOTE]
> Die Muster sind nicht notwendigerweise eindeutig und bieten möglicherweise keine vollständige Abdeckung des Datasets. Die Muster können sich überlappen, und einige Zeilen stimmen möglicherweise nicht mit einem Muster identisch.

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents     
| where EventNarrative != "" and monthofyear(StartTime) > 1 and monthofyear(StartTime) < 9
| where EventType == "Drought" or EventType == "Extreme Cold/Wind Chill"
| evaluate diffpatterns_text(EpisodeNarrative, EventType == "Extreme Cold/Wind Chill", 2)
```

|Count_of_True|Count_of_False|Percent_of_True|Percent_of_False|Muster|
|---|---|---|---|---|
|11|0|6,29|0|Windrichtung Nordwesten in * Wake * a Surface trough hat Heavy Lake effect schneedownwind * Lake Superior from|
|9|0|5,14|0|Kanadischer hoher Druckbereich * * Region * hat seit Februar * 2006 die kolurigstemperatur erzeugt. Dauer * Einfrieren der Temperaturen|
|0|34|0|6,24|* * * * * * * * * * * * * * * * * * West Tennessee,|
|0|42|0|7,71|* * * * * * hat * * * * * * * * in Western Colorado verursacht. *|
|0|45|0|8,26|* * niedriger als normal *|
|0|110|0|20,18|Niedriger als normal *|
