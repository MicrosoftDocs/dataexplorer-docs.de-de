---
title: "\"|\" (): Azure-Daten-Explorer"
description: In diesem Artikel wird der Wert von "" in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 15d9056ec21eb6f25ccbc985d659f310d670f02d
ms.sourcegitcommit: 085e212fe9d497ee6f9f477dd0d5077f7a3e492e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2020
ms.locfileid: "85133412"
---
# <a name="toscalar"></a>toscalar()

Gibt einen skalaren Konstanten Wert des ausgewerteten Ausdrucks zurück. 

Diese Funktion ist nützlich für Abfragen, für die gestaffelte Berechnungen erforderlich sind. Berechnen Sie z. b. die Gesamtanzahl von Ereignissen, und verwenden Sie dann das Ergebnis, um Gruppen zu filtern, die einen bestimmten Prozentsatz aller Ereignisse überschreiten.

**Syntax**

`toscalar(`*Begriff*`)`

**Argumente**

* *Expression*: Ausdruck, der für die skalare Konvertierung ausgewertet wird.

**Rückgabe**

Ein skalarer konstanter Wert des ausgewerteten Ausdrucks.
Wenn das Ergebnis eine tabellarische ist, werden die erste Spalte und die erste Zeile für die Konvertierung übernommen.

> [!TIP]
> Sie können eine [Let-Anweisung](letstatement.md) zur besseren Lesbarkeit der Abfrage verwenden, wenn Sie verwenden `toscalar()` .

**Notizen**

`toscalar()`kann während der Abfrage Ausführung beliebig oft berechnet werden.
Die `toscalar()` Funktion kann nicht auf Zeilenebene (für jedes Zeilen Szenario) angewendet werden.

**Beispiele**

Werten Sie `Start` , `End` und `Step` als skalare Konstanten aus, und verwenden Sie das Ergebnis für die `range` Auswertung.

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
