---
title: make_set() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird make_set() (Aggregationsfunktion) in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: db11bd528703323d54ff96b228c0b80bbcb86dd9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512681"
---
# <a name="make_set-aggregation-function"></a>make_set() (Aggregationsfunktion)

Gibt ein `dynamic` -Array (JSON) des Satzes eindeutiger Werte zurück, die *Expr* in der Gruppe annimmt.

* Kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden

**Syntax**

`summarize``make_set(` *Expr* `,` [ *MaxSize*]`)`

**Argumente**

* *Expr*: Ausdruck für die Aggregationsberechnung.
* *MaxSize* ist ein optionaler Ganzzahlgrenzwert für die maximale Anzahl der zurückgegebenen Elemente (Standardwert ist *1048576*). Der MaxSize-Wert darf 1048576 nicht überschreiten.

> [!NOTE]
> Eine veraltete und veraltete `makeset()` Variante dieser Funktion: hat ein Standardlimit von *MaxSize* = 128.

**Rückgabe**

Gibt ein `dynamic` -Array (JSON) des Satzes eindeutiger Werte zurück, die *Expr* in der Gruppe annimmt.
Die Sortierreihenfolge des Arrays ist nicht definiert.

> [!TIP]
> Um nur einzelne Werte zu zählen, verwenden Sie [dcount()](dcount-aggfunction.md)

**Beispiel**

```kusto
PageViewLog 
| summarize countries=make_set(country) by continent
```

![alt text](./images/aggregations/makeset.png "makeset")

**Siehe auch**

* Verwenden [`mv-expand`](./mvexpandoperator.md) Sie den Operator für die entgegengesetzte Funktion.
* [`make_set_if`](./makesetif-aggfunction.md)Operator ist `make_set`ähnlich wie , außer es akzeptiert auch ein Prädikat.