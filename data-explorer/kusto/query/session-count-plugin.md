---
title: 'session_count-Plug-in: Azure Daten-Explorer'
description: In diesem Artikel wird session_count-Plug-in in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1e173dcba48e8748562bad61e0f16786e957ca83
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550553"
---
# <a name="session_count-plugin"></a>session_count-Plug-In

Berechnet die Anzahl der Sitzungen basierend auf der ID-Spalte einer Zeitachse.

```kusto
T | evaluate session_count(id, datetime_column, startofday(ago(30d)), startofday(now()), 1min, 30min, dim1, dim2, dim3)
```

**Syntax**

*T* `| evaluate` `session_count(` *idColumn* `,` *timelinecolenn* `,` *Start* `,` *Ende* `,` *bin* `,` *lookbackwindow* [ `,` *dim1* `,` *dim2* `,` ...]`)`

**Argumente**

* *T*: der tabellarische Eingabe Ausdruck.
* *IdColumn*: der Name der Spalte mit ID-Werten, die die Benutzeraktivität darstellen. 
* *Timelinecolumzun*: der Name der Spalte, die die Zeitachse darstellt.
* *Start*: Skalar mit dem Wert des Start Zeitraums der Analyse.
* *End*: Skalar mit dem Wert des endzeitraums der Analyse.
* *Bin*: skalare konstanter Wert der Sitzungs Analyseschritt Zeitraum.
* *Lookbackwindow*: skalare Konstante Wert, der den Sitzungs Such Zeitraum darstellt. Wenn die ID von `IdColumn` in einem Zeitfenster in angezeigt wird `LookBackWindow` , wird die Sitzung als eine vorhandene Sitzung betrachtet. Wenn die ID nicht angezeigt wird, gilt die Sitzung als neu.
* *dim1*, *dim2*,...: (optional) Liste der Dimensions Spalten, in denen die Berechnung der Sitzungs Anzahl in Slice ist.

**Rückgabe**

Gibt eine Tabelle zurück, die die Sitzungs Zählerwerte für jeden Zeitachsen Zeitraum und jede vorhandene Dimensions Kombination enthält.

Das Ausgabe Tabellen Schema ist:

|*Timelinecolumschlag*|dim1|..|dim_n|count_sessions|
|---|---|---|---|---|--|--|--|--|--|--|
|Typ: ab *timelinecolumschlag*|..|..|..|long|


**Beispiele**

In diesem Beispiel sind die Daten deterministisch, und wir verwenden eine Tabelle mit zwei Spalten:
- Zeitachse: eine laufende Zahl zwischen 1 und 10.000
- ID: ID des Benutzers von 1 bis 50

`Id`wird an einem bestimmten `Timeline` Slot angezeigt, wenn es sich um einen unter Teiler von handelt `Timeline` (Timeline% ID = = 0).

Ein Ereignis mit `Id==1` wird an jedem `Timeline` Slot, einem Ereignis mit `Id==2` jedem zweiten `Timeline` Slot usw. angezeigt.

Im folgenden finden Sie einige 20 Zeilen der Daten:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let _data = range Timeline from 1 to 10000 step 1
| extend __key = 1
| join kind=inner (range Id from 1 to 50 step 1 | extend __key=1) on __key
| where Timeline % Id == 0
| project Timeline, Id;
// Look on few lines of the data
_data
| order by Timeline asc, Id asc
| limit 20
```

|Zeitachse|Id|
|---|---|
|1|1|
|2|1|
|2|2|
|3|1|
|3|3|
|4|1|
|4|2|
|4|4|
|5|1|
|5|5|
|6|1|
|6|2|
|6|3|
|6|6|
|7|1|
|7|7|
|8|1|
|8|2|
|8|4|
|8|8|

Wir definieren eine Sitzung in den nächsten Begriffen: die Sitzung wird als aktiv betrachtet, solange der Benutzer ( `Id` ) mindestens einmal an einem Zeitraum von 100 Zeit Slots angezeigt wird, während das Fenster für die Sitzungs Suche 41 Zeit Slots ist.

In der nächsten Abfrage wird die Anzahl der aktiven Sitzungen entsprechend der obigen Definition angezeigt.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let _data = range Timeline from 1 to 9999 step 1
| extend __key = 1
| join kind=inner (range Id from 1 to 50 step 1 | extend __key=1) on __key
| where Timeline % Id == 0
| project Timeline, Id;
// End of data definition
_data
| evaluate session_count(Id, Timeline, 1, 10000, 100, 41)
| render linechart 
```

:::image type="content" source="images/session-count-plugin/example-session-count.png" alt-text="Beispiel für Sitzungs Anzahl" border="false":::
