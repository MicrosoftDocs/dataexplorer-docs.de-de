---
title: session_count-Plugin - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird session_count-Plugin in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 76ce6ca3be1859aba0a94ae155c60342be3f1ad1
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663149"
---
# <a name="session_count-plugin"></a>session_count-Plug-In

Berechnet die Anzahl der Sitzungen basierend auf der ID-Spalte über eine Zeitachse.

```kusto
T | evaluate session_count(id, datetime_column, startofday(ago(30d)), startofday(now()), 1min, 30min, dim1, dim2, dim3)
```

**Syntax**

*T* `| evaluate` `,` *End* `,` *Bin* `,` `,` `,` `,` *IdColumn* `,` `,` *Start* *TimelineColumn* *LookBackWindow* *dim2* *dim1* IdColumn TimelineColumn Start End Bin LookBackWindow [ dim1 dim2 ...] `session_count(``)`

**Argumente**

* *T*: Der tabellarische Eingabeausdruck.
* *IdColumn*: Der Name der Spalte mit ID-Werten, die die Benutzeraktivität darstellen. 
* *TimelineColumn*: Der Name der Spalte, die die Zeitachse darstellt.
* *Start*: Skalar mit Wert des Analysestartzeitraums.
* *Ende*: Skalar mit Wert der Analyseendperiode.
* *Bin*: skalarkonstanter Wert des Sitzungsanalyseschrittzeitraums.
* *LookBackWindow*: skalarer konstanter Wert, der den Sitzungs-Lookback-Zeitraum darstellt. Wenn die `IdColumn` ID von in `LookBackWindow` einem Zeitfenster innerhalb angezeigt wird - die Sitzung wird als vorhanden betrachtet, wenn nicht - wird die Sitzung als neu betrachtet.
* *dim1*, *dim2*, ...: (optional) Liste der Dimensionsspalten, die die Berechnung der Sitzungsanzahl aufteilen.

**Rückgabe**

Gibt eine Tabelle zurück, die die Sitzungsanzahlwerte für jeden Zeitachsenzeitraum und für jede vorhandene Dimensionskombination enthält.

Ausgabetabellenschema ist:

|*TimelineColumn*|dim1|..|dim_n|count_sessions|
|---|---|---|---|---|--|--|--|--|--|--|
|Typ: ab *TimelineColumn*|..|..|..|long|


**Beispiele**


Im Interesse des Beispiels werden wir Daten deterministisch machen - eine Tabelle mit zwei Spalten:
- Zeitleiste: eine laufende Zahl von 1 bis 10.000
- ID: ID des Benutzers von 1 bis 50

`Id`am spezifischen `Timeline` Steckplatz angezeigt werden, `Timeline` wenn es sich um einen Teiler von (Timeline % ID == 0) handelt.

Dies bedeutet, `Id==1` dass das `Timeline` Ereignis mit `Id==2` an `Timeline` jedem Slot, Ereignis mit jedem zweiten Slot erscheinen wird, und so weiter.

Hier sind nur 20 Zeilen der Daten:

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

Definieren wir eine Sitzung in den nächsten Begriffen: Sitzung`Id`gilt als aktiv, solange Benutzer ( ) mindestens einmal in einem Zeitrahmen von 100 Zeitfenstern angezeigt wird, während das Sitzungs-Look-Back-Fenster 41 Zeitfenster beträgt.

Die nächste Abfrage zeigt die Anzahl der aktiven Sitzungen gemäß der obigen Definition.

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

:::image type="content" source="images/queries/example-session-count.png" alt-text="Beispielsitzungsanzahl":::
