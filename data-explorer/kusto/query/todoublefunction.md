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
ms.openlocfilehash: 8e93e86814adf2789d01e03173196468f085b7c2
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/05/2020
ms.locfileid: "87804099"
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

## <a name="returns"></a>Gibt zurück

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis ein Wert vom Typ `real` .
Wenn die Konvertierung nicht erfolgreich ist, ist das Ergebnis der Wert `real(null)` .
