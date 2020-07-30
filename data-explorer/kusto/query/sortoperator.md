---
title: 'Sort-Operator: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt den Sortier Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4f9878b77ad2288395a54d5315864e460ca37875
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351028"
---
# <a name="sort-operator"></a>sort-Operator 

Sortiert die Zeilen der Eingabetabelle in Reihenfolge nach einer oder mehreren Spalten.

```kusto
T | sort by strlen(country) asc, price desc
```

**Alias**

`order`

## <a name="syntax"></a>Syntax

*T* - `| sort by` *Ausdruck* [ `asc`  |  `desc` ] [ `nulls first`  |  `nulls last` ] [ `,` ...]

## <a name="arguments"></a>Argumente

* *T*: die zu sortierende Tabellen Eingabe.
* *Ausdruck*: ein skalarer Ausdruck, nach dem sortiert werden soll. Der Typ der Werte muss „Numerisch“, „Datum“, „Uhrzeit“ oder „Zeichenfolge“ sein.
* `asc` : Sortierung in aufsteigender Reihenfolge. Die Standardeinstellung ist `desc`, also absteigend.
* `nulls first`(der Standard `asc` Wert für Order) platziert die NULL-Werte am Anfang und `nulls last` (der Standard `desc` Wert für Order) platziert die NULL-Werte am Ende.

## <a name="example"></a>Beispiel

```kusto
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc nulls first
```

Alle Zeilen in der Tabelle „Traces“ mit einer bestimmten Aktivitäts-ID ( `ActivityId`), sortiert nach ihrem Zeitstempel. Wenn `Timestamp` die Spalte NULL-Werte enthält, werden diese in den ersten Zeilen des Ergebnisses angezeigt.

Fügen Sie vor dem aufsortier Vorgang einen Filter hinzu, um NULL-Werte aus dem Ergebnis auszuschließen:

```kusto
Traces
| where ActivityId == "479671d99b7b" and isnotnull(Timestamp)
| sort by Timestamp asc
```