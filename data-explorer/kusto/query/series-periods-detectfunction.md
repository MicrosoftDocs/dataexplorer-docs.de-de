---
title: series_periods_detect ()-Azure Daten-Explorer
description: In diesem Artikel wird series_periods_detect () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: a3f2a325b63306f7fec6b11eb3e684d3918bc7d5
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372520"
---
# <a name="series_periods_detect"></a>series_periods_detect()

Sucht die signifikantesten Zeiträume, die in einer Zeitreihe vorhanden sind.  

Häufig wird eine Metrik, die den Datenverkehr einer Anwendung misst, durch zwei bedeutende Zeiträume gekennzeichnet: wöchentlich und täglich. Bei einer solchen Zeit Reihe `series_periods_detect()` werden diese 2 dominanten Zeiträume von erkannt.  
Die Funktion nimmt als Eingabe eine Spalte an, die ein dynamisches Array von Zeitreihen enthält (in der Regel die resultierende Ausgabe des Operators " [make-Series](make-seriesoperator.md) "), zwei `real` Zahlen, die die minimale und die maximale Zeitspanne definieren (d. h. die Anzahl von Containern, z. b. für einen 1 `long` Die Funktion gibt 2 Spalten aus:
* *Zeiträume*: ein dynamisches Array mit den Punkten, die gefunden wurden (in Einheiten der bin-Größe), geordnet nach ihren Bewertungen
* *Bewertungen*: ein dynamisches Array mit Werten zwischen 0 und 1, die jeweils die Bedeutung eines Punkts an der jeweiligen Position im *Zeit* Array messen.
 
**Syntax**

`series_periods_detect(`*x* `,` *min_period* `,` *max_period* `,` *num_periods*`)`

**Argumente**

* *x*: Skalarausdruck des dynamischen Arrays, bei dem es sich um ein Array numerischer Werte handelt, in der Regel die resultierende Ausgabe der " [make-Series"-](make-seriesoperator.md) oder [make_list](makelist-aggfunction.md)
* *min_period*: eine `real` Zahl, die den minimalen Zeitraum angibt, nach dem gesucht werden soll.
* *max_period*: eine `real` Zahl, die den maximalen Zeitraum angibt, nach dem gesucht werden soll.
* *num_periods*: eine `long` Zahl, die die maximal zulässige Anzahl von Zeiträumen angibt. Dies ist die Länge der dynamischen Ausgabe Arrays.

> [!IMPORTANT]
> * Der Algorithmus kann Zeiträume mit mindestens 4 Punkten und höchstens der Hälfte der Reihen Länge erkennen. 
>
> * Legen Sie den *min_period* ein wenig weiter unten fest, und *max_period* Sie etwas oberhalb der Zeiträume, die Sie in der Zeitreihe erwarten. Wenn Sie z. b. ein stündliches, aggregiertes Signal haben und sowohl nach täglichen > als auch an wöchentlichen Perioden suchen (also 24 & 168), können Sie *min_period*= 0,8 \* 24, *max_period*= 1,2 168 festlegen und damit \* 20% Ränder um diese Zeiträume belassen.
>
> * Die Eingabe Zeitreihe muss regulär sein, d. h., Sie wird in konstanten Containern aggregiert (was immer der Fall ist, wenn Sie mithilfe von [make-Series](make-seriesoperator.md)erstellt wurde). Andernfalls ist die Ausgabe bedeutungslos.


**Beispiel**

Die folgende Abfrage bettet eine Momentaufnahme eines Monats für den Datenverkehr einer Anwendung ein, die zweimal pro Tag aggregiert wird (d. h. die Größe der Größe beträgt 12 Stunden).

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/series-periods/series-periods.png" alt-text="Reihen Zeiträume":::

`series_periods_detect()`Die Ausführung für diese Serie führt zu einem wöchentlichen Zeitraum (14 Punkte lang):

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_detect(y, 0.0, 50.0, 2)
```

| Reihen \_ Zeiträume \_ Erkennen von \_ y- \_ Zeiträumen  | Reihen \_ Zeiträume \_ Erkennen von \_ y- \_ Zeit \_ Punkten |
|-------------|-------------------|
| [14.0, 0.0] | [0,84, 0,0]  |


Beachten Sie, dass die tägliche Zeitspanne, die auch im Diagramm angezeigt werden kann, nicht gefunden wurde, weil die Stichprobenentnahme zu grob ist (12h bin size), sodass für einen täglichen Zeitraum von 2 Containern die minimale Dauer von 4 Punkten, die für den Algorithmus erforderlich sind, verniedrig ist.
