---
title: series_periods_validate() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird series_periods_validate() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 8eba96e21513e776c984a356f88a705ca46485af
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663398"
---
# <a name="series_periods_validate"></a>series_periods_validate()

Überprüft, ob eine Zeitreihe periodische Muster bestimmter Längen enthält.  

Sehr oft ist eine Metrik, die den Datenverkehr einer Anwendung misst, durch wöchentliche und/oder tägliche Zeiträume gekennzeichnet. Dies kann durch `series_periods_validate()` die Durchführung der Überprüfung für einen wöchentlichen und täglichen Zeitraum bestätigt werden.

Die Funktion verwendet als Eingabe eine Spalte, die ein dynamisches Array von Zeitreihen `real` (in der Regel die resultierende Ausgabe des [Make-Series-Operators)](make-seriesoperator.md) und eine oder mehrere Zahlen enthält, die die Längen der zu validierenden Perioden definieren. 

Die Funktion gibt 2 Spalten aus:
* *Perioden*: ein dynamisches Array, das die zu validierenden Perioden enthält (in der Eingabe angegeben)
* *Partituren*: ein dynamisches Array, das eine Punktzahl zwischen 0 und 1 enthält, die die Bedeutung einer Periode in ihrer jeweiligen Position im *Periodenarray* misst

**Syntax**

`series_periods_validate(`*x*`,` *x-Periode1* [ `,` *Periode2* `,` . . . ] `)`

**Argumente**

* *x*: Dynamischer Array-Skalarausdruck, der ein Array numerischer Werte ist, in der Regel die resultierende Ausgabe von [Make-Series-](make-seriesoperator.md) oder make_list-Operatoren. [make_list](makelist-aggfunction.md)
* *periode1*, *periode2*usw.: Zahlen, `real` die die zu validierenden Zeiträume in Einheiten der Lagerplatzgröße angeben. Wenn sich die Serie z. B. in 1h-Abschnitten befindet, beträgt eine wöchentliche Periode 168 Abschnitte.

> [!IMPORTANT]
> * Der Mindestwert für *period* jedes Periodenargument ist **4** und der Maximalwert ist die Hälfte der Länge der Eingabereihe. für ein *Periodenargument* außerhalb dieser Grenzen beträgt der Ausgabewert **0**.
>
> * Die Eingabezeitreihen müssen regelmäßig sein, d.h. in konstanten Lagerplätzen aggregiert sein (was immer der Fall ist, wenn sie mit [Make-Series](make-seriesoperator.md)erstellt wurde). Andernfalls ist die Ausgabe bedeutungslos.
> 
> * Die Funktion akzeptiert bis zu 16 Zeiträume, um sie zu validieren.


**Beispiel**

Die folgende Abfrage bettet eine Momentaufnahme eines Monats des Datenverkehrs einer Anwendung ein, die zweimal täglich aggregiert wird (d. h. die Größe des Lagerplatzs beträgt 12 Stunden).

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/samples/series-periods.png" alt-text="Serienperioden":::

Wenn `series_periods_validate()` Sie in dieser Serie ausführen, um einen wöchentlichen Zeitraum (14 Punkte lang) zu validieren, ergibt sich eine hohe Punktzahl und eine **0** Punktzahl bei der Validierung eines Fünf-Tage-Zeitraums (10 Punkte lang).

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_validate(y, 14.0, 10.0)
```

| \_Serienperioden\_validieren\_y\_Perioden  | \_Serienperioden\_validieren\_y-Scores\_ |
|-------------|-------------------|
| [14.0, 10.0] | [0.84,0.0]  |