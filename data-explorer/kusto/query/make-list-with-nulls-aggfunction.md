---
title: make_list_with_nulls () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird make_list_with_nulls () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
ms.openlocfilehash: 18d10aa15e25979c0945ec26efb8bfe9a66dfde4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252222"
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
> Verwenden Sie die- [`array_sort_asc()`](./arraysortascfunction.md) oder- [`array_sort_desc()`](./arraysortdescfunction.md) Funktion, um eine geordnete Liste anhand eines Schlüssels zu erstellen.
