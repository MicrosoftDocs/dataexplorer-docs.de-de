---
title: toscalar() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt toscalar() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 60fe8123760a9921bfa7abfacbbdffba6dba8d7b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505898"
---
# <a name="toscalar"></a>toscalar()

Gibt einen skalaren konstanten Wert des ausgewerteten Ausdrucks zurück. 

Diese Funktion ist nützlich für Abfragen, die gestaffelte Berechnungen erfordern, z. B. die Berechnung einer Gesamtanzahl von Ereignissen und dann die Verwendung für das Filtern von Gruppen, die bestimmte Prozentsätze aller Ereignisse überschreiten. 

**Syntax**

`toscalar(`*Ausdruck*`)`

**Argumente**

* *Ausdruck*: Ausdruck, der für die Skalarkonvertierung ausgewertet wird  

**Rückgabe**

Ein skalarkonstanter Wert des ausgewerteten Ausdrucks.
Wenn das Ausdrucksergebnis eine Tabellarische ist, werden die erste Spalte und die erste Zeile zur Konvertierung übernommen.

> [!TIP]
> Sie können eine [let-Anweisung](letstatement.md) für die `toscalar()`Lesbarkeit der Abfrage verwenden, wenn Sie .

**Hinweise**

`toscalar()`kann während der Abfrageausführung eine konstante Anzahl von Malen berechnet werden.
Mit anderen `toscalar()` Worten, die Funktion kann nicht auf Zeilenebene von (für jede Zeile Szenario) angewendet werden.

**Beispiele**

Die folgende Abfrage `Start` `End` wertet aus , und `Step` als skalare Konstanten - und verwendet sie für die `range` Auswertung. 

```kusto
let Start = toscalar(print x=1);
let End = toscalar(range x from 1 to 9 step 1 | count);
let Step = toscalar(2);
range z from Start to End step Step | extend start=Start, end=End, step=Step
```

|z|start|end|Schritt|
|---|---|---|---|
|1|1|9|2|
|3|1|9|2|
|5|1|9|2|
|7|1|9|2|
|9|1|9|2|