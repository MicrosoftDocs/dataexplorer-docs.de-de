---
title: make_list () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird make_list () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: fed2616f5fbd32b1c80f936d5689261467a9dc5e
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83224832"
---
# <a name="make_list-aggregation-function"></a>make_list () (Aggregations Funktion)

Gibt ein `dynamic` -Array (JSON) aller Werte von *Expr* in der Gruppe zurück.

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

**Syntax**

`summarize``make_list(` *Expr* [ `,` *MaxSize*]`)`

**Argumente**

* *Expr*: Ausdruck, der für die Aggregations Berechnung verwendet wird.
* *MaxSize* ist eine optionale ganzzahlige Beschränkung für die maximale Anzahl von zurückgegebenen Elementen (Standardwert ist *1048576*). Der MaxSize-Wert darf 1048576 nicht überschreiten.

> [!NOTE]
> Eine Legacy-und veraltete Variante dieser Funktion: `makelist()` hat ein Standard Limit von *MaxSize* = 128.

**Rückgabe**

Gibt ein `dynamic` -Array (JSON) aller Werte von *Expr* in der Gruppe zurück.
Wenn die Eingabe für den `summarize` Operator nicht sortiert ist, ist die Reihenfolge der Elemente im resultierenden Array nicht definiert.
Wenn die Eingabe für den `summarize` Operator sortiert ist, verfolgt die Reihenfolge der Elemente im resultierenden Array die der Eingabe.

> [!TIP]
> Verwenden Sie den- [`mv-apply`](./mv-applyoperator.md) Operator, um eine geordnete Liste nach einem Schlüssel zu erstellen. Beispiele finden Sie [hier](./mv-applyoperator.md#using-the-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key).

**Siehe auch**

[`make_list_if`](./makelistif-aggfunction.md)der-Operator ähnelt `make_list` , mit dem Unterschied, dass er auch ein Prädikat akzeptiert.