---
title: Analysieren Sie Zeitreihendaten mit Azure Data Explorer
description: Erfahren Sie, wie Sie Zeitreihendaten in der Cloud mit Azure Data Explorer analysieren können.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: how-to
ms.date: 04/07/2019
ms.openlocfilehash: 8c1ad74af063fe0380fec24b2717d4fdafe319df
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875309"
---
# <a name="time-series-analysis-in-azure-data-explorer"></a>Zeitreihenanalysen in Azure Data Explorer

Azure Data Explorer erfasst fortlaufend Telemetriedaten aus Clouddiensten oder von IoT-Geräten. Die Daten können für verschiedene Erkenntnisse analysiert werden, z.B. zur Überwachung der Integrität von Diensten, zu physischen Produktionsprozessen und zu Nutzungstrends. Die Analyse erfolgt in Zeitreihen ausgewählter Metriken, um Musterabweichungen im Vergleich mit dem typischen Baselinemuster zu ermitteln.
Azure Data Explorer bietet native Unterstützung für die Erstellung, Bearbeitung und Analyse von mehreren Zeitreihen. In diesem Thema erfahren Sie, wie Azure Data Explorer verwendet wird, um **innerhalb weniger Sekunden Tausende von Zeitreihen** zu erstellen und zu analysieren, um die Lösungen und Workflows für die Überwachung nahezu in Echtzeit zu ermöglichen.

## <a name="time-series-creation"></a>Erstellung von Zeitreihen

In diesem Abschnitt erstellen wir einen umfangreichen Satz regulärer Zeitreihen. Dabei verwenden wir ganz einfach und intuitiv den Operator `make-series` und geben fehlende Werte nach Bedarf ein.
Der erste Schritt bei der Einrichtung von Zeitreihenanalysen besteht darin, die ursprüngliche Telemetriedatentabelle zu partitionieren und in einen Satz Zeitreihen umzuwandeln. Die Tabelle enthält in der Regel eine Zeitstempelspalte, kontextbezogene Dimensionen und optionale Metriken. Die Dimensionen werden zum Partitionieren der Daten verwendet. Ziel ist es, pro Partition Tausende von Zeitreihen in regelmäßigen Zeitintervallen zu erstellen.

Die Eingabetabelle *demo_make_series1* enthält 600.000 Datensätze aus beliebigem Webdienstdatenverkehr. Verwenden Sie den folgenden Befehl, um zehn Datensätze als Stichprobe zu erfassen:

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03MTo0vTi3KTC02VKhRKAFyFQwNADOyzKUbAAAA) **\]**

```kusto
demo_make_series1 | take 10 
```

Die resultierende Tabelle enthält eine Zeitstempelspalte, drei kontextbezogene Dimensionsspalten und keine Metriken:

| TimeStamp | BrowserVer | OsVer | Land/Region |
| --- | --- | --- | --- |
| 2016-08-25 09:12:35.4020000 | Chrome 51.0 | Windows 7 | United Kingdom |
| 2016-08-25 09:12:41.1120000 | Chrome 52.0 | Windows 10 |   |
| 2016-08-25 09:12:46.2300000 | Chrome 52.0 | Windows 7 | United Kingdom |
| 2016-08-25 09:12:46.5100000 | Chrome 52.0 | Windows 10 | United Kingdom |
| 2016-08-25 09:12:46.5570000 | Chrome 52.0 | Windows 10 | Republik Litauen |
| 2016-08-25 09:12:47.0470000 | Chrome 52.0 | Windows 8.1 | Indien |
| 2016-08-25 09:12:51.3600000 | Chrome 52.0 | Windows 10 | United Kingdom |
| 2016-08-25 09:12:51.6930000 | Chrome 52.0 | Windows 7 | Niederlande |
| 2016-08-25 09:12:56.4240000 | Chrome 52.0 | Windows 10 | United Kingdom |
| 2016-08-25 09:13:08.7230000 | Chrome 52.0 | Windows 10 | Indien |

