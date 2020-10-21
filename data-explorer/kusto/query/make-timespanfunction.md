---
title: make_timespan ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird make_timespan () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 93b8ed12bcfb83c39964f820e61f928357af62c8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253182"
---
# <a name="make_timespan"></a>make_timespan()

Erstellt einen [TimeSpan](./scalar-data-types/timespan.md) -Skalarwert aus dem angegebenen Zeitraum.

```kusto
make_timespan(1,12,30,55.123) == time(1.12:30:55.123)
```

## <a name="syntax"></a>Syntax

`make_timespan(`*Stunde*,*Minute*`)`

`make_timespan(`*Stunde*,*Minute*,*Sekunde*`)`

`make_timespan(`*Tag*,*Stunde*,*Minute*,*Sekunde*`)`

## <a name="arguments"></a>Argumente

* *Day*: Day (ein positiver ganzzahliger Wert)
* *Hour*: Stunde (ein ganzzahliger Wert von 0 bis 23)
* *Minute*: Minute (ein ganzzahliger Wert zwischen 0 und 59)
* *Second*: Second (ein echter Wert, von 0 bis 59,9999999)

## <a name="returns"></a>Rückgabe

Wenn die Erstellung erfolgreich ist, wird das Ergebnis als [TimeSpan](./scalar-data-types/timespan.md) -Wert verwendet. andernfalls ist das Ergebnis NULL.
 
## <a name="example"></a>Beispiel

```kusto
print ['timespan'] = make_timespan(1,12,30,55.123)

```

|Zeitraum|
|---|
|1.12:30:55.1230000|


