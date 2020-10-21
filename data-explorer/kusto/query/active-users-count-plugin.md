---
title: 'active_users_count-Plug-in: Azure Daten-Explorer'
description: In diesem Artikel wird active_users_count-Plug-in in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a35cbb4c9078d58f2c9de3c681453c578baf1476
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252871"
---
# <a name="active_users_count-plugin"></a>active_users_count-Plug-In

Berechnet die unterschiedliche Anzahl von Werten, wobei jeder Wert mindestens einer minimalen Anzahl von Zeitpunkten in einem Nachschlage Zeitraum angezeigt wird.

Eignet sich nur für die Berechnung der unterschiedlichen Anzahl von "Lüfter", ohne den Anschein von "nicht-Fans" zu schließen. Ein Benutzer wird nur dann als "Lüfter" gezählt, wenn er während des Nachschlage Zeitraums aktiv war. Der Such Zeitraum wird nur verwendet, um zu bestimmen, ob ein Benutzer als `active` "Lüfter" angesehen wird. In der Aggregation selbst sind keine Benutzer aus dem Nachschlage Fenster enthalten. Im Vergleich dazu wird die [sliding_window_counts](sliding-window-counts-plugin.md) Aggregation über ein gleitender Fenster des Nachschlage Zeitraums ausgeführt.

```kusto
T | evaluate active_users_count(id, datetime_column, startofday(ago(30d)), startofday(now()), 7d, 1d, 2, 7d, dim1, dim2, dim3)
```

## <a name="syntax"></a>Syntax

*T* `| evaluate` `active_users_count(` *idColumn* `,` *timelinecolenn* `,` *Start* `,` *Ende* `,` *lookbackwindow* `,` *Period* `,` *activeperiodscount* `,` *bin* `,` [*dim1* `,` *dim2* `,` ...]`)`

## <a name="arguments"></a>Argumente

* *T*: der tabellarische Eingabe Ausdruck.
* *IdColumn*: der Name der Spalte mit ID-Werten, die die Benutzeraktivität darstellen. 
* *Timelinecolenn*: der Name der Spalte, die die Zeitachse darstellt.
* *Start*: (optional) Skalar mit dem Wert des Start Zeitraums der Analyse.
* *End*: (optional) Skalar mit dem Wert des Analyse endzeitraums.
* *Lookbackwindow*: ein gleitender Zeitfenster, das einen Zeitraum definiert, in dem die Benutzer Darstellung aktiviert ist. Der Nachschlage Zeitraum beginnt bei ([Aktuelles aussehen]-[Nachschlage Fenster]) und endet am ([aktuelle Darstellung]). 
* *Period*: skalare Konstante Zeitspanne, die als einzelne Darstellung gezählt werden soll (ein Benutzer wird als aktiv gezählt, wenn er in mindestens einem separaten activeperiodscount-Wert dieser Zeitspanne angezeigt wird.
* *Activeperiodscount*: minimale Anzahl von unterschiedlichen aktiven Zeitpunkten, um zu entscheiden, ob der Benutzer aktiv ist. Aktive Benutzer sind Benutzer, die mindestens in der Anzahl aktiver Zeiträume (gleich oder größer als) aufgetreten sind.
* *Bin*: skalare Konstante Wert des Analyseschritt Zeitraums. Kann ein numerischer/DateTime-/timestamp-Wert oder eine Zeichenfolge sein, die ist `week` / `month` / `year` . Alle Zeiträume sind die entsprechenden starstarf [Week](startofweekfunction.md)-starto- / [Month](startofmonthfunction.md)- / [startofyear](startofyearfunction.md) Funktionen.
* *dim1*, *dim2*,...: (optional) Liste der Dimensions Spalten, in denen die Berechnung der Aktivitäts Metrik in Slice ist.

## <a name="returns"></a>Rückgabe

Gibt eine Tabelle zurück, die unterschiedliche Zählerwerte für IDs aufweist, die in activeperiodcounts in den folgenden Punkten aufgetreten sind: der Nachschlage Zeitraum, jeder Zeitachse und jede vorhandene Dimensions Kombination.

Das Ausgabe Tabellen Schema ist:

|*Timelinecolumschlag*|dim1|..|dim_n|dcount_values|
|---|---|---|---|---|
|Typ: ab *timelinecolumschlag*|..|..|..|long|


## <a name="examples"></a>Beispiele

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

Ein Benutzer gilt als aktiv, wenn er beide der folgenden Kriterien erfüllt: 
* Der Benutzer wurde in mindestens drei verschiedenen Tagen angezeigt (Period = 1D, activezeiträume = 3).
* Der Benutzer wurde in einem Nachschlage Fenster von 8D vor und einschließlich der aktuellen Darstellung angezeigt.

In der Abbildung unten sind die folgenden Instanzen, die von diesem Kriterium aktiv sind, die folgenden Instanzen: Benutzer A auf 7/20 und Benutzer B auf 7/4 (siehe Plug-in-Ergebnisse weiter oben). Der Anschein von Benutzer B ist für das Nachschlage Fenster in 7/4 enthalten, jedoch nicht für den Start-End Zeitbereich von 6/29-30. 

:::image type="content" source="images/queries/active-users-count.png" alt-text="Beispiel für aktive Benutzer Anzahl":::
