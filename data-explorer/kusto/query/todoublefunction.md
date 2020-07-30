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
ms.openlocfilehash: 34734f3975b1720c1d009f190d4fae2ebc54283f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350756"
---
# <a name="todouble-toreal"></a>todouble(), toreal()

Konvertiert die Eingabe in einen Wert vom Typ `real` . ( `todouble()` und `toreal()` sind Synonyme.)

```kusto
toreal("123.4") == 123.4
```

## <a name="syntax"></a>Syntax

`toreal(`*Expr* `)` 
 Expr `todouble(` *Expr*`)`

## <a name="arguments"></a>Argumente

* *Expr*: ein Ausdruck, dessen Wert in einen Wert vom Typ konvertiert wird `real` .

## <a name="returns"></a>Rückgabe

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis ein Wert vom Typ `real` .
Wenn die Konvertierung nicht erfolgreich ist, ist das Ergebnis der Wert `real(null)` .

*Hinweis*: Verwenden Sie nach Möglichkeit die Verwendung von [Double () oder Real ()](./scalar-data-types/real.md) .