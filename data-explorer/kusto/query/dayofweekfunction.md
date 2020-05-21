---
title: dayof Week ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird dayof Week () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4e445a86b976f251de2beef4726c4840bcec8e44
ms.sourcegitcommit: ee90472a4f9d751d4049744d30e5082029c1b8fa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83722030"
---
# <a name="dayofweek"></a>dayofweek()

Gibt die ganzzahlige Anzahl von Tagen seit dem vorangehenden Sonntag als zurück `timespan` .

```kusto
dayofweek(datetime(2015-12-14)) == 1d  // Monday
```

**Syntax**

`dayofweek(`*a_date*`)`

**Argumente**

* `a_date`: Ein `datetime`-Wert.

**Rückgabe**

Die Dauer (als `timespan` -Wert) seit Mitternacht zu Beginn des vorhergehenden Sonntags, abgerundet auf die Anzahl von Tagen als ganze Zahl.

**Beispiele**

```kusto
dayofweek(datetime(1947-11-30 10:00:05))  // time(0.00:00:00), indicating Sunday
dayofweek(datetime(1970-05-11))           // time(1.00:00:00), indicating Monday
```
