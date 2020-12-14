---
title: 'Functions-Bibliothek: Azure Daten-Explorer'
description: In diesem Artikel werden benutzerdefinierte Funktionen beschrieben, mit denen die Funktionen von Azure Daten-Explorer erweitert werden.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: b7e066133817184664e37aec52a562525afa9504
ms.sourcegitcommit: fcaf3056db2481f0e3f4c2324c4ac956a4afef38
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97388952"
---
# <a name="functions-library"></a>Functions-Bibliothek

Der folgende Artikel enthält eine kategorisierte Liste von [UDF (benutzerdefinierte Funktionen)](../query/functions/user-defined-functions.md).

Der Code der benutzerdefinierten Funktionen wird in den Artikeln angegeben.  Sie kann in einer Let-Anweisung verwendet werden, die in eine Abfrage eingebettet ist, oder Sie kann in einer Datenbank mithilfe von persistent gespeichert werden [`.create function`](../management/create-function.md) .

## <a name="machine-learning-functions"></a>Machine Learning-Funktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[kmeans_fl()](kmeans-fl.md)|Clusterize mithilfe des k-means-Algorithmus. |
|[predict_fl()](predict-fl.md)|Vorhersagen mithilfe eines vorhandenen trainierten Machine Learning-Modells. |
|[predict_onnx_fl()](predict-onnx-fl.md)| Prognostizieren Sie die Verwendung eines vorhandenen trainierten Machine Learning-Modells im onnx-Format. |

## <a name="promql-functions"></a>Promql-Funktionen

Der folgende Abschnitt enthält allgemeine [promql](https://prometheus.io/docs/prometheus/latest/querying/basics/) -Funktionen. Diese Funktionen können für die Analyse von Metriken verwendet werden, die vom [Prometheus](https://prometheus.io/) -Überwachungssystem in Azure Daten-Explorer erfasst wurden. Alle Funktionen gehen davon aus, dass die Metriken in Azure Daten-Explorer mit dem [Prometheus-Datenmodell](https://prometheus.io/docs/concepts/data_model/)strukturiert werden.


|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[series_metric_fl ()](series-metric-fl.md)|Wählt mit dem Prometheus-Datenmodell gespeicherte Zeitreihen aus und ruft diese ab. |
|[series_rate_fl ()](series-rate-fl.md)|Berechnet die durchschnittliche Rate der Erhöhung der gegen Metrik pro Sekunde. |

## <a name="series-processing-functions"></a>Reihen Verarbeitungsfunktionen

|Funktionsname     |Beschreibung                                          |
|-------------------------|--------------------------------------------------------|
|[quantize_fl()](quantize-fl.md)|Quantisieren Sie metrikspalten. |
|[series_dot_product_fl()](series-dot-product-fl.md)|Berechnet das Punktprodukt von zwei numerischen Vektoren. |
|[series_downsample_fl()](series-downsample-fl.md)|Downsampling einer Zeitreihe mit einem ganzzahligen Faktor. |
|[series_exp_smoothing_fl()](series-exp-smoothing-fl.md)|Wendet einen grundlegenden exponentiellen Glättungs Filter für eine Reihe an. |
|[series_fit_lowess_fl()](series-fit-lowess-fl.md)|Passt ein lokales Polynoms mit der lowess-Methode in Reihen an. |
|[series_fit_poly_fl()](series-fit-poly-fl.md)|Passt ein polynomal mithilfe der Regressionsanalyse in Reihen an. |
|[series_moving_avg_fl()](series-moving-avg-fl.md)|Wendet einen Filter für den gleitenden Durchschnitt für eine Reihe an. |
|[series_rolling_fl()](series-rolling-fl.md)|Wendet eine parallele Aggregations Funktion für eine Reihe an. |
