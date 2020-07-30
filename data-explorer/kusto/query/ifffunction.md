---
title: IFF ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird IFF () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7eeab87f3c3ef42d1e00bf0d6b8853fe3a2f3125
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347492"
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

## <a name="returns"></a>Gibt zurück

Diese Funktion gibt den Wert von *ifTrue* zurück, wenn *predicate* als `true` ausgewertet wird, andernfalls den Wert von *ifFalse*.

## <a name="example"></a>Beispiel

```kusto
T 
| extend day = iff(floor(Timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```