---
title: Der Datentyp der Zeitspanne - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt den Datentyp der Zeitspanne in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 31a0bfafed817ffaf531cffdcb844da8a357531f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509604"
---
# <a name="the-timespan-data-type"></a>Der Zeitspandatentyp

Der `timespan` `time`Datentyp ( ) stellt ein Zeitintervall dar.

## <a name="timespan-literals"></a>Zeitspanliterale

Literale vom `timespan` Typ `timespan(`haben den *Syntaxwert*`)`, wobei eine Reihe von Formaten für *den Wert*unterstützt werden, wie in der folgenden Tabelle angegeben:

|||
---|---
`2d`|2 Tage
`1.5h`|1,5 Stunden
`30m`|30 Minuten
`10s`|10 Sekunden
`0.1s`|0,1 Sekunde
`100ms`| 100 Millisekunden
`10microsecond`|10 Mikrosekunden
`1tick`|100 ns
`time(15 seconds)`|15 Sekunden
`time(2)`| 2 Tage
`time(0.12:34:56.7)`|`0d+12h+34m+56.7s`

Die sonderform `time(null)` ist der [Nullwert](null-values.md).

## <a name="timespan-operators"></a>Zeitspanoperatoren

Zwei Werte `timespan` des Typs können hinzugefügt, subtrahiert und geteilt werden.
Der letzte Vorgang gibt `real` einen Wert des Typs zurück, der die Bruchzahl der Anzahl der Werte darstellt, die dem anderen wert entsprechen können.

## <a name="examples"></a>Beispiele

Im folgenden Beispiel wird auf verschiedene Weise berechnet, wie viele Sekunden an einem Tag sind:

```kusto
print
    result1 = 1d / 1s,
    result2 = time(1d) / time(1s),
    result3 = 24 * 60 * time(00:01:00) / time(1s)
```