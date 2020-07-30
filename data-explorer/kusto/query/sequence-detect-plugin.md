---
title: 'sequence_detect-Plug-in: Azure Daten-Explorer'
description: In diesem Artikel wird sequence_detect-Plug-in in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7b4b3d2b43bea2eeb96c9bbca94131cb7887db8c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345690"
---
# <a name="sequence_detect-plugin"></a>Plug-In „sequence_detect“

Erkennt Sequenz Vorkommen auf der Grundlage der bereitgestellten Prädikate.

```kusto
T | evaluate sequence_detect(datetime_column, 10m, 1h, e1 = (Col1 == 'Val'), e2 = (Col2 == 'Val2'), Dim1, Dim2)
```

## <a name="syntax"></a>Syntax

*T* `| evaluate` `sequence_detect` `(` *timelinecolenn* `,` *maxsequencestepwindow* `,` *maxsequencespan* `,` *expr1* `,` *expr2* `,` ..., *dim1* `,` *Dim2* `,` ...`)`

## <a name="arguments"></a>Argumente

* *T*: der tabellarische Eingabe Ausdruck.
* *Timelinecolbin*: Spalten Verweis, der die Zeitachse darstellt, muss im Quell Ausdruck vorhanden sein.
* *Maxsequencestepwindow*: skalarer konstanter Wert der maximalen zulässigen Zeitspanne zwischen zwei sequenziellen Schritten in der Sequenz
* *Maxsequencespan*: skalare Konstante Wert der maximalen Spanne für die Sequenz, um alle Schritte abzuschließen.
* *Expr1*, *expr2*,...: booleschen-Prädikat Ausdrücke definieren von Sequenz Schritten
* *Dim1*, *Dim2*,...: Dimensions Ausdrücke, die zum Korrelieren von Sequenzen verwendet werden.

## <a name="returns"></a>Rückgabe

Gibt eine einzelne Tabelle zurück, in der jede Zeile in der Tabelle ein einzelnes Sequenz Vorkommen darstellt:

* *Dim1*, *Dim2*,...: Dimensions Spalten, die zum Korrelieren von Sequenzen verwendet wurden.
* *Expr1*_*timelinecolenumn*, *expr2*_*timelinecolenumn*,...: Spalten mit Uhrzeitwerten, die die Zeitachse der einzelnen Sequenz Schritte darstellen.
* *Dauer*: das Zeitfenster für die gesamtsequenz

## <a name="examples"></a>Beispiele

### <a name="exploring-storm-events"></a>Erkunden von Storm-Ereignissen 

Die folgende Abfrage sucht in der Tabelle "stormevents" (Wetterstatistiken für 2007) und zeigt Fälle, in denen die Sequenz "übermäßige Hitze" innerhalb von 5 Tagen auf "Wildfire" folgt.

<!-- csl: https://help.kusto.windows.net/Samples -->
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

|Zustand|heat_StartTime|wildfire_StartTime|Duration|
|---|---|---|---|
|Kalifornische|2007-05-08 00:00:00.0000000|2007-05-08 16:02:00.0000000|16:02:00|
|Kalifornische|2007-05-08 00:00:00.0000000|2007-05-10 11:30:00.0000000|2.11:30:00|
|Kalifornische|2007-07-04 09:00:00.0000000|2007-07-05 23:01:00.0000000|1,14:01:00|
|South Dakota|2007-07-23 12:00:00.0000000|2007-07-27 09:00:00.0000000|3,21:00:00|
|TEXAS|2007-08-10 08:00:00.0000000|2007-08-11 13:56:00.0000000|1,05:56:00|
|Kalifornische|2007-08-31 08:00:00.0000000|2007-09-01 11:28:00.0000000|1,03:28:00|
|Kalifornische|2007-08-31 08:00:00.0000000|2007-09-02 13:30:00.0000000|2.05:30:00|
|Kalifornische|2007-09-02 12:00:00.0000000|2007-09-02 13:30:00.0000000|01:30:00|
