---
title: bag_merge ()-Azure Daten-Explorer
description: In diesem Artikel wird bag_merge () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspod
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 06/18/2020
ms.openlocfilehash: 0a23f6ece8be3ba451c1f61a90eb65452b68f9ce
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85265058"
---
# <a name="bag_merge"></a>bag_merge()

Führt `dynamic` Eigenschafts Behälter in einem Eigenschaften Behälter `dynamic` zusammen, wobei alle Eigenschaften zusammengeführt werden.

**Syntax**

`bag_merge(`*bag1* `, ` *BAG2* `[` ,` *bag3*, ...])`

**Argumente**

* *bag1... Bagn*: Eingabe `dynamic` Eigenschaft-Taschen. Die-Funktion akzeptiert zwischen 2 und 64 Argumente.

**Rückgabe**

Gibt einen `dynamic` Eigenschaften Behälter zurück. Ergebnisse aus der Zusammenführung aller Eingabe Eigenschaften Behälter-Objekte. Wenn ein Schlüssel in mehr als einem Eingabe Objekt angezeigt wird, wird ein beliebiger Wert (aus den möglichen Werten für diesen Schlüssel) ausgewählt.

**Beispiel**

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