Da keine Metriken vorhanden sind, können wir nur einen Satz Zeitreihen erstellen, die die Anzahl von Datenverkehrsbewegungen selbst darstellen, partitioniert nach Betriebssystem. Dazu erstellen wir folgende Abfrage:

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPwQrCMBAE0Hu/Yo4NVLBn6Td4ULyWtV1tMJtIsoEq/XhbC4J48jgw+5h1rBDrW0UDDakjR7HsWUIrdOM2cbScakxIWYSiffJSL49W+KAkd2N2hVsMGv8yaPw2furFhCVu1gifpelC9loa9Hyh7LTZInh8FFiPSP7K5fufap1UoR4Mzg/s04njjEb2PUfofNYNFPUFtJiguAEBAAA=) **\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| render timechart 
```

- Verwenden Sie den Operator [`make-series`](kusto/query/make-seriesoperator.md), um einen Satz aus drei Zeitreihen zu erstellen, wobei Folgendes gilt:
    - `num=count()`: Zeitreihe für den Datenverkehr.
    - `range(min_t, max_t, 1h)`: Die Zeitreihe wird im Zeitbereich in Abschnitten von jeweils einer Stunde erstellt (ältester und neuester Zeitstempel der Tabellendatensätze).
    - `default=0`: Geben Sie eine Füllmethode für fehlende Zeitabschnitte an, um eine reguläre Zeitreihe zu erstellen. Verwenden Sie alternativ dazu [`series_fill_const()`](kusto/query/series-fill-constfunction.md), [`series_fill_forward()`](kusto/query/series-fill-forwardfunction.md), [`series_fill_backward()`](kusto/query/series-fill-backwardfunction.md) und [`series_fill_linear()`](kusto/query/series-fill-linearfunction.md), um Änderungen zu berücksichtigen.
    - `byOsVer`: Partitioniert nach Betriebssystem.
- Die tatsächliche Datenstruktur der Zeitreihen ist ein numerisches Array des aggregierten Werts pro Zeitabschnitt. Wir verwenden `render timechart` für die Visualisierung.

In der oben stehenden Tabelle haben wir drei Partitionen. Wir können separate Zeitreihen für jede Betriebssystemversion erstellen – Windows 10 (rot), 7 (blau) und 8.1 (grün) –, wie im Diagramm zu sehen:

![Zeitreihenpartitionen](media/time-series-analysis/time-series-partition.png)

## <a name="time-series-analysis-functions"></a>Analysefunktionen für Zeitreihen

In diesem Abschnitt führen wir typische Funktionen zum Verarbeiten von Zeitreihen aus.
Sobald ein Satz Zeitreihen erstellt wurde, unterstützt Azure Data Explorer eine wachsende Liste von Funktionen zur Verarbeitung und Analyse, die Sie in der [Dokumentation zu Zeitreihen](kusto/query/machine-learning-and-tsa.md) finden. Im Folgenden werden einige repräsentative Funktionen zum Verarbeiten und Analysieren von Zeitreihen beschrieben.

### <a name="filtering"></a>Filterung

Die Filterung ist ein gängiges Verfahren zur Signalverarbeitung und eignet sich gut für Aufgaben zur Verarbeitung von Zeitreihen (z.B. zum Glätten eines Signals mit Rauschen oder zum Erkennen von Änderungen).
- Es gibt zwei allgemeine Filterfunktionen:
    - [`series_fir()`](kusto/query/series-firfunction.md): Anwenden eines FIR-Filters. Wird zur einfachen Berechnung des gleitenden Durchschnitts und zur Differenzierung der Zeitreihe zum Erkennen von Änderungen verwendet.
    - [`series_iir()`](kusto/query/series-iirfunction.md): Anwenden eines IIR-Filters. Wird zur exponentiellen Glättung und für kumulative Summen verwendet.
- `Extend`: Erweitern Sie die Zeitreihe, indem Sie der Abfrage eine neue Reihe für den gleitenden Durchschnitt von Abschnitten der Größe 5 (namens *ma_num*) hinzufügen:

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPQavCMBCE7/6KOSYQ4fXgSfobPDx517C2q4bXpLLZQBV/vKkFQTx5WRh25tvZgRUxJK9ooWPuaCAxPcfRR/pnn1kC5wZ35BIjSbjxbDf7EPlXKV6s3a6GmUHTVwya3hkf9tUds1wvEqnEthtLUmPR85HKoO0PxoQXBSFBKJ3YPP9xSyWH5mxxuGKX/1gqlCfl1Neln5EL3R+DmCodhC9MahqHjXVQKbxMW5NScyzQerA7k+gDa1tswzsBAAA=) **\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| render timechart
```

