---
title: series_periods_validate ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird series_periods_validate () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 89ac06f2d2bbb376f08cf3fd88d316a7fcab0594
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618603"
---
# <a name="series_periods_validate"></a>series_periods_validate()

Überprüft, ob eine Zeitreihe periodische Muster mit angegebenen Längen enthält.  

Sehr häufig ist eine Metrik, die den Datenverkehr einer Anwendung misst, durch eine wöchentliche und/oder tägliche Zeitspanne gekennzeichnet. Dies kann bestätigt werden, indem `series_periods_validate()` Sie die Überprüfung wöchentlich und täglicher Zeiträume ausführen.

Die Funktion nutzt als Eingabe eine Spalte, die ein dynamisches Array von Zeitreihen (in der Regel die resultierende Ausgabe des Operators " [make-Series](make-seriesoperator.md) ") `real` enthält, und eine oder mehrere Zahlen, die die Längen der zu validierenden Zeiträume definieren. 

Die Funktion gibt 2 Spalten aus:
* *Zeiträume*: ein dynamisches Array mit den zu validierenden Punkten (in der Eingabe angegeben)
* *Bewertungen*: ein dynamisches Array, das eine Bewertung zwischen 0 und 1 enthält, mit der die Bedeutung eines Punkts an der jeweiligen Position im *Zeit* Array gemessen wird.

**Syntax**

`series_periods_validate(`*x* `,` *period1* [ `,` *period2* period2 `,` . . . ] `)`

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

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/series-periods/series-periods.png" alt-text="Reihen Zeiträume":::

Das `series_periods_validate()` Ausführen von auf dieser Reihe zum Überprüfen eines wöchentlichen Zeitraums (14 Punkte) führt zu einer hohen Bewertung und mit einer Bewertung von **0** (null) bei der Überprüfung eines Zeitraums von fünf Tagen (10 Punkte).

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_validate(y, 14.0, 10.0)
```

| Reihen\_Zeiträume\_\_validieren\_y-Zeiträume  | Reihen\_Zeiträume\_\_validieren\_von y-Bewertungen |
|-------------|-------------------|
| [14,0, 10,0] | [0,84, 0,0]  |