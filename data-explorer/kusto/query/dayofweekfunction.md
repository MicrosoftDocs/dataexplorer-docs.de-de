---
title: dayof Week ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird dayof Week () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0b13b7e4f0425cd83aefb41a94b76eb08db27498
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247655"
---
# <a name="dayofweek"></a>dayofweek()

Gibt die ganzzahlige Anzahl von Tagen seit dem vorangehenden Sonntag als zurück `timespan` .

```kusto
dayofweek(datetime(2015-12-14)) == 1d  // Monday
```

## <a name="syntax"></a>Syntax

`dayofweek(`*a_date*`)`

## <a name="arguments"></a>Argumente

* `a_date`: Ein `datetime`-Wert.

## <a name="returns"></a>Rückgabe

Die Dauer (als `timespan` -Wert) seit Mitternacht zu Beginn des vorhergehenden Sonntags, abgerundet auf die Anzahl von Tagen als ganze Zahl.

## <a name="examples"></a>Beispiele

```kusto
dayofweek(datetime(1947-11-30 10:00:05))  // time(0.00:00:00), indicating Sunday
dayofweek(datetime(1970-05-11))           // time(1.00:00:00), indicating Monday
```