![Filtern von Zeitreihen](media/time-series-analysis/time-series-filtering.png)

### <a name="regression-analysis"></a>Regressionsanalyse

Azure Data Explorer unterstützt die segmentierte lineare Regressionsanalyse, um den Trend einer Zeitreihe zu einzuschätzen.
- Verwenden Sie [series_fit_line()](kusto/query/series-fit-linefunction.md), um zum Erkennen eines allgemeinen Trends die beste angepasste Linie für eine Zeitreihe zu ermitteln.
- Verwenden Sie [series_fit_2lines()](kusto/query/series-fit-2linesfunction.md), um Trendänderungen relativ zur Baseline zu erkennen – diese sind in Überwachungsszenarien nützlich.

Beispiel für die Funktionen `series_fit_line()` und `series_fit_2lines()` in einer Zeitreihenabfrage:

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuKqUUitKEnNS1GACMSnZZbEG+Vk5qUWa1Rq6iCLggSBYkAdRUD1qUUKIIHkjMSiEoXyzJIMjYrk/JzS3DzbCk0AUIIJ02EAAAA=) **\]**

```kusto
demo_series2
| extend series_fit_2lines(y), series_fit_line(y)
| render linechart with(xcolumn=x)
```

![Zeitreihenregression](media/time-series-analysis/time-series-regression.png)

- Blau: ursprüngliche Zeitreihe
- Grün: angepasste Linie
- Rot: zwei angepasste Linien

> [!NOTE]
> Die Funktion hat den Punkt des Sprungs (der Änderung der Stufe) erkannt.

### <a name="seasonality-detection"></a>Erkennung der Saisonalität

Viele Metriken folgen saisonalen (periodischen) Mustern. Benutzerdatenverkehr in Clouddiensten weist in der Regel tägliche und wöchentliche Muster auf, wobei die höchsten Werte etwa um die Mitte der Werktage und die niedrigsten während der Nacht und am Wochenende zu finden sind. IoT-Sensoren messen in regelmäßigen Abständen. Physikalische Messwerte wie Temperatur, Luftdruck oder Luftfeuchtigkeit können ebenfalls ein saisonales Verhalten zeigen.

Das folgende Beispiel wendet die Erkennung von Saisonalität auf einen Monat Datenverkehr eines Webdiensts an (Zeitabschnitte von 2 Stunden):

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuaqUShKzUtJLVIoycxNTc5ILCoBAHrjE80fAAAA) **\]**

```kusto
demo_series3
| render timechart 
```

![Saisonalität von Zeitreihen](media/time-series-analysis/time-series-seasonality.png)

- Verwenden Sie [series_periods_detect()](kusto/query/series-periods-detectfunction.md), um die Zeiträume in der Zeitreihe automatisch zu erkennen. 
- Verwenden Sie [series_periods_validate()](kusto/query/series-periods-validatefunction.md), wenn Sie wissen, dass eine Metrik bestimmte Zeiträume aufweisen sollte und Sie überprüfen möchten, ob diese tatsächlich vorhanden sind.

> [!NOTE]
> Wenn bestimmte Zeiträume nicht vorhanden sind, handelt es sich um eine Anomalie.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12OwQ6CMBBE737FHKmpVtAr39IguwkYyzZ0IZj48TZSLx533szOEAfxieeR0/XwRpzlwb2iilkSShapl5mTQYvd5QvxxJqd1bQEi8vZor6RawaLxsA5FewcOjBKBOP0PXUMXL7lyrCeeIvdRPjrzIw35Qyoe6W2GY4qJMv9yb91xtX0AS7N323BAAAA) **\]**

```kusto
demo_series3
| project (periods, scores) = series_periods_detect(num, 0., 14d/2h, 2) //to detect the periods in the time series
| mv-expand periods, scores
| extend days=2h*todouble(periods)/1d
```

