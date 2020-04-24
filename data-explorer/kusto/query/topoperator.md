---
title: Top-Operator - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der oberste Operator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bb1d24dd0cd1dfeecb1925e474eb77e8cb86121f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505915"
---
# <a name="top-operator"></a>top-Operator

Gibt die ersten *N* Datensätze nach den angegebenen Spalten sortiert zurück.

```kusto
T | top 5 by Name desc nulls last
```

**Syntax**

*T* `| top` *NumberOfRows-Ausdruck* `by` *Expression* [`asc` | `desc`] [`nulls first` | `nulls last`]

**Argumente**

* *NumberOfRows*: Die Anzahl der zurückzugebenden Zeilen von *T.* Sie können einen beliebigen numerischen Ausdruck angeben.
* *Ausdruck*: Ein skalarer Ausdruck, nach dem sortiert werden soll. Der Typ der Werte muss „Numerisch“, „Datum“, „Uhrzeit“ oder „Zeichenfolge“ sein.
* Unter Umständen wird `asc` oder `desc` (Standard) angezeigt, um zu steuern, ob die tatsächliche Auswahl vom „unteren“ oder „oberen“ Ende des Bereichs erfolgt.
* `nulls first`(der Standard `asc` für `nulls last` die Reihenfolge) `desc` oder (der Standard für die Reihenfolge) kann angezeigt werden, um zu steuern, ob NULL-Werte am Anfang oder am Ende des Bereichs sein werden.


**Tipps**

* `top 5 by name`entspricht dem Ausdruck `sort by name | take 5` sowohl aus semantischer als auch aus Performance-Perspektive.
* Verwenden Sie den [Top-Nested-Operator,](topnestedoperator.md) um hierarchische (verschachtelte) Top-Ergebnisse zu erzeugen.