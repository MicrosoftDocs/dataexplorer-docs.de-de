---
title: round() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt round() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 05111b6261c14f21d8d08e88a4c070faab32dc4c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510233"
---
# <a name="round"></a>round()

Gibt die abgerundete Quelle auf die angegebene Genauigkeit zurück.

**Syntax**

`round(`*Quelle* `,` [ *Präzision*]`)`

**Argumente**

* *Quelle*: Die Quelle skalar wird die Runde berechnet.
* *Genauigkeit*: Anzahl der Ziffern, auf die die Quelle gerundet wird. (Standardwert ist 0)

**Rückgabe**

Die abgerundete Quelle mit der angegebenen Genauigkeit.

Runde unterscheidet [`bin()`](binfunction.md) / [`floor()`](floorfunction.md) sich dadurch, dass die erste Runde eine Zahl auf eine bestimmte Anzahl von Ziffern, während die letzten Runden Wert auf eine ganze Zahl Vielfache einer bestimmten Bin-Größe (rund(2.15, 1) gibt 2.2, während bin(2.15, 1) gibt 2).
 

**Beispiele**

```kusto
round(2.15, 1)                   // 2.2
round(2.15) (which is the same as round(2.15, 0))                   // 2
round(-50.55, -2)                   // -100
round(21.5, -1)                   // 20
```