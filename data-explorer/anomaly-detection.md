---
title: Anomalieerkennung in Zeitreihen und Vorhersagen in Azure Data Explorer
description: Informationen zum Analysieren von Zeitreihendaten für Anomalieerkennung und Vorhersage mit Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: eac1237e098b90f526e441ef89f0902f72abde53
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81492754"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Anomalieerkennung und Vorhersage in Azure Data Explorer

Azure Data Explorer erfasst fortlaufend Telemetriedaten aus Clouddiensten oder von IoT-Geräten. Die Daten werden für verschiedene Erkenntnisse analysiert, z.B. zur Überwachung der Integrität von Diensten, zu physischen Produktionsprozessen, Nutzungstrends und Auslastungsvorhersage. Die Analyse erfolgt in Zeitreihen ausgewählter Metriken, um Musterabweichungen der Metriken von ihrem typischen Baselinemuster zu ermitteln. Azure Data Explorer bietet native Unterstützung für die Erstellung, Bearbeitung und Analyse mehrerer Zeitreihen. Tausende von Zeitreihen können innerhalb weniger Sekunden erstellt und analysiert werden, um die Überwachung von Lösungen und Workflows nahezu in Echtzeit zu ermöglichen.

In diesem Artikel werden die Funktionen in Azure Data Explorer zu Anomalieerkennung in Zeitreihen und Vorhersage beschrieben. Die anwendbaren Zeitreihenfunktionen basieren auf einem robusten, gut bekannten Analysemodell, in dem jede ursprüngliche Zeitreihe in saisonale, Trend- und Restkomponenten aufgeschlüsselt wird. Anomalien werden anhand von Ausreißern bei Restkomponenten erkannt, während die Prognose durch Extrapolieren von saisonalen und Trendkomponenten erfolgt. Die Implementierung von Azure Data Explorer erweitert das grundlegende Analysemodell erheblich durch automatische Saisonabhängigkeitserkennung, stabile Ausreißeranalyse und vektorisierte Implementierung zur Verarbeitung Tausender von Zeitreihen in Sekunden.

## <a name="prerequisites"></a>Voraussetzungen

In [Zeitreihenanalysen in Azure Data Explorer](/azure/data-explorer/time-series-analysis) finden Sie eine Übersicht über Zeitreihenfunktionen.

## <a name="time-series-decomposition-model"></a>Zeitreihenanalyse-Modell

Die native Implementierung für die Zeitreihenvorhersage und Erkennung von Anomalien in Azure Data Explorer verwendet ein gut bekanntes Analysemodell. Dieses Modell wird auf Zeitreihen von Metriken angewendet, von denen erwartet wird, dass sie regelmäßiges und Trendverhalten wie Dienstdatenverkehr, Komponententakt und periodische IoT-Messungen zum Vorhersagen zukünftiger Metrikwerte und Erkennen anomaler Werte manifestieren. Dieser Regressionsprozess geht von der Annahme aus, dass die Zeitreihen anders als das zuvor bekannte saisonale und Trendverhalten nach dem Zufallsprinzip verteilt sind. Sie können dann zukünftige Metrikwerte auf Basis der saisonalen und Trendkomponenten, zusammen als Baseline bezeichnet, prognostizieren und den restlichen Teil ignorieren. Sie können anomale Werte auch basierend auf der Ausreißeranalyse erkennen, wobei nur der restliche Teil verwendet wird.
Verwenden Sie zum Erstellen eines Analysemodells die Funktion [`series_decompose()`](kusto/query/series-decomposefunction.md). Die `series_decompose()`-Funktion nimmt einen Satz von Zeitreihen und schlüsselt sie automatisch jedes Mal in saisonale, Trend-, Rest- und Baselinekomponenten auf. 