| Zeiträume | Treffer | days |
| --- | --- | --- |
| 84 | 0.820622786055595 | 7 |
| 12 | 0.764601405803502 | 1 |

Die Funktion erkennt tägliche und wöchentliche Saisonalität. Der Trefferwert bei der täglichen Messung ist geringer, weil sich Wochenendtage von Wochentagen unterscheiden.

### <a name="element-wise-functions"></a>Elementbezogene Funktionen

In einer Zeitreihe können arithmetische und logische Operationen durchgeführt werden. Mit [series_subtract()](kusto/query/series-subtractfunction.md) können wir eine residuale Zeitreihe berechnen, also den Unterschied zwischen der ursprünglichen Rohdatenmetrik und einer geglätteten Metrik, und nach Anomalien im verbleibenden Signal suchen:

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WQQU/DMAyF7/sVT5waqWjrgRPqb+AAgmPltR6LSNLJcdhA+/G4izRAnLhEerbfl2cHVkSfBkUPnfNIgaSZOM5DpDceMovn3OGMXGIk8Z+8jDdPPvKjUjw4d78KC4NO/2LQ6Tfjz/jqjEXeVolUYj/OJWnjMPGOStB+gznhSoFPEEqv3Fz2aWukFt3eYfuBh/zMYlA+KafJmsOCrPRh56Ux2UL4wKRN1+LOtVApXF/37RTOfioUfvpz2arQqBVS2Q7rtc6wa4wlkPLVCLXIqE7DHvcsXOOh73Hz4tM0HzO6zQ1gDOx8UOvZrtayst0Y7z4babkkYQxMyQbGPYnCiGIxTS/fXGpfwk+n7uQBAAA=) **\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| extend residual_num=series_subtract(num, ma_num) //to calculate residual time series
| where OsVer == "Windows 10"   // filter on Win 10 to visualize a cleaner chart 
| render timechart
```

![Zeitreihenoperationen](media/time-series-analysis/time-series-operations.png)

- Blau: ursprüngliche Zeitreihe
- Rot: geglättete Zeitreihe
- Grün: residuale Zeitreihe

## <a name="time-series-workflow-at-scale"></a>Zeitreihenworkflow für eine große Anzahl von Vorgängen

Das folgende Beispiel zeigt, wie diese Funktionen innerhalb von Sekunden für mehrere Tausend Zeitreihen ausgeführt werden können, um Anomalien zu erkennen. Um einige beispielhafte Telemetriedatensätze aus der Metrik für die Anzahl von Lesevorgängen eines Datenbankdiensts im Lauf von vier Tagen anzuzeigen, führen Sie die folgende Abfrage aus:

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKEnMTlUwAQArfAiiGgAAAA==) **\]**

```kusto
demo_many_series1
| take 4 
```

| timestamp | Loc | anonOp | DB | DataRead |
| --- | --- | --- | --- | --- |
| 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 262 | 0 |
| 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 241 | 0 |
| 2016-09-11 21:00:00.0000000 | Loc 9 | -865998331941149874 | 262 | 279862 |
| 2016-09-11 21:00:00.0000000 | Loc 9 | 371921734563783410 | 255 | 0 |

Und eine einfache Statistik:

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVcgrzbVNzi/NK9HQ1FHIzcyLL7EFkhohnr6uwSGOvgEg0cQKkGhiBZIoAEq2dK9VAAAA) **\]**

```kusto
demo_many_series1
| summarize num=count(), min_t=min(TIMESTAMP), max_t=max(TIMESTAMP) 
```

| num | min\_t | max\_t |
| --- | --- | --- |
| 2177472 | 2016-09-08 00:00:00.0000000 | 2016-09-11 23:00:00.0000000 |

Eine Zeitreihe in 1-Stunden-Abschnitten der Metrik für die Anzahl von Lesevorgängen (4 Tage × 24 Stunden = 96 Punkte) führt zu einer normalen Musterfluktuation:

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPMQvCMBSE9/6KGxOoYGfpIOjgUBDtXh7twwabFF6ittIfb2rBQSfHg+8+7joOsMZVATlC72vqSFTDtq8subHyLIZ9hgn+Zi2JefKMq/JQ7M/ltjhqvQGSbrbQ8JeFhm/LTyGZInbl1RIhTI3P6X5ROwp0ikmjd/hYYByE3IXV+1G6TEqRtTqahF3DgmAs1y1JwMOEVo0Rzdf6BbBH5FAHAQAA) **\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h)
| render timechart with(ymin=0) 
```

