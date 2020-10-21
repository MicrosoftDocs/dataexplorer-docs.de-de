---
title: 'Sort-Operator: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt den Sortier Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8823b0a6bb15898a9bb15ed00919fa57d75f8e25
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245816"
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
* `nulls first` (der Standard `asc` Wert für Order) platziert die NULL-Werte am Anfang und `nulls last` (der Standard `desc` Wert für Order) platziert die NULL-Werte am Ende.

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