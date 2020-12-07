---
title: 'Functions-Bibliothek: Azure Daten-Explorer'
description: In diesem Artikel werden benutzerdefinierte Funktionen beschrieben, mit denen die Funktionen von Azure Daten-Explorer erweitert werden.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: fb08edf4105c44a6be96cf6b2f314cf25887e69a
ms.sourcegitcommit: 4d5628b52b84f7564ea893f621bdf1a45113c137
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96443999"
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
