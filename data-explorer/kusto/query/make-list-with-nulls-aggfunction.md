---
title: make_list_with_nulls() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird make_list_with_nulls() (Aggregationsfunktion) in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
ms.openlocfilehash: 4b039008c5969cf02187d69a3486b09e04ec41ae
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512868"
---
# <a name="make_list_with_nulls-aggregation-function"></a>make_list_with_nulls() (Aggregationsfunktion)

Gibt `dynamic` ein (JSON) Array aller Werte von *Expr* in der Gruppe zurück, einschließlich NULL-Werten.

* Kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden

**Syntax**

`summarize``make_list_with_nulls(` *Expr*`)`

**Argumente**

* *Expr*: Ausdruck, der für die Aggregationsberechnung verwendet wird.

**Rückgabe**

Gibt `dynamic` ein (JSON) Array aller Werte von *Expr* in der Gruppe zurück, einschließlich NULL-Werten.
Wenn die Eingabe `summarize` für den Operator nicht sortiert ist, ist die Reihenfolge der Elemente im resultierenden Array nicht definiert.
Wenn die Eingabe `summarize` für den Operator sortiert ist, wird die Reihenfolge der Elemente im resultierenden Array die Reihenfolge der Eingabe nachverfolgt.

> [!TIP]
> Verwenden [`mv-apply`](./mv-applyoperator.md) Sie den Operator, um eine geordnete Liste mit einem Schlüssel zu erstellen. Beispiele finden Sie [hier](./mv-applyoperator.md#using-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key).
