---
title: Round ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird die Round ()-Daten-Explorer in Azure beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 90d424929fe0b2034e4778ca2167e1e14dfbf79e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242899"
---
# <a name="round"></a>round()

Gibt die abgerundete Quelle auf die angegebene Genauigkeit zurück.

## <a name="syntax"></a>Syntax

`round(`*Quelle* [ `,` *Genauigkeit*]`)`

## <a name="arguments"></a>Argumente

* *Quelle*: der quellskalar, für den die Runde berechnet wird.
* *Genauigkeit*: die Anzahl der Ziffern, auf die die Quelle gerundet wird. (der Standardwert ist 0).

## <a name="returns"></a>Rückgabe

Die abgerundete Quelle mit der angegebenen Genauigkeit.

Die Runde unterscheidet sich [`bin()`](binfunction.md) / [`floor()`](floorfunction.md) von, da die erste Zahl eine Zahl auf eine bestimmte Anzahl von Ziffern aufrundet, während der Wert der letzten gerundet auf ein ganzzahliges Vielfache einer angegebenen Größe (Round (2.15, 1) 2,2 zurückgibt, während bin (2.15, 1) 2 zurückgibt).
 

## <a name="examples"></a>Beispiele

```kusto
round(2.15, 1)                   // 2.2
round(2.15) (which is the same as round(2.15, 0))                   // 2
round(-50.55, -2)                   // -100
round(21.5, -1)                   // 20
```