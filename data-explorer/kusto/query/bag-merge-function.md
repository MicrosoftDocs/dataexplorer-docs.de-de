---
title: bag_merge ()-Azure Daten-Explorer
description: In diesem Artikel wird bag_merge () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 06/18/2020
ms.openlocfilehash: 66a05cdc03b155b8fceace0af8d86807a10d0da4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349362"
---
# <a name="bag_merge"></a>bag_merge()

Führt `dynamic` Eigenschafts Behälter in einem Eigenschaften Behälter `dynamic` zusammen, wobei alle Eigenschaften zusammengeführt werden.

## <a name="syntax"></a>Syntax

`bag_merge(`*bag1* `, ` *BAG2* `[` ,` *bag3*, ...])`

## <a name="arguments"></a>Argumente

* *bag1... Bagn*: Eingabe `dynamic` Eigenschaft-Taschen. Die-Funktion akzeptiert zwischen 2 und 64 Argumente.

## <a name="returns"></a>Rückgabe

Gibt einen `dynamic` Eigenschaften Behälter zurück. Ergebnisse aus der Zusammenführung aller Eingabe Eigenschaften Behälter-Objekte. Wenn ein Schlüssel in mehr als einem Eingabe Objekt angezeigt wird, wird ein beliebiger Wert (aus den möglichen Werten für diesen Schlüssel) ausgewählt.

## <a name="example"></a>Beispiel

Ausdruck:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print result = bag_merge(
   dynamic({'A1':12, 'B1':2, 'C1':3}),
   dynamic({'A2':81, 'B2':82, 'A1':1}))
```

|result|
|---|
|{<br>  "A1": 12,<br>  "B1": 2,<br>  "C1": 3,<br>  "A2": 81,<br>  "B2": 82<br>}|
