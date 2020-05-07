---
title: row_window_session ()-Azure Daten-Explorer
description: In diesem Artikel wird row_window_session () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 51bc5e26cdc2d002b29ec435a68421ba8768a7a4
ms.sourcegitcommit: 9fe6ee7db15a5cc92150d3eac0ee175f538953d2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/07/2020
ms.locfileid: "82907181"
---
# <a name="row_window_session"></a>row_window_session()

`row_window_session()`berechnet die Sitzungsstart Werte einer Spalte in einem [serialisierten Zeilen Satz](./windowsfunctions.md#serialized-row-set).

**Syntax**

`row_window_session` `(` *`Expr`* `,` *`MaxDistanceFromFirst`* `,` *`MaxDistanceBetweenNeighbors`* [`,` *`Restart`*] `)`

* *`Expr`* ein Ausdruck, dessen Werte in Sitzungen gruppiert werden.
  NULL-Werte verursachen NULL-Werte, und der nächste Wert startet eine neue Sitzung.
  *`Expr`* muss ein skalarer Ausdruck vom Typ `datetime`sein.

* *`MaxDistanceFromFirst`* legt ein Kriterium für das Starten einer neuen Sitzung fest: den maximalen Abstand zwischen dem aktuellen *`Expr`* Wert von und dem *`Expr`* Wert von am Anfang der Sitzung.
  Dabei handelt es sich um eine skalare `timespan`Konstante vom Typ.

* *`MaxDistanceBetweenNeighbors`* legt ein zweites Kriterium für das Starten einer neuen Sitzung fest: der maximale Abstand von einem *`Expr`* Wert zum nächsten.
  Dabei handelt es sich um eine skalare `timespan`Konstante vom Typ.

* *Restart* ist ein optionaler Skalarausdruck vom `boolean`Typ. Wenn angegeben, wird die Sitzung von `true` jedem Wert, der ergibt, sofort neu gestartet.

**Rückgabe**

Die-Funktion gibt die Werte am Anfang jeder Sitzung zurück.

**Hinweise**

Die Funktion verfügt über das folgende konzeptionelle Berechnungsmodell:

1. Wechseln Sie in der angegebenen Reihenfolge *`Expr`* zur Eingabe Sequenz von Werten.

1. Legen Sie für jeden Wert fest, ob eine neue Sitzung eingerichtet wird.

1. Wenn eine neue Sitzung eingerichtet wird, geben Sie den Wert *`Expr`* von aus. Andernfalls geben Sie den vorherigen Wert von *`Expr`* aus.

Die Bedingung, die bestimmt, ob der Wert eine neue Sitzung darstellt, ist eine logische oder eine der folgenden Bedingungen:

* , Wenn kein vorheriger Sitzungs Wert vorhanden war, oder der vorherige Sitzungs Wert NULL war.

* , Wenn der Wert *`Expr`* von den vorherigen Sitzungs Wert plus plus *`MaxDistanceFromFirst`* überschreitet.

* , Wenn der Wert *`Expr`* von den vorherigen Wert von Plus *`MaxDistanceBetweenNeighbors`* über *`Expr`* schreitet oder überschreitet.

**Beispiele**

Im folgenden Beispiel wird gezeigt, wie die Startwerte der Sitzung für eine Tabelle mit zwei Spalten berechnet `ID` werden: eine Spalte, die eine Sequenz `Timestamp` identifiziert, und eine Spalte, die die Uhrzeit angibt, zu der jeder Datensatz aufgetreten ist. In diesem Beispiel darf eine Sitzung nicht länger als 1 Stunde sein, und Sie wird fortgesetzt, solange Datensätze weniger als 5 Minuten voneinander entfernt sind.

```kusto
datatable (ID:string, Timestamp:datetime) [
    // ...
]
| sort by ID asc, Timestamp asc
| extend SessionStarted = row_window_session(Timestamp, 1h, 5m, ID != prev(ID))
```