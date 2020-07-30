---
title: IIf ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird IIf () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0d912f94a224b073fe9214f70077067d3a24c906
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347475"
---
# <a name="iif"></a>iif()

Wertet das erste Argument (das Prädikat) aus und gibt den Wert des zweiten oder dritten Arguments zurück, abhängig davon, ob das Prädikat als `true` (Second) oder `false` (dritte) ausgewertet wurde.

Die zweiten und dritten Argumente müssen vom gleichen Typ sein.

## <a name="syntax"></a>Syntax

`iif(`*Prädikat* `,` *ifTrue* `,` *IfFalse*`)`

## <a name="arguments"></a>Argumente

* *Prädikat*: ein Ausdruck, der zu einem `boolean` Wert ausgewertet wird.
* *ifTrue*: ein Ausdruck, der ausgewertet wird und dessen Wert von der Funktion zurückgegeben wird, wenn *Prädikat* als ausgewertet wird `true` .
* *IfFalse*: ein Ausdruck, der ausgewertet wird, und der Wert, der von der Funktion zurückgegeben wird, wenn *Prädikat* als ausgewertet wird `false` .

## <a name="returns"></a>Gibt zurück

Diese Funktion gibt den Wert von *ifTrue* zurück, wenn *predicate* als `true` ausgewertet wird, andernfalls den Wert von *ifFalse*.

## <a name="example"></a>Beispiel

```kusto
T 
| extend day = iif(floor(Timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

Ein Alias für [`iff()`](ifffunction.md) .