---
title: Auftragsoperator - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der Auftragsoperator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9e2c2adb86f1eb705856e95f8b8f4ee329cb3b43
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511967"
---
# <a name="order-operator"></a>order-Operator 

Sortiert die Zeilen der Eingabetabelle in Reihenfolge nach einer oder mehreren Spalten.

```kusto
T | order by country asc, price desc
```

**Alias**

[sort-Operator](sortoperator.md)

**Syntax**

`asc` | `desc``nulls first` | `nulls last` *T-Spalte* `| sort by` *column* [ ]`,` [ ] [ ...]

**Argumente**

* *T*: Die zu sortierende Tabelleneingabe.
* *Spalte*: Spalte von *T,* nach der sortiert werden soll. Der Typ der Werte muss „Numerisch“, „Datum“, „Uhrzeit“ oder „Zeichenfolge“ sein.
* `asc` : Sortierung in aufsteigender Reihenfolge. Die Standardeinstellung ist `desc`, also absteigend.
* `nulls first`(der Standard `asc` für die Reihenfolge) platziert die `nulls last` NULL-Werte `desc` am Anfang und (der Standard für die Reihenfolge) platziert die NULL-Werte am Ende.

**Beispiel**

```kusto
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc nulls first
```

Alle Zeilen in der Tabelle „Traces“ mit einer bestimmten Aktivitäts-ID ( `ActivityId`), sortiert nach ihrem Zeitstempel. Wenn `Timestamp` die Spalte NULL-Werte enthält, werden diese in den ersten Zeilen des Ergebnisses angezeigt.

Um NULL-Werte aus dem Ergebnis auszuschließen, fügen Sie einen Filter vor dem Aufruf zum Sortieren hinzu:

```kusto
Traces
| where ActivityId == "479671d99b7b" and isnotnull(Timestamp)
| sort by Timestamp asc
```