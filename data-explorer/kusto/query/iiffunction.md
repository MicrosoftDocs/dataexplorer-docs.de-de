---
title: iif() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt iif() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 36ba98b9677055dffce32911d80e67a9161b673b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514041"
---
# <a name="iif"></a>iif()

Bewertet das erste Argument (das Prädikat) und gibt den Wert des zweiten oder dritten `true` Arguments zurück, `false` je nachdem, ob das Prädikat auf (zweites) oder (drittes) ausgewertet wurde.

Die zweiten und dritten Argumente müssen vom gleichen Typ sein.

**Syntax**

`iif(`*Prädikat* `,` *ifTrue* `,` *ifFalse*`)`

**Argumente**

* *Prädikat*: Ein Ausdruck, `boolean` der zu einem Wert ausgewertet wird.
* *ifTrue*: Ein Ausdruck, der ausgewertet wird, und sein Wert `true`wird von der Funktion zurückgegeben, wenn *prädikat* sausiert zu .
* *ifFalse*: Ein Ausdruck, der ausgewertet wird, und sein Wert `false`wird von der Funktion zurückgegeben, wenn *prädikat* sausiert zu .

**Rückgabe**

Diese Funktion gibt den Wert von *ifTrue* zurück, wenn *predicate* als `true` ausgewertet wird, andernfalls den Wert von *ifFalse*.

**Beispiel**

```kusto
T 
| extend day = iif(floor(Timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

Ein Alias [`iff()`](ifffunction.md)für .