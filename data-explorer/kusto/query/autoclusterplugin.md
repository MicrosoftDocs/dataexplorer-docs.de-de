---
title: 'Autocluster-Plug-in: Azure-Daten-Explorer'
description: Dieser Artikel beschreibt das Autocluster-Plug-in in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 959b11eca2dc369a3f737e01175f77ff6626f773
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803844"
---
# <a name="autocluster-plugin"></a>autocluster-Plug-In

```kusto
T | evaluate autocluster()
```

`autocluster`findet allgemeine Muster diskreter Attribute (Dimensionen) in den Daten. Anschließend werden die Ergebnisse der ursprünglichen Abfrage, unabhängig davon, ob es sich um 100 oder 100.000 Zeilen handelt, auf eine kleine Anzahl von Mustern reduziert. Das Plug-in wurde entwickelt, um Fehler zu analysieren (z. b. Ausnahmen oder Abstürze), kann aber potenziell an jedem gefilterten Dataset arbeiten.

> [!NOTE]
> `autocluster`basiert größtenteils auf dem Seed-Expand-Algorithmus aus dem folgenden Dokument: [Algorithmen für das telemetriedatenmining mit diskreten Attributen](https://www.scitepress.org/DigitalLibrary/PublicationsDetail.aspx?ID=d5kcrO+cpEU=&t=1). 


## <a name="syntax"></a>Syntax

`T | evaluate autocluster(`*Argumente*`)`

## <a name="returns"></a>Gibt zurück

Das `autocluster` Plug-in gibt eine (in der Regel kleine) Gruppe von Mustern zurück. Die Muster erfassen Teile der Daten mit gemeinsamen gemeinsamen Werten über mehrere diskrete Attribute hinweg. Jedes Muster in den Ergebnissen wird durch eine Zeile dargestellt.

Die erste Spalte ist die Segment-ID. Die nächsten beiden Spalten enthalten die Anzahl und den Prozentsatz der Zeilen aus der ursprünglichen Abfrage, die mit dem Muster erfasst wurden. Die übrigen Spalten stammen aus der ursprünglichen Abfrage. Der Wert ist entweder ein bestimmter Wert aus der Spalte oder ein Platzhalter Wert (standardmäßig NULL), was Variablen Werte bedeutet.

Die Muster sind nicht eindeutig, können sich überlappen und in der Regel nicht alle ursprünglichen Zeilen abdecken. Einige Zeilen fallen ggf. nicht in eines der Muster.

> [!TIP]
> Verwenden Sie [Where](./whereoperator.md) und [Project](./projectoperator.md) in der eingabepipe, um die Daten auf die für Sie interessanten Daten zu reduzieren.
>
> Wenn Sie eine interessante Zeile finden, können Sie dafür einen Drilldown durchführen, indem Sie die jeweiligen Werte dem `where` -Filter hinzufügen.

## <a name="arguments"></a>Argumente 

> [!NOTE] 
> Alle Argumente sind optional.

`T | evaluate autocluster(`[*Sizeweight*, *weightcolumn*, *numseeds*, *customwildcard*, *customwildcard*,...]`)`

Alle Argumente sind optional, aber sie müssen wie oben angegeben sortiert werden. Um anzugeben, dass der Standardwert verwendet werden soll, fügen Sie die Zeichenfolge Tilde value ' ~ ' ein (siehe die Spalte ' example ' in der Tabelle).

|Argument        | Typ, Bereich, Standard              |Beschreibung                | Beispiel                                        |
|----------------|-----------------------------------|---------------------------|------------------------------------------------|
| SizeWeight     | 0 < *Double* < 1 [Standard: 0,5]   | Ermöglicht Ihnen die Kontrolle über das Gleichgewicht zwischen generischer (hoher Abdeckung) und informativen (vielen gemeinsam genutzten) Werten. Wenn Sie den Wert erhöhen, wird die Anzahl der Muster in der Regel verringert, und jedes Muster deckt tendenziell eine größere prozentuale Abdeckung ab. Wenn Sie den Wert verringern, werden normalerweise spezifischere Muster mit mehr gemeinsam genutzten Werten und eine geringere prozentuale Abdeckung erzeugt. Die Formel für die unter-der-Haube ist eine gewichtete geometrische Mittel zwischen der normalisierten generischen Bewertung und dem informativen Ergebnis mit Gewichtungen `SizeWeight` und`1-SizeWeight`                   | `T | evaluate autocluster(0.8)`                |
|Weightcolumn    | *column_name*                     | Berücksichtigt jede Zeile in der Eingabe gemäß dem angegebenen Gewicht (standardmäßig verfügt jede Spalte über eine Gewichtung von „1“). Das Argument muss ein Name einer numerischen Spalte sein (z. b. int, Long, Real). Eine übliche Nutzung einer Gewichtungsspalte besteht darin, die Stichprobenerstellung oder die Bucket-Zuordnung/Aggregation der Daten zu berücksichtigen, die bereits in die einzelnen Zeilen eingebettet sind.                                                                                                       | `T | evaluate autocluster('~', sample_Count)` | 
| NumSeeds        | *int* [Standardwert: 25]              | Die Anzahl von Startwerten bestimmt die Anzahl von anfänglichen lokalen Suchpunkten des Algorithmus. In einigen Fällen, abhängig von der Struktur der Daten und wenn Sie die Anzahl der Kerne erhöhen, wird die Anzahl (oder Qualität) der Ergebnisse durch den erweiterten Suchraum mit einem langsameren Abfrage Kompromiss vergrößert. Der Wert weist in beide Richtungen abnehmende Ergebnisse auf. Wenn Sie also den Wert auf weniger als 5 verringern, werden dadurch die Leistungsverbesserungen beeinträchtigt. Wenn Sie auf mehr als 50 erhöhen, werden nur selten zusätzliche Muster generiert.                                         | `T | evaluate autocluster('~', '~', 15)`       |
| Customwildcard  | *"any_value_per_type"*           | Legt den Platzhalter Wert für einen bestimmten Typ in der Ergebnistabelle fest. Er gibt an, dass das aktuelle Muster keine Einschränkung für diese Spalte hat. Der Standardwert ist NULL, da der Standardwert der Zeichenfolge eine leere Zeichenfolge ist. Wenn der Standardwert ein guter Wert in den Daten ist, sollte ein anderer Platzhalter Wert verwendet werden (z `*` . b.).                                                                                                                | `T | evaluate autocluster('~', '~', '~', '*', int(-1), double(-1), long(0), datetime(1900-1-1))` |

## <a name="examples"></a>Beispiele

### <a name="using-autocluster"></a>Verwenden von Autocluster

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State , EventType , Damage
| evaluate autocluster(0.6)
```

|SegmentId|Anzahl|Percent|State|EventType|Damage|
|---|---|---|---|---|---|---|---|---|
|0|2278|38,7||Hagel|Nein
|1|512|8,7||Sturm|YES
|2|898|15,3|TEXAS||

### <a name="using-custom-wildcards"></a>Verwenden von benutzerdefinierten Platzhaltern

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State , EventType , Damage 
| evaluate autocluster(0.2, '~', '~', '*')
```

|SegmentId|Anzahl|Percent|State|EventType|Damage|
|---|---|---|---|---|---|---|---|---|
|0|2278|38,7|\*|Hagel|Nein
|1|512|8,7|\*|Sturm|YES
|2|898|15,3|TEXAS|\*|\*

## <a name="see-also"></a>Siehe auch

* [basket](./basketplugin.md)
* [Ver](./reduceoperator.md)
