---
title: dayofweek() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt dayofweek() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 31d3f525653f6e0979229e4355cdec6cb76833f8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516302"
---
# <a name="dayofweek"></a>dayofweek()

Gibt die ganze Zahl der Tage seit dem `timespan`vorherigen Sonntag als zurück.

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
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```