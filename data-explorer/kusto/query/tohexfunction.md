---
title: tohex() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt tohex() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 402a0923d4fe760e97fa6098ad955b6c24a5d5ee
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506119"
---
# <a name="tohex"></a>tohex()

Konvertiert die Eingabe in eine hexadezimale Zeichenfolge.

```kusto
tohex(256) == '100'
tohex(-256) == 'ffffffffffffff00' // 64-bit 2's complement of -256
tohex(toint(-256), 8) == 'ffffff00' // 32-bit 2's complement of -256
tohex(256, 8) == '00000100'
tohex(256, 2) == '100' // Exceeds min length of 2, so min length is ignored.
```

**Syntax**

`tohex(`*Expr*`, [`` *MinLength*]`, )'

**Argumente**

* *Expr*: int oder long-Wert, der in eine Hex-Zeichenfolge konvertiert wird.  Andere Typen werden nicht unterstützt.
* *MinLength*: numerischer Wert, der die Anzahl der führenden Zeichen darstellt, die in die Ausgabe aufgenommen werden sollen.  Werte zwischen 1 und 16 werden unterstützt, Werte größer als 16 werden auf 16 abgeschnitten.  Wenn die Zeichenfolge länger als minLength ohne führende Zeichen ist, wird minLength effektiv ignoriert.  Negative Zahlen können nur mindestens durch ihre zugrunde liegende Datengröße dargestellt werden, so dass für eine int (32-Bit) die minLength bei mindestens 8 sein wird, für eine lange (64-Bit) wird es bei mindestens 16 sein.

**Rückgabe**

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis ein Zeichenfolgenwert.
Wenn die Konvertierung nicht erfolgreich ist, lautet das Ergebnis null.