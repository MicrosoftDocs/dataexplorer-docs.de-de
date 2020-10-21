---
title: WEEK_OF_YEAR ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird WEEK_OF_YEAR () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 18cad42dfd0f652daa4c8da80524ba40ace9b153
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251233"
---
# <a name="week_of_year"></a>WEEK_OF_YEAR ()

Gibt eine ganze Zahl zurück, die die Wochen Nummer darstellt. Die Wochen Nummer wird von der ersten Woche eines Jahres berechnet, die den ersten Donnerstag gemäß [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Week_dates)enthält.

```kusto
week_of_year(datetime("2015-12-14"))
```

## <a name="syntax"></a>Syntax

`week_of_year(`*a_date*`)`

## <a name="arguments"></a>Argumente

* `a_date`: Ein `datetime`-Wert.

## <a name="returns"></a>Rückgabe

`week number` -Die Wochen Nummer, die das angegebene Datum enthält.

## <a name="examples"></a>Beispiele

|Eingabe                                    |Output|
|-----------------------------------------|------|
|`week_of_year(datetime(2020-12-31))`     |`53`  |
|`week_of_year(datetime(2020-06-15))`     |`25`  |
|`week_of_year(datetime(1970-01-01))`     |`1`   |
|`week_of_year(datetime(2000-01-01))`     |`52`  |

> [!NOTE]
> `weekofyear()` ist eine veraltete Variante dieser Funktion. `weekofyear()` war nicht ISO 8601-kompatibel. die erste Woche eines Jahres wurde als Woche mit dem ersten Mittwoch des Jahrs definiert.
Die aktuelle Version dieser Funktion `week_of_year()` ist ISO 8601-kompatibel. die erste Woche eines Jahres wird als Woche mit dem ersten Donnerstag des Jahrs definiert.