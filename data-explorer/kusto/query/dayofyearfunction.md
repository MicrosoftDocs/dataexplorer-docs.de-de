---
title: dayosyear ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird dayosyear () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2ac301365cb22849dc07ea137756f4093bea79b2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252386"
---
# <a name="dayofyear"></a>dayofyear()

Gibt die Ganzzahl zurück, die die Tagesnummer des angegebenen Jahrs darstellt.

```kusto
dayofyear(datetime(2015-12-14))
```

## <a name="syntax"></a>Syntax

`dayofweek(`*a_date*`)`

## <a name="arguments"></a>Argumente

* `a_date`: Ein `datetime`-Wert.

## <a name="returns"></a>Rückgabe

`day number` des angegebenen Jahres.