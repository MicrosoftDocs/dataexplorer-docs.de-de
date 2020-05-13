---
title: series_periods_validate ()-Azure Daten-Explorer
description: In diesem Artikel wird series_periods_validate () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: b157c7fef0b9b4d98f08f5e5020803eea3960097
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372487"
---
# <a name="series_periods_validate"></a>series_periods_validate()

Überprüft, ob eine Zeitreihe periodische Muster mit angegebenen Längen enthält.  

Sehr häufig ist eine Metrik, die den Datenverkehr einer Anwendung misst, durch eine wöchentliche und/oder tägliche Zeitspanne gekennzeichnet. Dies kann bestätigt werden, indem Sie die `series_periods_validate()` Überprüfung wöchentlich und täglicher Zeiträume ausführen.

Die Funktion nutzt als Eingabe eine Spalte, die ein dynamisches Array von Zeitreihen (in der Regel die resultierende Ausgabe des Operators " [make-Series](make-seriesoperator.md) ") enthält, und eine oder mehrere `real` Zahlen, die die Längen der zu validierenden Zeiträume definieren. 

Die Funktion gibt 2 Spalten aus:
* *Zeiträume*: ein dynamisches Array mit den zu validierenden Punkten (in der Eingabe angegeben)
* *Bewertungen*: ein dynamisches Array, das eine Bewertung zwischen 0 und 1 enthält, mit der die Bedeutung eines Punkts an der jeweiligen Position im *Zeit* Array gemessen wird.

**Syntax**

`series_periods_validate(`*x* `,` *period1* [ `,` *period2* `,` . . . ] `)`

**Argumente**

* *x*: Skalarausdruck des dynamischen Arrays, bei dem es sich um ein Array numerischer Werte handelt, in der Regel die resultierende Ausgabe der " [make-Series"-](make-seriesoperator.md) oder [make_list](makelist-aggfunction.md)
* *period1*, *period2*usw.: `real` Zahlen, die die zu validierenden Zeiträume in Einheiten der bin-Größe angeben. Wenn sich die Reihe z. b. in 1-Stunden-Containern befindet, beträgt die wöchentliche Zeit 168 Behälter.

> [!IMPORTANT]
> * Der minimale Wert für jedes der *Period* -Argumente beträgt **4** , und der Höchstwert ist die Hälfte der Länge der Eingabe Reihe. bei einem *Period* -Argument außerhalb dieser Begrenzungen ist das Ausgabe Ergebnis **0**.
>
> * Die Eingabe Zeitreihe muss regulär sein, d. h., Sie wird in konstanten Containern aggregiert (was immer der Fall ist, wenn Sie mithilfe von [make-Series](make-seriesoperator.md)erstellt wurde). Andernfalls ist die Ausgabe bedeutungslos.
> 
> * Die-Funktion akzeptiert bis zu 16 Zeiträume für die Validierung.


**Beispiel**

Die folgende Abfrage bettet eine Momentaufnahme eines Monats für den Datenverkehr einer Anwendung ein, die zweimal pro Tag aggregiert wird (d. h. die Größe der Größe beträgt 12 Stunden).

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/series-periods/series-periods.png" alt-text="Reihen Zeiträume":::

`series_periods_validate()`Das Ausführen von auf dieser Reihe zum Überprüfen eines wöchentlichen Zeitraums (14 Punkte) führt zu einer hohen Bewertung und mit einer Bewertung von **0** (null) bei der Überprüfung eines Zeitraums von fünf Tagen (10 Punkte).

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_validate(y, 14.0, 10.0)
```

| Reihen \_ Zeiträume \_ validieren \_ y- \_ Zeiträume  | Reihen \_ Zeiträume \_ Validieren von \_ y- \_ Bewertungen |
|-------------|-------------------|
| [14,0, 10,0] | [0,84, 0,0]  |
