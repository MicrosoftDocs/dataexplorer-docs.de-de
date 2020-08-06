---
title: 'Top-Operator: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird der Top-Operator in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 52e66205b5ba048e4ec2d160d447082b1bf65de1
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/05/2020
ms.locfileid: "87802909"
---
# <a name="top-operator"></a>top-Operator

Gibt die ersten *N* Datensätze zurück, die nach den angegebenen Spalten sortiert sind.

```kusto
T | top 5 by Name desc nulls last
```

## <a name="syntax"></a>Syntax

*T* - `| top` *numofrows* - `by` *Ausdruck* [ `asc`  |  `desc` ] [ `nulls first`  |  `nulls last` ]

## <a name="arguments"></a>Argumente

* *Nummeriofrows*: die *Anzahl von Zeilen, die* zurückgegeben werden sollen. Sie können einen beliebigen numerischen Ausdruck angeben.
* *Ausdruck*: ein skalarer Ausdruck, nach dem sortiert werden soll. Der Typ der Werte muss „Numerisch“, „Datum“, „Uhrzeit“ oder „Zeichenfolge“ sein.
* Unter Umständen wird `asc` oder `desc` (Standard) angezeigt, um zu steuern, ob die tatsächliche Auswahl vom „unteren“ oder „oberen“ Ende des Bereichs erfolgt.
* `nulls first`(Standard für `asc` Order) oder `nulls last` (die Standardeinstellung für `desc` Order) kann angezeigt werden, um zu steuern, ob NULL-Werte am Anfang oder am Ende des Bereichs liegen.

> [!TIP]
> `top 5 by name`entspricht dem Ausdruck `sort by name | take 5` aus Semantik-und Leistungs Perspektiven.

## <a name="see-also"></a>Siehe auch 

* Verwenden Sie den [Top-netsted-](topnestedoperator.md) Operator, um hierarchische (hervor gebnigte) Top-Ergebnisse zu
