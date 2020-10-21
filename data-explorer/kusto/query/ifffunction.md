---
title: IFF ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird IFF () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5cc6a41c8b74e4fd08eebbe968b7384dce39039e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252279"
---
# <a name="iff"></a>iff()

Wertet das erste Argument (das Prädikat) aus und gibt den Wert des zweiten oder dritten Arguments zurück, abhängig davon, ob das Prädikat als `true` (Second) oder `false` (dritte) ausgewertet wurde.

Die zweiten und dritten Argumente müssen vom gleichen Typ sein.

## <a name="syntax"></a>Syntax

`iff(`*Prädikat* `,` *ifTrue* `,` *IfFalse*`)`

## <a name="arguments"></a>Argumente

* *Prädikat*: ein Ausdruck, der zu einem `boolean` Wert ausgewertet wird.
* *ifTrue*: ein Ausdruck, der ausgewertet wird und dessen Wert von der Funktion zurückgegeben wird, wenn *Prädikat* als ausgewertet wird `true` .
* *IfFalse*: ein Ausdruck, der ausgewertet wird, und der Wert, der von der Funktion zurückgegeben wird, wenn *Prädikat* als ausgewertet wird `false` .

## <a name="returns"></a>Rückgabe

Diese Funktion gibt den Wert von *ifTrue* zurück, wenn *predicate* als `true` ausgewertet wird, andernfalls den Wert von *ifFalse*.

## <a name="example"></a>Beispiel

```kusto
T 
| extend day = iff(floor(Timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```