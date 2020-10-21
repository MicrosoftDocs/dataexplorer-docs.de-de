---
title: HourOfDay ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird HourOfDay () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6d83725e067f12d05382a905aee935f9433ef7a9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244715"
---
# <a name="hourofday"></a>hourofday()

Gibt die ganzzahlige Zahl zurück, die die Stundenzahl des angegebenen Datums darstellt.

```kusto
hourofday(datetime(2015-12-14 18:54)) == 18
```

## <a name="syntax"></a>Syntax

`hourofday(`*a_date*`)`

## <a name="arguments"></a>Argumente

* `a_date`: Ein `datetime`-Wert.

## <a name="returns"></a>Rückgabe

`hour number` des Tages (0-23).