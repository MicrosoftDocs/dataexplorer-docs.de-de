---
title: row_window_session() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird row_window_session() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 87e89443bc85125e705bc180ea0cdb9e599c9c13
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510250"
---
# <a name="row_window_session"></a>row_window_session()

`row_window_session()`berechnet Sitzungsstartwerte einer Spalte in einem [serialisierten Zeilensatz](./windowsfunctions.md#serialized-row-set).

**Syntax**

`row_window_session``(` *Expr* `,` *MaxDistanceFromFirst* `,` *MaxDistanceBetweenNeighbors* [`,` *Neustart*]`)`

* *Expr* ist ein Ausdruck, dessen Werte in Sitzungen gruppiert sind.
  Nullwerte erzeugen NULL-Werte, und der nächste Wert startet eine neue Sitzung.
  *Expr* muss ein skalarer `datetime`Ausdruck vom Typ sein.

* *MaxDistanceFromFirst* legt ein Kriterium für das Starten einer neuen Sitzung fest: Den maximalen Abstand zwischen dem aktuellen Wert von *Expr* und dem Wert von *Expr* zu Beginn der Sitzung.
  Es ist eine skalare `timespan`Konstante vom Typ .

* *MaxDistanceBetweenNeighbors* legt ein zweites Kriterium für das Starten einer neuen Sitzung fest: Die maximale Entfernung von einem Wert von *Expr* zum nächsten.
  Es ist eine skalare `timespan`Konstante vom Typ .

* *Neustart* ist ein optionaler skalarer Ausdruck vom Typ `boolean`. Wenn angegeben, wird jeder `true` Wert, der ausgewertet wird, sofort neu gestartet.

**Rückgabe**

Die Funktion gibt die Werte zu Beginn jeder Sitzung zurück.

**Hinweise**

Die Funktion verfügt über das folgende konzeptionelle Berechnungsmodell:

1. Gehen Sie über die Eingabesequenz der Werte *Expr* in der Reihenfolge.

2. Bestimmen Sie für jeden Wert, ob eine neue Sitzung erstellt wird.

3. Wenn eine neue Sitzung erstellt wird, gibt den Wert von *Expr*aus. Andernfalls wird der vorherige Wert von *Expr*ausgesendet.

Die Bedingung, die bestimmt, ob der Wert eine neue Sitzung darstellt, ist ein logisches ODER der folgenden:

* Wenn kein vorheriger Sitzungswert vorhanden war oder der vorherige Sitzungswert null war.

* Wenn der Wert von *Expr* dem vorherigen Sitzungswert plus *MaxDistanceFromFirst*entspricht oder diesen überschreitet.

* Wenn der Wert von *Expr* dem vorherigen Wert von *Expr* plus *MaxDistanceBetweenNeighbors*entspricht oder diesen überschreitet.

**Beispiele**

Das folgende Beispiel zeigt, wie die Sitzungsstartwerte für `ID` eine Tabelle mit zwei `Timestamp` Spalten, einer Spalte, die eine Sequenz identifiziert, und einer Spalte berechnet werden, die den Zeitpunkt angibt, zu dem jeder Datensatz aufgetreten ist. In diesem Beispiel darf eine Sitzung 1 Stunde nicht überschreiten und wird fortgesetzt, solange die Datensätze weniger als 5 Minuten voneinander entfernt sind.

```kusto
datatable (ID:string, Timestamp:datetime) [
    // ...
]
| sort by ID asc, Timestamp asc
| extend SessionStarted = row_window_session(Timestamp, 1h, 5m, ID != prev(ID))
```