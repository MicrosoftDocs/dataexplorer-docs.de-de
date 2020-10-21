---
title: REPEAT ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird REPEAT () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cfe3c2704f7eb086319770925419a9877e39366b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243074"
---
# <a name="repeat"></a>repeat()

Generiert ein dynamisches Array, das eine Reihe gleicher Werte enthält.

## <a name="syntax"></a>Syntax

`repeat(`*Wert* `,` *Anzahl*`)` 

## <a name="arguments"></a>Argumente

* *value*: der Wert des Elements im resultierenden Array. Der *Wert* kann "Boolean", "Integer", "Long", "Real", "DateTime" oder "TimeSpan" lauten.   
* *count*: die Anzahl der Elemente im resultierenden Array. Die *Anzahl* muss eine ganzzahlige Zahl sein.
Wenn *count* gleich NULL ist, wird ein leeres Array zurückgegeben.
Wenn *count* kleiner als 0 (null) ist, wird ein NULL-Wert zurückgegeben. 

## <a name="examples"></a>Beispiele

Das folgende Beispiel gibt `[1, 1, 1]`zurück:

```kusto
T | extend r = repeat(1, 3)
```