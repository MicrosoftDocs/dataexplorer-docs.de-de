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
ms.openlocfilehash: 04b6122c7517d79d5563892a621eed8cde3b948a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348478"
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

## <a name="returns"></a>Gibt zurück

Die Dauer (als `timespan` -Wert) seit Mitternacht zu Beginn des vorhergehenden Sonntags, abgerundet auf die Anzahl von Tagen als ganze Zahl.

## <a name="examples"></a>Beispiele

```kusto
dayofweek(datetime(1947-11-30 10:00:05))  // time(0.00:00:00), indicating Sunday
dayofweek(datetime(1970-05-11))           // time(1.00:00:00), indicating Monday
```
