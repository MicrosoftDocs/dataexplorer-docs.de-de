---
title: sequence_detect-Plugin - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird sequence_detect-Plugin in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: afe4b41cc9bf3e81389edfb1fac76472772fa8f6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509179"
---
# <a name="sequence_detect-plugin"></a>sequence_detect Plugin

Erkennt Sequenzvorkommen basierend auf bereitgestellten Prädikaten.

```kusto
T | evaluate sequence_detect(datetime_column, 10m, 1h, e1 = (Col1 == 'Val'), e2 = (Col2 == 'Val2'), Dim1, Dim2)
```

**Syntax**

*T* `| evaluate` `,` `,` `,` `,` `,` *TimelineColumn* `,` *MaxSequenceSpan* *Dim1* *Dim2* *Expr2* *Expr1* *MaxSequenceStepWindow* TimelineColumn`,` MaxSequenceStepWindow MaxSequenceSpan Expr1 Expr2 ..., Dim1 Dim2 ... `sequence_detect` `(``)`

**Argumente**

* *T*: Der tabellarische Eingabeausdruck.
* *TimelineColumn*: Spaltenverweis, der die Zeitachse darstellt, muss im Quellausdruck vorhanden sein
* *MaxSequenceStepWindow*: Skalarkonstantenwert der maximal zulässigen Zeitspanne zwischen 2 sequenziellen Schritten in der Sequenz
* *MaxSequenceSpan*: Skalarkonstantenwert der maximalen Spanne für die Sequenz, um alle Schritte abzuschließen
* *Expr1*, *Expr2*, ...: boolesche Prädikatausdrücke, die Sequenzschritte definieren
* *Dim1*, *Dim2*, ...: Dimensionsausdrücke, die zum Korrelieren von Sequenzen verwendet werden

**Rückgabe**

Gibt eine einzelne Tabelle zurück, in der jede Zeile in der Tabelle ein einzelnes Sequenzvorkommen darstellt:

* *Dim1*, *Dim2*, ...: Dimensionsspalten, die zum Korrelieren von Sequenzen verwendet wurden.
* *Expr1*_*TimelineColumn*, *Expr2*_*TimelineColumn*, ...: Spalten mit Zeitwerten, die die Zeitachse jedes Sequenzschritts darstellen.
* *Dauer*: das gesamte Sequenzzeitfenster

**Beispiele**

### <a name="exploring-storm-events"></a>Erkunden von Sturmereignissen 

Die folgende Abfrage sucht nach der Tabelle StormEvents (Wetterstatistik für 2007) und zeigt Fälle, in denen die Sequenz von "Übermäßige Hitze" innerhalb von 5 Tagen von "Wildfire" gefolgt wurde.

```kusto
StormEvents
| evaluate sequence_detect(
        StartTime,
        5d,  // step max-time
        5d,  // sequence max-time
        heat=(EventType == "Excessive Heat"), 
        wildfire=(EventType == 'Wildfire'), 
        State)
```

|State|heat_StartTime|wildfire_StartTime|Duration|
|---|---|---|---|
|Kalifornien|2007-05-08 00:00:00.0000000|2007-05-08 16:02:00.0000000|16:02:00|
|Kalifornien|2007-05-08 00:00:00.0000000|2007-05-10 11:30:00.0000000|2.11:30:00|
|Kalifornien|2007-07-04 09:00:00.0000000|2007-07-05 23:01:00.0000000|1.14:01:00|
|SOUTH DAKOTA|2007-07-23 12:00:00.0000000|2007-07-27 09:00:00.0000000|3.21:00:00|
|TEXAS|2007-08-10 08:00:00.0000000|2007-08-11 13:56:00.0000000|1.05:56:00|
|Kalifornien|2007-08-31 08:00:00.0000000|2007-09-01 11:28:00.0000000|1.03:28:00|
|Kalifornien|2007-08-31 08:00:00.0000000|2007-09-02 13:30:00.0000000|2.05:30:00|
|Kalifornien|2007-09-02 12:00:00.0000000|2007-09-02 13:30:00.0000000|01:30:00|