---
title: WEEK_OF_YEAR ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird WEEK_OF_YEAR () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 82678a68166061fc7b8a30c7cb2e019c8d3d9e0c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338557"
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

`week number`-Die Wochen Nummer, die das angegebene Datum enthält.

## <a name="examples"></a>Beispiele

|Eingabe                                    |Output|
|-----------------------------------------|------|
|`week_of_year(datetime(2020-12-31))`     |`53`  |
|`week_of_year(datetime(2020-06-15))`     |`25`  |
|`week_of_year(datetime(1970-01-01))`     |`1`   |
|`week_of_year(datetime(2000-01-01))`     |`52`  |

> [!NOTE]
> `weekofyear()`ist eine veraltete Variante dieser Funktion. `weekofyear()`war nicht ISO 8601-kompatibel. die erste Woche eines Jahres wurde als Woche mit dem ersten Mittwoch des Jahrs definiert.
Die aktuelle Version dieser Funktion `week_of_year()` ist ISO 8601-kompatibel. die erste Woche eines Jahres wird als Woche mit dem ersten Donnerstag des Jahrs definiert.