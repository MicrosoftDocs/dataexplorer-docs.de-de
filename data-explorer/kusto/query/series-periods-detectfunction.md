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
ms.openlocfilehash: 2ee587bfa7d01ae0602cc870e6c0b776593d53c0
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351334"
---
# <a name="series_periods_detect"></a>series_periods_detect()

Sucht die signifikantesten Zeiträume, die in einer Zeitreihe vorhanden sind.  

Häufig wird eine Metrik, die den Datenverkehr einer Anwendung misst, durch zwei bedeutende Zeiträume gekennzeichnet: wöchentlich und täglich. Die-Funktion `series_periods_detect()` erkennt diese beiden dominanten Zeiträume in einer Zeitreihe.  
Die Funktion nimmt als Eingabe an:
* Eine Spalte, die ein dynamisches Array von Zeitreihen enthält. In der Regel ist die Spalte die resultierende Ausgabe des Operators " [make-Series](make-seriesoperator.md) ".
* Zwei `real` Zahlen, die den minimalen und maximalen Zeitraum definieren, die Anzahl der zu suchenden Container. Beispielsweise wäre für einen 1-Stunden-bin die Größe eines täglichen Zeitraums 24. 
* Eine `long` Zahl, die die Gesamtzahl der Zeiträume für die zu durchsuchende Funktion definiert. 

Die Funktion gibt zwei Spalten aus:
* *Zeiträume*: ein dynamisches Array, das die Zeiträume enthält, die in Einheiten der bin-Größe gefunden wurden, geordnet nach ihren Ergebnissen.
* *Bewertungen*: ein dynamisches Array mit Werten zwischen 0 und 1. Jedes Array misst die Bedeutung eines Punkts an der jeweiligen Position im *Zeit* Array.
 
## <a name="syntax"></a>Syntax

`series_periods_detect(`*x* `,` *min_period* `,` *max_period* `,` *num_periods*`)`

## <a name="arguments"></a>Argumente

* *x*: Skalarausdruck des dynamischen Arrays, bei dem es sich um ein Array numerischer Werte handelt, in der Regel die resultierende Ausgabe der " [make-Series"-](make-seriesoperator.md) oder [make_list](makelist-aggfunction.md)
* *min_period*: eine `real` Zahl, die den minimalen Zeitraum angibt, nach dem gesucht werden soll.
* *max_period*: eine `real` Zahl, die den maximalen Zeitraum angibt, nach dem gesucht werden soll.
* *num_periods*: eine `long` Zahl, die die maximal zulässige Anzahl von Zeiträumen angibt. Diese Zahl ist die Länge der dynamischen Ausgabe Arrays.

> [!IMPORTANT]
> * Der Algorithmus kann Zeiträume mit mindestens 4 Punkten und höchstens der Hälfte der Reihen Länge erkennen. 
>
> * Legen Sie den *min_period* ein wenig weiter unten fest, und *max_period* ein wenig weiter oben, die Zeiträume, die in der Zeitreihe zu finden sind. Wenn Sie z. b. ein stündliches aggregiertes Signal haben und sowohl nach Tages-als auch wöchentlich (24 bis 168 Stunden) suchen, können Sie *min_period*= 0,8 \* 24, *max_period*= 1,2 \* 168 festlegen und 20% Ränder um diese Zeiträume belassen.
>
> * Die Eingabe Zeitreihe muss regelmäßig sein. Das heißt, dass Sie in konstanten Containern aggregiert werden, was immer der Fall ist, wenn Sie mithilfe von [make-Series](make-seriesoperator.md)erstellt wurde. Andernfalls ist die Ausgabe bedeutungslos.

## <a name="example"></a>Beispiel

Die folgende Abfrage bettet eine Momentaufnahme eines Monats für den Datenverkehr einer Anwendung ein, die zweimal täglich aggregiert wird. Die bin-Größe beträgt 12 Stunden.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/series-periods/series-periods.png" alt-text="Reihen Zeiträume":::

`series_periods_detect()`Die Ausführung für diese Serie ergibt sich im wöchentlichen Zeitraum, 14 Punkte.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_detect(y, 0.0, 50.0, 2)
```

| Reihen \_ Zeiträume \_ Erkennen von \_ y- \_ Zeiträumen  | Reihen \_ Zeiträume \_ Erkennen von \_ y- \_ Zeit \_ Punkten |
|-------------|-------------------|
| [14.0, 0.0] | [0,84, 0,0]  |


> [!NOTE] 
> Die tägliche Zeitspanne, die auch im Diagramm angezeigt werden kann, wurde nicht gefunden, da die Stichprobenentnahme zu grob ist (12h bin size). Daher liegt ein täglicher Zeitraum von 2 Containern unter dem minimalen Zeitraum von 4 Punkten, der für den Algorithmus erforderlich ist.
