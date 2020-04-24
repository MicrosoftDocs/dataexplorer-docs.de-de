---
title: make_list() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird make_list() (Aggregationsfunktion) in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: e46dbfac7b8c819f66d469c160452ec4dddfb769
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512749"
---
# <a name="make_list-aggregation-function"></a>make_list() (Aggregationsfunktion)

Gibt ein `dynamic` -Array (JSON) aller Werte von *Expr* in der Gruppe zurück.

* Kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden

**Syntax**

`summarize``make_list(` *Expr* `,` [ *MaxSize*]`)`

**Argumente**

* *Expr*: Ausdruck, der für die Aggregationsberechnung verwendet wird.
* *MaxSize* ist ein optionaler Ganzzahlgrenzwert für die maximale Anzahl der zurückgegebenen Elemente (Standardwert ist *1048576*). Der MaxSize-Wert darf 1048576 nicht überschreiten.

> [!NOTE]
> Eine veraltete und veraltete `makelist()` Variante dieser Funktion: hat ein Standardlimit von *MaxSize* = 128.

**Rückgabe**

Gibt ein `dynamic` -Array (JSON) aller Werte von *Expr* in der Gruppe zurück.
Wenn die Eingabe `summarize` für den Operator nicht sortiert ist, ist die Reihenfolge der Elemente im resultierenden Array nicht definiert.
Wenn die Eingabe `summarize` für den Operator sortiert ist, wird die Reihenfolge der Elemente im resultierenden Array die Reihenfolge der Eingabe nachverfolgt.

> [!TIP]
> Verwenden [`mv-apply`](./mv-applyoperator.md) Sie den Operator, um eine geordnete Liste mit einem Schlüssel zu erstellen. Beispiele finden Sie [hier](./mv-applyoperator.md#using-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key).

**Siehe auch**

[`make_list_if`](./makelistif-aggfunction.md)Operator ist `make_list`ähnlich wie , außer es akzeptiert auch ein Prädikat.