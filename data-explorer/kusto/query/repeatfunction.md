---
title: REPEAT ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird REPEAT () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0d488ac96cd3db2161761ea837d5490d25cfc920
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345826"
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