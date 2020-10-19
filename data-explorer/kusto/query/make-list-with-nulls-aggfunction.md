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
ms.openlocfilehash: c53faca94e273bf816abcfa34ed400708a7433a3
ms.sourcegitcommit: 62476f682b7812cd9cff7e6958ace5636ee46755
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2020
ms.locfileid: "92169556"
---
# <a name="make_list_with_nulls-aggregation-function"></a>make_list_with_nulls () (Aggregations Funktion)

Gibt ein- `dynamic` Array (JSON) aller Werte von *expr* in der Gruppe zurück, einschließlich NULL-Werten.

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

## <a name="syntax"></a>Syntax

`summarize``make_list_with_nulls(` *Expr*`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der für die Aggregations Berechnung verwendet wird.

## <a name="returns"></a>Gibt zurück

Gibt ein- `dynamic` Array (JSON) aller Werte von *expr* in der Gruppe zurück, einschließlich NULL-Werten.
Wenn die Eingabe für den `summarize` Operator nicht sortiert ist, ist die Reihenfolge der Elemente im resultierenden Array nicht definiert.
Wenn die Eingabe für den `summarize` Operator sortiert ist, verfolgt die Reihenfolge der Elemente im resultierenden Array die der Eingabe.

> [!TIP]
> Verwenden Sie die- [`array_sort_asc()`](./arraysortascfunction.md) oder- [`array_sort_desc()`](./arraysortdescfunction.md) Funktion, um eine geordnete Liste anhand eines Schlüssels zu erstellen.
