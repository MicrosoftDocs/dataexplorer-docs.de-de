---
title: make_timespan() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird make_timespan() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 31301f684ea700cf89e9234c4c43adab068319b7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512766"
---
# <a name="make_timespan"></a>make_timespan()

Erstellt einen Skalarwert für die [Zeitspanne](./scalar-data-types/timespan.md) aus dem angegebenen Zeitraum.

```kusto
make_timespan(1,12,30,55.123) == time(1.12:30:55.123)
```

**Syntax**

`make_timespan(`*Stunde,**Minute*`)`

`make_timespan(`*Stunde*,*Minute*,*Sekunde*`)`

`make_timespan(`*Tag*,*Stunde*,*Minute*,*Sekunde*`)`

**Argumente**

* *Tag*: Tag (ein positiver Ganzzahlwert)
* *Stunde*: Stunde (ein Ganzzahlwert, von 0 bis 23)
* *Minute*: Minute (ein Ganzzahlwert, von 0 bis 59)
* *Sekunde*: Sekunde (ein realer Wert, von 0 bis 59,9999999)

**Rückgabe**

Wenn die Erstellung erfolgreich ist, ist das Ergebnis ein [Zeitspanwert,](./scalar-data-types/timespan.md) andernfalls ist das Ergebnis null.
 
**Beispiel**

```kusto
print ['timespan'] = make_timespan(1,12,30,55.123)

```

|Zeitraum|
|---|
|1.12:30:55.1230000|


