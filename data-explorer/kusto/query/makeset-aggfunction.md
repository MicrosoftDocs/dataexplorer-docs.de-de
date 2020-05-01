---
title: MAKE_SET () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird MAKE_SET () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: e6eb481423e31e4dfa1b4e6c738ffb525e9aaef7
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618399"
---
# <a name="make_set-aggregation-function"></a>MAKE_SET () (Aggregations Funktion)

Gibt ein `dynamic` -Array (JSON) des Satzes eindeutiger Werte zurück, die *Expr* in der Gruppe annimmt.

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

**Syntax**

`summarize``make_set(` *Expr* [`,` *MaxSize*]`)`

**Argumente**

* *Expr*: Ausdruck für die Aggregations Berechnung.
* *MaxSize* ist eine optionale ganzzahlige Beschränkung für die maximale Anzahl von zurückgegebenen Elementen (Standardwert ist *1048576*). Der MaxSize-Wert darf 1048576 nicht überschreiten.

> [!NOTE]
> Eine Legacy-und veraltete Variante dieser Funktion: `makeset()` hat ein Standard Limit von *MaxSize* = 128.

**Rückgabe**

Gibt ein `dynamic` -Array (JSON) des Satzes eindeutiger Werte zurück, die *Expr* in der Gruppe annimmt.
Die Sortierreihenfolge des Arrays ist nicht definiert.

> [!TIP]
> Um nur unterschiedliche Werte zu zählen, verwenden Sie [DCount ()](dcount-aggfunction.md) .

**Beispiel**

```kusto
PageViewLog 
| summarize countries=make_set(country) by continent
```

:::image type="content" source="images/makeset-aggfunction/makeset.png" alt-text="Makeset":::

**Siehe auch**

* Verwenden [`mv-expand`](./mvexpandoperator.md) Sie den-Operator für die umgekehrte Funktion.
* [`make_set_if`](./makesetif-aggfunction.md)der-Operator ähnelt `make_set`, mit dem Unterschied, dass er auch ein Prädikat akzeptiert.