---
title: Doppel ()/toreal ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird "/toreal ()" in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e62432773d99d74a46022cad3199f3bab0cae50b
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128456"
---
# <a name="todouble-toreal"></a>"gedouble ()", "toreal" ()

Konvertiert die Eingabe in einen Wert vom Typ `real` . ( `todouble()` und `toreal()` sind Synonyme.)

```kusto
toreal("123.4") == 123.4
```

**Syntax**

`toreal(`*Expr* `)` 
 Expr `todouble(` *Expr*`)`

**Argumente**

* *Expr*: ein Ausdruck, dessen Wert in einen Wert vom Typ konvertiert wird `real` .

**Rückgabe**

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis ein Wert vom Typ `real` .
Wenn die Konvertierung nicht erfolgreich ist, ist das Ergebnis der Wert `real(null)` .

*Hinweis*: Verwenden Sie nach Möglichkeit die Verwendung von [Double () oder Real ()](./scalar-data-types/real.md) .