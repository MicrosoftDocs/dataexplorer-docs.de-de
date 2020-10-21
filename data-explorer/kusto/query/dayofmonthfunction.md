---
title: dayosmonth ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird dayosmonth () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e98e1405a6521ef9cdaf40a24b2c173bdf72c376
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252408"
---
# <a name="dayofmonth"></a>dayofmonth()

Gibt die ganzzahlige Zahl zurück, die die Tagesnummer des angegebenen Monats darstellt.

```kusto
dayofmonth(datetime(2015-12-14)) == 14
```

## <a name="syntax"></a>Syntax

`dayofmonth(`*a_date*`)`

## <a name="arguments"></a>Argumente

* `a_date`: Ein `datetime`-Wert.

## <a name="returns"></a>Rückgabe

`day number` des angegebenen Monats.