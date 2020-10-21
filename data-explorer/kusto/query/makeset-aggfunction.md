---
title: MAKE_SET () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird MAKE_SET () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: 8f5494c9d54d2950ba82da8de0b0094b2d17f798
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246400"
---
# <a name="make_set-aggregation-function"></a>MAKE_SET () (Aggregations Funktion)

Gibt ein `dynamic` -Array (JSON) des Satzes eindeutiger Werte zurück, die *Expr* in der Gruppe annimmt.

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

## <a name="syntax"></a>Syntax

`summarize``make_set(` *Expr* [ `,` *MaxSize*]`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck für die Aggregations Berechnung.
* *MaxSize* ist eine optionale ganzzahlige Beschränkung für die maximale Anzahl von zurückgegebenen Elementen (Standardwert ist *1048576*). Der MaxSize-Wert darf 1048576 nicht überschreiten.

> [!NOTE]
> Eine Legacy-und veraltete Variante dieser Funktion: `makeset()` hat ein Standard Limit von *MaxSize* = 128.

## <a name="returns"></a>Rückgabe

Gibt ein `dynamic` -Array (JSON) des Satzes eindeutiger Werte zurück, die *Expr* in der Gruppe annimmt.
Die Sortierreihenfolge des Arrays ist nicht definiert.

> [!TIP]
> Um nur unterschiedliche Werte zu zählen, verwenden Sie [DCount ()](dcount-aggfunction.md) .

## <a name="example"></a>Beispiel

```kusto
PageViewLog 
| summarize countries=make_set(country) by continent
```

:::image type="content" source="images/makeset-aggfunction/makeset.png" alt-text="Tabelle mit Kusto-Abfrage zusammenfassen von Ländern nach Kontinent in Azure Daten-Explorer":::

## <a name="see-also"></a>Weitere Informationen

* Verwenden Sie [`mv-expand`](./mvexpandoperator.md) den-Operator für die umgekehrte Funktion.
* [`make_set_if`](./makesetif-aggfunction.md) der-Operator ähnelt `make_set` , mit dem Unterschied, dass er auch ein Prädikat akzeptiert.
