---
title: Doppel ()/toreal ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird "/toreal ()" in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9a1a18ffdfc28d0487464202baa759acccd3e40e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250475"
---
# <a name="todouble-toreal"></a>todouble(), toreal()

Konvertiert die Eingabe in einen Wert vom Typ `real` . ( `todouble()` und `toreal()` sind Synonyme.)

```kusto
toreal("123.4") == 123.4
```

> [!NOTE]
> Verwenden Sie nach Möglichkeit die Verwendung von [Double () oder Real ()](./scalar-data-types/real.md) .

## <a name="syntax"></a>Syntax

`toreal(`*Expr* `)` 
 Expr `todouble(` *Expr*`)`

## <a name="arguments"></a>Argumente

* *Expr*: ein Ausdruck, dessen Wert in einen Wert vom Typ konvertiert wird `real` .

## <a name="returns"></a>Rückgabe

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis ein Wert vom Typ `real` .
Wenn die Konvertierung nicht erfolgreich ist, ist das Ergebnis der Wert `real(null)` .
