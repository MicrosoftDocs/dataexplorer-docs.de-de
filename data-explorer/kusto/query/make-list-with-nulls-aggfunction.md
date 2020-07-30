---
title: make_list_with_nulls () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird make_list_with_nulls () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
ms.openlocfilehash: b78bed51da8422dced4d57406c10721639b68ccc
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346982"
---
# <a name="make_list_with_nulls-aggregation-function"></a>make_list_with_nulls () (Aggregations Funktion)

Gibt ein- `dynamic` Array (JSON) aller Werte von *expr* in der Gruppe zurück, einschließlich NULL-Werten.

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

## <a name="syntax"></a>Syntax

`summarize``make_list_with_nulls(` *Expr*`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der für die Aggregations Berechnung verwendet wird.

## <a name="returns"></a>Rückgabe

Gibt ein- `dynamic` Array (JSON) aller Werte von *expr* in der Gruppe zurück, einschließlich NULL-Werten.
Wenn die Eingabe für den `summarize` Operator nicht sortiert ist, ist die Reihenfolge der Elemente im resultierenden Array nicht definiert.
Wenn die Eingabe für den `summarize` Operator sortiert ist, verfolgt die Reihenfolge der Elemente im resultierenden Array die der Eingabe.

> [!TIP]
> Verwenden Sie den- [`mv-apply`](./mv-applyoperator.md) Operator, um eine geordnete Liste nach einem Schlüssel zu erstellen. Beispiele finden Sie [hier](./mv-applyoperator.md#using-the-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key).
