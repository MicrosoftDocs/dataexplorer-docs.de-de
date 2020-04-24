---
title: week_of_year() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird week_of_year() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 1c3702165f01ab321f80bad900e59968092be2ed
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504538"
---
# <a name="week_of_year"></a>week_of_year()

Gibt eine ganze Zahl zurück, die die Wochennummer darstellt. Die Wochennummer wird ab der ersten Woche eines Jahres berechnet, die den ersten Donnerstag nach [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Week_dates)enthält.

```kusto
week_of_year(datetime("2015-12-14"))
```

**Syntax**

`week_of_year(`*a_date*`)`

**Argumente**

* `a_date`: Ein `datetime`-Wert.

**Rückgabe**

`week number`- Die Wochennummer, die das angegebene Datum enthält.

**Beispiele**

|Eingabe                                    |Output|
|-----------------------------------------|------|
|`week_of_year(datetime(2020-12-31))`     |`53`  |
|`week_of_year(datetime(2020-06-15))`     |`25`  |
|`week_of_year(datetime(1970-01-01))`     |`1`   |
|`week_of_year(datetime(2000-01-01))`     |`52`  |

> [!NOTE]
> `weekofyear()`ist eine veraltete Variante dieser Funktion. `weekofyear()`war nicht ISO 8601-konform; die erste Woche eines Jahres wurde als die Woche mit dem ersten Mittwoch des Jahres definiert.
Die aktuelle Version dieser `week_of_year()`Funktion, ist ISO 8601-kompatibel; die erste Woche eines Jahres wird als die Woche mit dem ersten Donnerstag des Jahres definiert.