Beispielsweise können Sie Datenverkehr eines internen Webdiensts mit der folgenden Abfrage aufschlüsseln:

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WQ3WrDMAyF7/sUukvCnDXJGIOVPEULuwxqoixm/gm2+jf28JObFjbYrmyho3M+yRCD1a5jaGFAJtaW8qaqX8qqLqvnYrMySYHnvxRNWT1B07xW1U03JFEzbVYDWd9Z/KAuUtAUm9UXpLJcSnAH2+LxPZe3AO9gJ6ZbRjvDGLy9EbG/BUemOXnvLxD1AOJ1mijQtWhbyHbbOgOA9RogkqGeAaXn3g1BooVb6OiDNHpD6CjAUccDGv2JrL0TSzozuQHyPYqHdqRkDKN3aBRwkJaCQJIoQ4VsuXh2A/Xezj5SWkVBWSvI0vSoOSsWpLtEpyDwY4KTW8nnJ5ws+2+eAhSyOxjkd+HDVVcIfHplp2TYTxgYTpqnnDUbarM32gPO86PY4jjqfmGw3vGkftNlCi5xNprbWW5kYvENQQnqDh8CAAA=) **\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (baseline, seasonal, trend, residual) = series_decompose(num, -1, 'linefit')  //  decomposition of a set of time series to seasonal, trend, residual, and baseline (seasonal+trend)
| render timechart with(title='Web app. traffic of a month, decomposition', ysplit=panels)
```

![Zeitreihenanalyse](media/anomaly-detection/series-decompose-timechart.png)

* Die ursprüngliche Zeitreihe wird mit **num** (in Rot) bezeichnet. 
* Der Prozess beginnt mit der automatischen Erkennung der Saisonabhängigkeit mithilfe der Funktion [`series_periods_detect()`](kusto/query/series-periods-detectfunction.md) und extrahiert das **saisonale** Muster (in Violett).
* Das saisonale Muster wird der ursprünglichen Zeitreihe entnommen und eine lineare Regression mit der Funktion [`series_fit_line()`](kusto/query/series-fit-linefunction.md) ausgeführt, um die **Trendkomponente** (in Hellblau dargestellt) zu finden.
* Die Funktion entnimmt den Trend, und das Verbleibende ist die **Restkomponente** (in Grün).
* Abschließend fügt die Funktion die saisonalen und Trendkomponenten hinzu, um die **Baseline** (in Blau) zu generieren.

## <a name="time-series-anomaly-detection"></a>Anomalieerkennung in Zeitreihen

Die Funktion [`series_decompose_anomalies()`](kusto/query/series-decompose-anomaliesfunction.md) sucht anomale Punkte in einem Satz von Zeitreihen. Diese Funktion ruft `series_decompose()` zum Erstellen des Analysemodells auf und führt dann [`series_outliers()`](kusto/query/series-outliersfunction.md) für die Restkomponente aus. `series_outliers()` berechnet die Anomaliebewertungen für jeden Punkt der Restkomponente mithilfe des Zauntests von Tukey. Anomaliebewertungen über 1,5 oder unter -1,5 zeigen einen leichten Anomalienanstieg bzw. -abfall an. Anomaliebewertungen über 3,0 oder unter -3,0 zeigen eine starke Anomalie an. 

Mit der folgenden Abfrage können Sie Anomalien im internen Webdienstdatenverkehr erkennen:

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WR3W7CMAyF73mKI25KpRbaTmjSUJ8CpF1WoXVptPxUifmb9vBLoGO7GFeR7ePv2I4ihpamYdToBBNLTYuqKF/zosyLdbqZqagQl/8UVV68oKreimLSdVFUDZtZR9o2WnxQ48lJ8tXsCzHM7yHMUdfidFiEN4U12AXoloUe0Turp4nYTsaeaYzs/RVedgis80CObkFdI9ltywTAagV4UtQyRKiZgyLEaTGZ9taFQqtIGHI4SX8USn4KltYEJF2YTIeFMFaHPPkMvrWOMuxFoEpDaVjujmo6aq0erafmIY+7ZCiX6wx5mSGJHb3kJA1sF8jB8q69toNwjLPkYfGTseqoja//eLNkRXXyTnuIcVyCneh72cL2YQdtDQ8ZHvIkDcsfPWH+3AvPvObx0FMXD/RLhfDYW9VhtNKwj/8U69M1b2S//AbRUQMWQQIAAA==) **\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (anomalies, score, baseline) = series_decompose_anomalies(num, 1.5, -1, 'linefit')
| render anomalychart with(anomalycolumns=anomalies, title='Web app. traffic of a month, anomalies') //use "| render anomalychart with anomalycolumns=anomalies" to render the anomalies as bold points on the series charts.
```

![Anomalieerkennung in Zeitreihen](media/anomaly-detection/series-anomaly-detection.png)

* Die ursprünglichen Zeitreihen (in Rot). 
* Die Baselinekomponente (saisonal und Trend – in Blau).
* Die anomalen Punkte (in Violett) über der ursprünglichen Zeitreihe. Die anomalen Punkte weichen deutlich von den erwarteten Baselinewerten ab.

## <a name="time-series-forecasting"></a>Zeitreihenvorhersage

