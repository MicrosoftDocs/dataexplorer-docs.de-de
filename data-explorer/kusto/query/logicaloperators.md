---
title: 'Logische (binäre) Operatoren: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel werden logische (binäre) Operatoren in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/14/2018
ms.openlocfilehash: 6d1fcf7b9786951fedbdbf45d9e2c20a765452dd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248514"
---
# <a name="logical-binary-operators"></a>Logische (binäre) Operatoren

Die folgenden logischen Operatoren werden zwischen zwei Werten des- `bool` Typs unterstützt:

> [!NOTE]
> Diese logischen Operatoren werden manchmal als boolesche Operatoren bezeichnet und werden manchmal als binäre Operatoren bezeichnet. Die Namen sind alle Synonyme.

|Operatorname|Syntax|Bedeutung|
|-------------|------|-------|
|Gleichheit     |`==`  |Ergibt `true` , wenn beide Operanden ungleich NULL und gleich sind. Andernfalls `false`.|
|Ungleichheit   |`!=`  |Ergibt `true` , wenn mindestens eine (oder beide) der Operanden NULL ist oder nicht identisch ist. Andernfalls `true`.|
|Logisches AND  |`and` |Ergibt, `true` Wenn beide Operanden sind `true` .|
|Logisches OR   |`or`  |Ergibt `true` , wenn einer der Operanden ist `true` , unabhängig vom anderen Operanden.|

> [!NOTE]
> Aufgrund des Verhaltens des booleschen NULL-Werts `bool(null)` sind zwei boolesche NULL-Werte weder gleich noch nicht gleich (d. h `bool(null) == bool(null)` ., `bool(null) != bool(null)` beide ergeben den Wert `false` ).
>
> Auf der anderen Seite wird `and` / `or` der NULL-Wert als äquivalent zu behandelt `false` , sodass `bool(null) or true` ist `true` , und `bool(null) and true` ist `false` .