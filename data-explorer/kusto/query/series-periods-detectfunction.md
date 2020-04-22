---
title: series_periods_detect() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird series_periods_detect() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 0bc78f93270809774504c1eccbc9fa2bbce6c964
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663443"
---
# <a name="series_periods_detect"></a>series_periods_detect()

Sucht die wichtigsten Zeiträume, die in einer Zeitreihe vorhanden sind.  

Sehr oft ist eine Metrik, die den Datenverkehr einer Anwendung misst, durch zwei signifikante Zeiträume gekennzeichnet: eine wöchentliche und eine tägliche. Bei einer solchen `series_periods_detect()` Zeitreihe müssen diese beiden beherrschenden Zeiträume ermittelt werden.  
Die Funktion nimmt als Eingabe eine Spalte mit einem dynamischen Array von Zeitreihen `real` (in der Regel die resultierende Ausgabe des [Make-Series-Operators),](make-seriesoperator.md) zwei Zahlen, die die minimale und maximale Periodengröße definieren `long` (d. h. Anzahl der Abschnitte, z. B. für 1h Bin die Größe eines Tageszeitraums wäre 24) zu suchen, und eine Zahl, die die Gesamtzahl der Perioden für die Funktion zu suchen definiert. Die Funktion gibt 2 Spalten aus:
* *Perioden*: ein dynamisches Array, das die gefundenen Perioden (in Einheiten der Lagerplatzgröße) enthält, sortiert nach ihren Werten
* *Scores*: ein dynamisches Array, das Werte zwischen 0 und 1 enthält, misst jeweils die Bedeutung einer Periode in ihrer jeweiligen Position im *Periodenarray*
 
**Syntax**

`series_periods_detect(`*x* `,` *min_period* `,` *max_period* `,` *num_periods*`)`

**Argumente**

* *x*: Dynamischer Array-Skalarausdruck, der ein Array numerischer Werte ist, in der Regel die resultierende Ausgabe von [Make-Series-](make-seriesoperator.md) oder make_list-Operatoren. [make_list](makelist-aggfunction.md)
* *min_period*: `real` Eine Zahl, die den minimalen Zeitraum angibt, nach dem gesucht werden soll.
* *max_period*: `real` Eine Zahl, die den maximalen Zeitraum angibt, nach dem gesucht werden soll.
* *num_periods*: `long` Eine Zahl, die die maximal erforderliche Anzahl von Perioden angibt. Dies ist die Länge der dynamischen Ausgabearrays.

> [!IMPORTANT]
> * Der Algorithmus kann Perioden mit mindestens 4 Punkten und höchstens der Hälfte der Serienlänge erkennen. 
>
> * Sie sollten die *min_period* ein wenig unten und *max_period* ein wenig über die Zeiträume, die Sie erwarten, in der Zeitreihe zu finden. Wenn Sie z. B. ein stündlich aggregiertes Signal haben und sowohl nach tages- > als auch nach wöchentlichen Perioden\*suchen (das wären\*24 & 168), können Sie *min_period*=0,8 24, *max_period*=1,2 168 festlegen, sodass 20 % Margen um diese Zeiträume herum übrig bleiben.
>
> * Die Eingabezeitreihen müssen regelmäßig sein, d.h. in konstanten Lagerplätzen aggregiert sein (was immer der Fall ist, wenn sie mit [Make-Series](make-seriesoperator.md)erstellt wurde). Andernfalls ist die Ausgabe bedeutungslos.


**Beispiel**

Die folgende Abfrage bettet eine Momentaufnahme eines Monats des Datenverkehrs einer Anwendung ein, die zweimal täglich aggregiert wird (d. h. die Größe des Lagerplatzs beträgt 12 Stunden).

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/samples/series-periods.png" alt-text="Serienperioden":::

Laufen `series_periods_detect()` auf dieser Serie ergibt sich im Wochenzeitraum (14 Punkte lang):

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_detect(y, 0.0, 50.0, 2)
```

| \_Reihenperioden\_\_\_erkennen y-Perioden  | Serienperioden\_\_\_\_erkennen\_y Perioden-Scores |
|-------------|-------------------|
| [14.0, 0.0] | [0.84, 0.0]  |


Beachten Sie, dass die tägliche Periode, die auch im Diagramm gesehen werden kann, nicht gefunden wurde, da die Probenahme zu grob ist (12h Bin-Größe), so dass eine tägliche Periode von 2 Bins die minimale Periodengröße von 4 Punkten, die vom Algorithmus benötigt wird, niedrig ist.