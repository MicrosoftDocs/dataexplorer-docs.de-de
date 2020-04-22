---
title: activity_engagement-Plugin - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird activity_engagement-Plugin in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a7ed7ad7ebef425160b64b792ff75c95d4c4fcee
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030310"
---
# <a name="activity_engagement-plugin"></a>activity_engagement-Plug-In

Berechnet das Aktivitäts-Beteiligungs-Verhältnis auf Grundlage der ID-Spalte über ein gleitendes Zeitachsenfenster.

activity_engagement Plugin kann zur Berechnung von DAU/WAU/MAU (tägliche/wöchentliche/monatliche Aktivitäten) verwendet werden.

```kusto
T | evaluate activity_engagement(id, datetime_column, 1d, 30d)
```

**Syntax**

*T* `| evaluate` `,` `,``,` `,` `,` `,` *IdColumn* `,` *TimelineColumn* `,` *End**Start* *dim2* *dim1* *OuterActivityWindow* *InnerActivityWindow* IdColumn TimelineColumn [ Startend ] InnerActivityWindow OuterActivityWindow [ dim1 dim2 ...] `activity_engagement(``)`

**Argumente**

* *T*: Der tabellarische Eingabeausdruck.
* *IdColumn*: Der Name der Spalte mit ID-Werten, die die Benutzeraktivität darstellen. 
* *TimelineColumn*: Der Name der Spalte, die die Zeitachse darstellt.
* *Start*: (optional) Skalar mit Wert des Analysestartzeitraums.
* *Ende*: (optional) Skalar mit Wert der Analyseendperiode.
* *InnerActivityWindow*: Skalar mit Wert des Fensterzeitraums für die Analyse des inneren Bereichs.
* *OuterActivityWindow*: Skalar mit Wert des Fensterzeitraums für die Analyse des äußeren Bereichs.
* *dim1*, *dim2*, ...: (optional) Liste der Dimensionsspalten, die die Berechnung der Aktivitätsmetriken aufteilen.

**Rückgabe**

Gibt eine Tabelle zurück, die (unterschiedliche Anzahl von ID-Werten innerhalb des Fensters des inneren Bereichs, eindeutige Anzahl von ID-Werten innerhalb des Fensters des äußeren Bereichs und das Aktivitätsverhältnis) für jede Fensterperiode des inneren Bereichs und für jede vorhandene Dimensionskombination aufweist.

Ausgabetabellenschema ist:

|TimelineColumn|dcount_activities_inner|dcount_activities_outer|activity_ratio|dim1|..|dim_n|
|---|---|---|---|--|--|--|--|--|--|
|Typ: ab *TimelineColumn*|long|long|double|..|..|..|


**Beispiele**

### <a name="dauwau-calculation"></a>DAU/WAU-Berechnung

Im folgenden Beispiel wird DAU/WAU (Daily Active Users / Weekly Active Users ratio) über zufällig generierte Daten berechnet.

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

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-wau.png" border="false" alt-text="Aktivitätsengagement dau wau":::

### <a name="daumau-calculation"></a>DAU/MAU-Berechnung

Im folgenden Beispiel wird DAU/WAU (Daily Active Users / Weekly Active Users ratio) über zufällig generierte Daten berechnet.

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

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-mau.png" border="false" alt-text="Aktivitätsengagement dau mau":::

### <a name="daumau-calculation-with-additional-dimensions"></a>DAU/MAU-Berechnung mit zusätzlichen Abmessungen

Im folgenden Beispiel wird DAU/WAU (Daily Active Users / Weekly Active Users ratio) über eine zufällig generierte Daten mit zusätzlicher Dimension (`mod3`) berechnet.

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

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-mau-mod3.png" border="false" alt-text="Aktivitätengagement dau mau mod 3":::
