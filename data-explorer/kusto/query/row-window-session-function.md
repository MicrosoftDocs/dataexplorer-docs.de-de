---
title: row_window_session ()-Azure Daten-Explorer
description: In diesem Artikel wird row_window_session () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f872004a8291adc95f594c6301075faa02c8ec6c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242873"
---
# <a name="row_window_session"></a>row_window_session()

`row_window_session()` berechnet die Sitzungsstart Werte einer Spalte in einem [serialisierten Zeilen Satz](./windowsfunctions.md#serialized-row-set).

## <a name="syntax"></a>Syntax

`row_window_session` `(` *`Expr`* `,` *`MaxDistanceFromFirst`* `,` *`MaxDistanceBetweenNeighbors`* [`,` *`Restart`*] `)`

* *`Expr`* ein Ausdruck, dessen Werte in Sitzungen gruppiert werden.
  NULL-Werte verursachen NULL-Werte, und der nächste Wert startet eine neue Sitzung.
  *`Expr`* muss ein skalarer Ausdruck vom Typ sein `datetime` .

* *`MaxDistanceFromFirst`* legt ein Kriterium für das Starten einer neuen Sitzung fest: den maximalen Abstand zwischen dem aktuellen Wert von *`Expr`* und dem Wert von *`Expr`* am Anfang der Sitzung.
  Dabei handelt es sich um eine skalare Konstante vom Typ `timespan` .

* *`MaxDistanceBetweenNeighbors`* legt ein zweites Kriterium für das Starten einer neuen Sitzung fest: der maximale Abstand von einem Wert *`Expr`* zum nächsten.
  Dabei handelt es sich um eine skalare Konstante vom Typ `timespan` .

* *Restart* ist ein optionaler Skalarausdruck vom Typ `boolean` . Wenn angegeben, wird die Sitzung von jedem Wert, der ergibt, `true` sofort neu gestartet.

## <a name="returns"></a>Rückgabe

Die-Funktion gibt die Werte am Anfang jeder Sitzung zurück.

**Notizen**

Die Funktion verfügt über das folgende konzeptionelle Berechnungsmodell:

1. Wechseln Sie in der angegebenen Reihenfolge zur Eingabe Sequenz von Werten *`Expr`* .

1. Legen Sie für jeden Wert fest, ob eine neue Sitzung eingerichtet wird.

1. Wenn eine neue Sitzung eingerichtet wird, geben Sie den Wert von aus *`Expr`* . Andernfalls geben Sie den vorherigen Wert von aus *`Expr`* .

Die Bedingung, die bestimmt, ob der Wert eine neue Sitzung darstellt, ist eine logische oder eine der folgenden Bedingungen:

* , Wenn kein vorheriger Sitzungs Wert vorhanden war, oder der vorherige Sitzungs Wert NULL war.

* , Wenn der Wert von *`Expr`* den vorherigen Sitzungs Wert plus plus überschreitet *`MaxDistanceFromFirst`* .

* , Wenn der Wert von *`Expr`* den vorherigen Wert von plus überschreitet oder überschreitet *`Expr`* *`MaxDistanceBetweenNeighbors`* .

## <a name="examples"></a>Beispiele

Im folgenden Beispiel wird gezeigt, wie die Startwerte der Sitzung für eine Tabelle mit zwei Spalten berechnet `ID` werden: eine Spalte, die eine Sequenz identifiziert, und eine Spalte, die `Timestamp` die Uhrzeit angibt, zu der jeder Datensatz aufgetreten ist. In diesem Beispiel darf eine Sitzung nicht länger als 1 Stunde sein, und Sie wird fortgesetzt, solange Datensätze weniger als 5 Minuten voneinander entfernt sind.

```kusto
datatable (ID:string, Timestamp:datetime) [
    // ...
]
| sort by ID asc, Timestamp asc
| extend SessionStarted = row_window_session(Timestamp, 1h, 5m, ID != prev(ID))
```