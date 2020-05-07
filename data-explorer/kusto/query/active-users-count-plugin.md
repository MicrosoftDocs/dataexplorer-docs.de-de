---
title: 'active_users_count-Plug-in: Azure Daten-Explorer'
description: In diesem Artikel wird active_users_count-Plug-in in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 22d3744cfa83a003830acc07710fd459003dbf20
ms.sourcegitcommit: 9fe6ee7db15a5cc92150d3eac0ee175f538953d2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/07/2020
ms.locfileid: "82907204"
---
# <a name="active_users_count-plugin"></a>Plug-in active_users_count

Berechnet die unterschiedliche Anzahl von Werten, wobei jeder Wert mindestens einer minimalen Anzahl von Zeitpunkten in einem Nachschlage Zeitraum angezeigt wird.

Eignet sich nur für die Berechnung der unterschiedlichen Anzahl von "Lüfter", ohne den Anschein von "nicht-Fans" zu schließen. Ein Benutzer wird nur dann als "Lüfter" gezählt, wenn er während des Nachschlage Zeitraums aktiv war. Der Such Zeitraum wird nur verwendet, um zu bestimmen, ob ein Benutzer `active` als "Lüfter" angesehen wird. In der Aggregation selbst sind keine Benutzer aus dem Nachschlage Fenster enthalten. Im Vergleich dazu wird die [sliding_window_counts](sliding-window-counts-plugin.md) Aggregation über ein gleitender Fenster des Nachschlage Zeitraums ausgeführt.

```kusto
T | evaluate active_users_count(id, datetime_column, startofday(ago(30d)), startofday(now()), 7d, 1d, 2, 7d, dim1, dim2, dim3)
```

**Syntax**

*T* `| evaluate` `,` *Bin* `,` *dim1* `,` *dim2* `,` *Start* `,` `,` *Period* `,` *IdColumn* `,` `,` *LookbackWindow* *End* *ActivePeriodsCount* *TimelineColumn* idColumn`,` timelinecolenn Start Ende lookbackwindow period activeperiodscount bin [dim1 dim2...] `active_users_count(``)`

**Argumente**

* *T*: der tabellarische Eingabe Ausdruck.
* *IdColumn*: der Name der Spalte mit ID-Werten, die die Benutzeraktivität darstellen. 
* *Timelinecolenn*: der Name der Spalte, die die Zeitachse darstellt.
* *Start*: (optional) Skalar mit dem Wert des Start Zeitraums der Analyse.
* *End*: (optional) Skalar mit dem Wert des Analyse endzeitraums.
* *Lookbackwindow*: ein gleitender Zeitfenster, das einen Zeitraum definiert, in dem die Benutzer Darstellung aktiviert ist. Der Nachschlage Zeitraum beginnt bei ([Aktuelles aussehen]-[Nachschlage Fenster]) und endet am ([aktuelle Darstellung]). 
* *Period*: skalare Konstante Zeitspanne, die als einzelne Darstellung gezählt werden soll (ein Benutzer wird als aktiv gezählt, wenn er in mindestens einem separaten activeperiodscount-Wert dieser Zeitspanne angezeigt wird.
* *Activeperiodscount*: minimale Anzahl von unterschiedlichen aktiven Zeitpunkten, um zu entscheiden, ob der Benutzer aktiv ist. Aktive Benutzer sind Benutzer, die mindestens in der Anzahl aktiver Zeiträume (gleich oder größer als) aufgetreten sind.
* *Bin*: skalare Konstante Wert des Analyseschritt Zeitraums. Kann ein numerischer/DateTime-/timestamp-Wert oder eine Zeichenfolge `week` / `month` / `year`sein, die ist. Alle Zeiträume sind die entsprechenden starstarf [Week](startofweekfunction.md)/-starto-[Month](startofmonthfunction.md)/[-Funktionen.](startofyearfunction.md)
* *dim1*, *dim2*,...: (optional) Liste der Dimensions Spalten, in denen die Berechnung der Aktivitäts Metrik in Slice ist.

**Rückgabe**

Gibt eine Tabelle zurück, die unterschiedliche Zählerwerte für IDs aufweist, die in activeperiodcounts in den folgenden Punkten aufgetreten sind: der Nachschlage Zeitraum, jeder Zeitachse und jede vorhandene Dimensions Kombination.

Das Ausgabe Tabellen Schema ist:

|*Timelinecolumschlag*|dim1|..|dim_n|dcount_values|
|---|---|---|---|---|
|Typ: ab *timelinecolumschlag*|..|..|..|long|


**Beispiele**

Berechnen Sie die wöchentliche Anzahl der unterschiedlichen Benutzer, die in einem Zeitraum von acht Tagen in mindestens drei Tagen aufgetreten sind. Zeitraum der Analyse: Juli 2018.

```kusto
let Start = datetime(2018-07-01);
let End = datetime(2018-07-31);
let LookbackWindow = 8d;
let Period = 1d;
let ActivePeriods = 3;
let Bin = 7d; 
let T =  datatable(User:string, Timestamp:datetime)
[
    "B",      datetime(2018-06-29),
    "B",      datetime(2018-06-30),
    "A",      datetime(2018-07-02),
    "B",      datetime(2018-07-04),
    "B",      datetime(2018-07-08),
    "A",      datetime(2018-07-10),
    "A",      datetime(2018-07-14),
    "A",      datetime(2018-07-17),
    "A",      datetime(2018-07-20),
    "B",      datetime(2018-07-24)
]; 
T | evaluate active_users_count(User, Timestamp, Start, End, LookbackWindow, Period, ActivePeriods, Bin)



```

|Timestamp|`dcount`|
|---|---|
|2018-07-01 00:00:00.0000000|1|
|2018-07-15 00:00:00.0000000|1|

Ein Benutzer wird als aktiv betrachtet, wenn er eines der folgenden Kriterien erfüllt: 
* Der Benutzer wurde in mindestens drei verschiedenen Tagen angezeigt (Period = 1D, activezeiträume = 3).
* Der Benutzer wurde in einem Nachschlage Fenster von 8D vor und einschließlich der aktuellen Darstellung angezeigt.

In der Abbildung unten sind die folgenden Instanzen, die von diesem Kriterium aktiv sind, die folgenden Instanzen: Benutzer A auf 7/20 und Benutzer B auf 7/4 (siehe Plug-in-Ergebnisse weiter oben). Der Anschein von Benutzer B ist für das Nachschlage Fenster auf 7/4, jedoch nicht für den Start-End-Zeitbereich von 6/29-30 enthalten. 

:::image type="content" source="images/queries/active-users-count.png" alt-text="Beispiel für aktive Benutzer Anzahl":::
