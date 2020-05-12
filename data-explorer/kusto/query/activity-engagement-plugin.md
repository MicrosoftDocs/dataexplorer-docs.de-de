---
title: 'activity_engagement-Plug-in: Azure Daten-Explorer'
description: In diesem Artikel wird activity_engagement-Plug-in in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9aa85bcb12cd5f8d836f58ea9d16a318d8a40506
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225954"
---
# <a name="activity_engagement-plugin"></a>activity_engagement-Plug-In

Berechnet das Aktivitäts-Beteiligungs-Verhältnis auf Grundlage der ID-Spalte über ein gleitendes Zeitachsenfenster.

activity_engagement-Plug-in kann zum Berechnen von Dau/WAU/Mau (tägliche/wöchentliche/monatliche Aktivitäten) verwendet werden.

```kusto
T | evaluate activity_engagement(id, datetime_column, 1d, 30d)
```

**Syntax**

*T* `| evaluate` `activity_engagement(` *idColumn* `,` *timelinecolenn* `,` [*Start* `,` *Ende* `,` ] *inneractivitywindow* `,` *outeractivitywindow* [ `,` *dim1* `,` *dim2* `,` ...]`)`

**Argumente**

* *T*: der tabellarische Eingabe Ausdruck.
* *IdColumn*: der Name der Spalte mit ID-Werten, die die Benutzeraktivität darstellen. 
* *Timelinecolren*: der Name der Spalte, die die Zeitachse darstellt.
* *Start*: (optional) Skalar mit dem Wert des Start Zeitraums der Analyse.
* *End*: (optional) Skalar mit dem Wert des Analyse endzeitraums.
* *Inneractivitywindow*: Skalar mit dem Wert des Fenster Zeitraums für die innere Bereichs Analyse.
* *Outeractivitywindow*: Skalar mit dem Wert des Fenster Zeitraums für den äußeren Gültigkeitsbereich.
* *dim1*, *dim2*,...: (optional) Liste der Dimensions Spalten, in denen die Berechnung der Aktivitäts Metrik in Slice ist.

**Rückgabe**

Gibt eine Tabelle zurück, die (unterschiedliche Anzahl von ID-Werten im Fenster des inneren Bereichs, eine eindeutige Anzahl von ID-Werten im äußeren Gültigkeitsbereich und das aktivitätsverhältnis) für jeden Fenster Zeitraum im inneren Gültigkeitsbereich und für jede vorhandene Dimensions Kombination enthält.

Das Ausgabe Tabellen Schema ist:

|Timelinecolumschlag|dcount_activities_inner|dcount_activities_outer|activity_ratio|dim1|..|dim_n|
|---|---|---|---|--|--|--|--|--|--|
|Typ: ab *timelinecolumschlag*|long|long|double|..|..|..|


**Beispiele**

### <a name="dauwau-calculation"></a>Berechnung von Dau/WAU

Im folgenden Beispiel wird das Verhältnis von Dau/WAU (tägliche aktive Benutzer/wöchentliche aktive Benutzer) über ein zufällig generiertes Daten berechnet.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-01-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// Calculate DAU/WAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 7d)
| project _day, Dau_Wau=activity_ratio*100 
| render timechart 
```

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-wau.png" border="false" alt-text="Aktivitäts Engagement, Dau WAU":::

### <a name="daumau-calculation"></a>Berechnung von Dau/Mau

Im folgenden Beispiel wird das Verhältnis von Dau/WAU (tägliche aktive Benutzer/wöchentliche aktive Benutzer) über ein zufällig generiertes Daten berechnet.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-05-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// Calculate DAU/MAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 30d)
| project _day, Dau_Mau=activity_ratio*100 
| render timechart 
```

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-mau.png" border="false" alt-text="Activity Engagement, Dau Mau":::

### <a name="daumau-calculation-with-additional-dimensions"></a>Dau/Mau-Berechnung mit zusätzlichen Dimensionen

Im folgenden Beispiel wird das Verhältnis von Dau/WAU (tägliche aktive Benutzer/wöchentliche aktive Benutzer) über ein zufällig generiertes Daten mit zusätzlicher Dimension ( `mod3` ) berechnet.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-05-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
| extend mod3 = strcat("mod3=", id % 3)
// Calculate DAU/MAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 30d, mod3)
| project _day, Dau_Mau=activity_ratio*100, mod3 
| render timechart 
```

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-mau-mod3.png" border="false" alt-text="Activity Engagement, Dau Mau Mod 3":::