Die Funktion [`series_decompose_forecast()`](kusto/query/series-decompose-forecastfunction.md) prognostiziert die zukünftigen Werte eines Satzes Reihe von Zeitreihen. Diese Funktion ruft `series_decompose()` auf, um das Analysemodell zu erstellen, und extrapoliert dann für jede Zeitreihe die Baselinekomponente in die Zukunft.

Mit der folgenden Abfrage können Sie den Webdienstdatenverkehr der nächsten Woche vorhersagen:

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA22QzW6DMBCE73mKuQFqKISqitSIW98gkXpEDl5iK9hG9uanUR++dqE99YRGO8x845EYRtuO0UIKJtaG8qbebMt6U9avxW41Joe4/+doyvoFTfNW14tPJlOjZqGc1w9n263crSQZ1xlxpi6Q1xSa1ReSLGcJezGtuJ7y+C3gLA6xZM/CTBi8MwshuxnkaUlGYJpS5/ETQUvEzJsiTz+ibZEd9psMQFUBgUbqGSLe7GkkpBVYygfn46EfSVjyuOpwEaN+CNbOxki6M1mZTNSLkAbOv3WSemcmF6j7vSX8dcTUlvOFsZJcFDHFx4wYnmp7JTzjplnlrHmkNvugI8Q0PYO9GAbdww0RyDjLav1XHLnBimAjEG5E5zQ7vRP284x36hOOTtxZ8Q3The8P2QEAAA==) **\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week by Time Series Decomposition')
```

![Zeitreihenvorhersage](media/anomaly-detection/series-forecasting.png)

* Ursprüngliche Metrik (in Rot). Zukünftige Werte fehlen und werden standardmäßig auf 0 (null) gesetzt.
* Extrapolieren Sie die Baselinekomponente (in Blau), um die Werte der nächsten Woche vorherzusagen.

## <a name="scalability"></a>Skalierbarkeit

Die Syntax der Abfragesprache von Azure Data Explorer ermöglicht einen einzelnen Aufruf zur Verarbeitung mehrerer Zeitreihen. Ihre einzigartig optimierte Implementierung ermöglicht schnelle Verarbeitung, was für effektive Anomalieerkennung und Vorhersagen beim Überwachen Tausender von Leistungsindikatoren in nahezu in Echtzeit ablaufenden Szenarien von entscheidender Bedeutung ist.

Die folgende Abfrage zeigt die gleichzeitige Verarbeitung von drei Zeitreihen:

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA21Qy26DMBC85yvmFlChcUirSI34ikTqETl4KVawjfDmqX587UCaHuqLtePxPLYjhtG2YpRQkom1oaQQy3Uulrl4TzezLjLk5T9GkYsViuJDiImnIqlox6F1g745W67VZqbIuMrIA1WeBk2+mH0jjvk4wh5NKU9fSbhTOItdMNmyND2awZkpIbsxyMukDM/UR8/9FV6rIEkXJqvgmsYTl7X0lISHspzvtqt5hjdxPxkeYBHA4gGKFMBiAUilIAfWja617CY1NG4ASX/FSfuj7PRNsg4ZXANz7Fj3HSGuBmOjZ5hYbcSqIBwbZpNk+iQFcQpx4/omrqLamd55qh5v41d22nIybWChOI0qQ9Cg4e5ftyE6zprbhDV3VM4/aQ/Z96/gQTahU4wsYZzlNvs11vYL3BJsCIQz0eHed/W30jz9AUEBI0ktAgAA) **\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid
| extend offset=case(sid=='TS3', 4000000, sid=='TS2', 2000000, 0)   //  add artificial offset for easy visualization of multiple time series
| extend num=series_add(num, offset)
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week for 3 time series')
```

![Skalierbarkeit von Zeitreihen](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>Zusammenfassung

In diesem Dokument werden die nativen Funktionen von Azure Data Explorer zur Anomalieerkennung in Zeitreihen und Vorhersage beschrieben. Jede ursprüngliche Zeitreihe wird für die Erkennung von Anomalien und/oder Prognose in saisonale, Trend- und Restkomponenten aufgeschlüsselt. Diese Funktionen können für nahezu in Echtzeit ablaufende Überwachungsszenarien wie z.B. Fehlererkennung, vorbeugende Wartung sowie Bedarfs- und Auslastungsvorhersagen verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

Informationen zu [Machine Learning-Funktionen](/azure/data-explorer/machine-learning-clustering) in Azure Data Explorer.
