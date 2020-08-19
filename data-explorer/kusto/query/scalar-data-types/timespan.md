---
title: 'Der TimeSpan-Datentyp: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird der TimeSpan-Datentyp in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 204076e8ed079dec69cae7080e7d2c50df52a9a6
ms.sourcegitcommit: bc09599c282b20b5be8f056c85188c35b66a52e5
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88610320"
---
# <a name="the-timespan-data-type"></a>Der TimeSpan-Datentyp

Der `timespan` ( `time` )-Datentyp stellt ein Zeitintervall dar.

## <a name="timespan-literals"></a>TimeSpan-Literale

Literale vom Typ `timespan` verfügen über den Syntax `timespan(` *Wert* `)` , bei dem eine Reihe von Formaten für den *Wert*unterstützt werden, wie in der folgenden Tabelle angegeben:

|Wert|Zeitspanne|
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

Das besondere Formular `time(null)` ist der [NULL-Wert](null-values.md).

## <a name="timespan-operators"></a>TimeSpan-Operatoren

Es können zwei Werte vom Typ `timespan` hinzugefügt, subtrahiert und geteilt werden.
Der letzte Vorgang gibt einen Wert vom Typ zurück, `real` der die Sekundenbruchteile angibt, mit denen ein Wert in den anderen eingefügt werden kann.

## <a name="examples"></a>Beispiele

Im folgenden Beispiel wird die Anzahl der Sekunden an einem Tag auf verschiedene Weise berechnet:

```kusto
print
    result1 = 1d / 1s,
    result2 = time(1d) / time(1s),
    result3 = 24 * 60 * time(00:01:00) / time(1s)
```