![Zeitreihe für eine große Anzahl von Vorgängen](media/time-series-analysis/time-series-at-scale.png)

Das oben beschriebene Verhalten ist irreführend, da die einzelne normale Zeitreihe aus Tausenden von verschiedenen Instanzen aggregiert wurde, die anomale Muster aufweisen können. Daher erstellen wir eine Zeitreihe pro Instanz. Eine Instanz wird durch „Loc“ (Speicherort), „anonOp“ (Operation) und „DB“ (bestimmter Computer) definiert.

Wie viele Zeitreihen können wir erstellen?

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVUiqVPDJT9ZR8C/QUXBxAkol55fmlQAAWEsFxjQAAAA=) **\]**

```kusto
demo_many_series1
| summarize by Loc, Op, DB
| count
```

| Anzahl |
| --- |
| 18339 |

Jetzt erstellen wir einen Satz aus 18.339 Zeitreihen der Metrik für die Anzahl von Lesevorgängen. Wir fügen die `by`-Klausel zur make-series-Anweisung hinzu, wenden lineare Regression an und wählen die beiden Zeitreihen aus, bei denen der signifikanteste Abwärtstrend zu beobachten war:

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPsU7DQBBE+3zFdLmTTGHSgFAKUCiQiIKIe2u5rJ0T9l3YWwcH5eO5JBIFVJSzmnmz07Gi96FWzKExOepIzIb7WPcUDnVi8ZxKHJGGvifxX3yym+pp+biu7pcv1t4Bk+5EofFfFBp/U/4EJsdse+eri4QwbdKc9q1ZkNJrVhYx4IcCHyAUWjbnRcXlpQLl1uLtgOfoCqx2BRYPGcyjctjASPoYSLhA6uKObR5waasbr3XnA5tzrc0RjTtcn0hnKyg55KtkDAvU9+y2JIpPr1ujXjueT9cse+8YlVDTeIfVoNQymiiZ5ENSCi4vM3FQxAblzWx2a6f2G2UcBRyWAQAA) **\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc 
| render timechart with(title='Service Traffic Outage for 2 instances (out of 18339)')
```

![Die Top 2 der Zeitreihen](media/time-series-analysis/time-series-top-2.png)

Zeigen Sie die Instanzen an:

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPvW4CMRCEe55iSlsyBWkjChApIoESAb21udsQg38O26AD8fDx3SEUJVXKWc18s2M5wxmvM6bIIVVkKYqaXdCO/EUnjobTBDekk3MUzZU7u9i+rl4229nqXcpnYGQ7CrX/olD7m/InMLoV24HHg0RkqtOUzjuxoEzroiSCx4MC4xHJ71j0i9TwksLkS+LjgmWoFN4ahcW8gLnN7GuImI4niqyQbGhYlgFDm/40WVvjWfS1skRyaPDUkXorKFXl2MSw5yr/pN9Z31SyxuhbAQAA) **\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc
| project Loc, Op, DB, slope 
```

| Loc | Op | DB | slope |
| --- | --- | --- | --- |
| Loc 15 | 37 | 1151 | -102743.910227889 |
| Loc 13 | 37 | 1249 | -86303.2334644601 |

In weniger als zwei Minuten hat Azure Data Explorer fast 20.000 Zeitreihen analysiert und zwei anomale Zeitreihen erkannt, in denen die Anzahl von Lesevorgängen plötzlich sehr stark gesunken ist.

Diese erweiterte Funktionalität und die hohe Verarbeitungsgeschwindigkeit von Azure Data Explorer sorgen für eine einzigartige, leistungsstarke Lösung für die Analyse von Zeitreihen.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu [Anomalieerkennung in Zeitreihen und Vorhersage](anomaly-detection.md) in Azure Data Explorer.
* Informationen zu [Machine Learning-Funktionen](machine-learning-clustering.md) in Azure Data Explorer.