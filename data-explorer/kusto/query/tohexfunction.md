---
title: "\"gehex ()-Azure Daten-Explorer | Microsoft-Dokumentation"
description: In diesem Artikel wird in Azure Daten-Explorer beschrieben, wie Sie in Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7784424d0053761d4cfdb373dea0ed57f8bca83e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250359"
---
# <a name="tohex"></a>tohex()

Konvertiert Eingaben in eine hexadezimale Zeichenfolge.

```kusto
tohex(256) == '100'
tohex(-256) == 'ffffffffffffff00' // 64-bit 2's complement of -256
tohex(toint(-256), 8) == 'ffffff00' // 32-bit 2's complement of -256
tohex(256, 8) == '00000100'
tohex(256, 2) == '100' // Exceeds min length of 2, so min length is ignored.
```

## <a name="syntax"></a>Syntax

`tohex(`*Expr* `, [` , ` *MinLength*]` ) '

## <a name="arguments"></a>Argumente

* *Expr*: int-oder Long-Wert, der in eine hexadezimale Zeichenfolge konvertiert wird.  Andere Typen werden nicht unterstützt.
* *MinLength*: numerischer Wert, der die Anzahl der führenden Zeichen darstellt, die in die Ausgabe eingeschlossen werden sollen.  Werte zwischen 1 und 16 werden unterstützt, Werte, die größer als 16 sind, werden auf 16 gekürzt.  Wenn die Zeichenfolge länger als minLength ohne führende Zeichen ist, wird minLength tatsächlich ignoriert.  Negative Zahlen können nur minimal durch die zugrunde liegende Datengröße dargestellt werden. bei einem int-Wert (32 Bit) beträgt der Wert für minLength mindestens 8, für einen Long (64-Bit)-Wert ist der Wert 16.

## <a name="returns"></a>Rückgabe

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis ein Zeichen folgen Wert.
Wenn die Konvertierung nicht erfolgreich ist, ist das Ergebnis NULL.