---
title: Logische (binäre) Operatoren - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden logische (binäre) Operatoren in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/14/2018
ms.openlocfilehash: 53505067b93234aa89849e1c66fe2f77a58e0f26
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513089"
---
# <a name="logical-binary-operators"></a>Logische (binäre) Operatoren

Die folgenden logischen Operatoren werden `bool` zwischen zwei Werten des Typs unterstützt:

> [!NOTE]
> Diese logischen Operatoren werden manchmal als boolesche Operatoren und manchmal als binäre Operatoren bezeichnet. Die Namen sind alle Synonyme.

|Operatorname|Syntax|Bedeutung|
|-------------|------|-------|
|Gleichheit     |`==`  |`true` Ergibt, wenn beide Operanden nicht null und gleich sind. Andernfalls `false`.|
|Ungleichheit   |`!=`  |`true` Ergibt, wenn entweder einer (oder beide) der Operanden null oder nicht gleich sind. Andernfalls `true`.|
|Logisch und  |`and` |`true` Ergibt, wenn beide Operanden sind `true`.|
|Logisch oder   |`or`  |`true` Ergibt, wenn einer der Operanden `true`ist , unabhängig vom anderen Operanden.|

> [!NOTE]
> Aufgrund des Verhaltens des booleschen `bool(null)`NULL-Werts sind zwei boolesche NULL-Werte `bool(null) == bool(null)` `bool(null) != bool(null)` weder gleich `false`noch gleich (mit anderen Worten, und beide geben den Wert ab).
>
> Behandeln `and` / `or` Sie hingegen den NULL-Wert `false`als `bool(null) or true` äquivalent `true`zu `bool(null) and true` `false`, ist also und ist .