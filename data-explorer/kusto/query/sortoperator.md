---
title: Sortieroperator - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt den Sortieroperator in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 638783b28cddc51d64a80096d7d4d6e0f669d354
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507479"
---
# <a name="sort-operator"></a>sort-Operator 

Sortiert die Zeilen der Eingabetabelle in Reihenfolge nach einer oder mehreren Spalten.

```kusto
T | sort by strlen(country) asc, price desc
```

**Alias**

`order`

**Syntax**

`asc` | `desc``nulls first` | `nulls last` *T-Ausdruck* `| sort by` *expression* [ ]`,` [ ] [ ...]

**Argumente**

* *T*: Die zu sortierende Tabelleneingabe.
* *ausdruck*: Ein skalarer Ausdruck, nach dem sortiert werden soll. Der Typ der Werte muss „Numerisch“, „Datum“, „Uhrzeit“ oder „Zeichenfolge“ sein.
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