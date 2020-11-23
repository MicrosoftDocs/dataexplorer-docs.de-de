---
title: 'Functions-Bibliothek: Azure Daten-Explorer'
description: In diesem Artikel werden benutzerdefinierte Funktionen beschrieben, mit denen die Funktionen von Azure Daten-Explorer erweitert werden.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 552d55cdf3e40a4138b6521ffa2afdd85eeab68b
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324617"
---
# <a name="functions-library"></a>Functions-Bibliothek

Der folgende Artikel enthält eine kategorisierte Liste von [UDF (benutzerdefinierte Funktionen)](../query/functions/user-defined-functions.md).

Der Code der benutzerdefinierten Funktionen wird in den Artikeln angegeben.  Sie kann in einer Let-Anweisung verwendet werden, die in eine Abfrage eingebettet ist, oder Sie kann in einer Datenbank mit der [. Create-Funktion](../management/create-function.md)persistent gespeichert werden.

## <a name="machine-learning-functions"></a>Machine Learning-Funktionen

|Funktionsname     |BESCHREIBUNG                                          |
|-------------------------|--------------------------------------------------------|
|[kmeans_fl()](kmeans-fl.md)|Clusterize mithilfe des k-means-Algorithmus. |
|[predict_fl()](predict-fl.md)|Vorhersagen mithilfe eines vorhandenen trainierten Machine Learning-Modells. |
|[predict_onnx_fl()](predict-onnx-fl.md)| Prognostizieren Sie die Verwendung eines vorhandenen trainierten Machine Learning-Modells im onnx-Format. |

## <a name="series-processing-functions"></a>Reihen Verarbeitungsfunktionen

|Funktionsname     |BESCHREIBUNG                                          |
|-------------------------|--------------------------------------------------------|
|[quantize_fl()](quantize-fl.md)|Quantisieren Sie metrikspalten. |
|[series_dot_product_fl()](series-dot-product-fl.md)|Berechnet das Punktprodukt von zwei numerischen Vektoren. |
|[series_exp_smoothing_fl ()](series-exp-smoothing-fl.md)|Wendet einen grundlegenden exponentiellen Glättungs Filter für eine Reihe an. |
|[series_fit_poly_fl()](series-fit-poly-fl.md)|Passt ein polynomal mithilfe der Regressionsanalyse in Reihen an. |
|[series_moving_avg_fl()](series-moving-avg-fl.md)|Wendet einen Filter für den gleitenden Durchschnitt für eine Reihe an. |
|[series_rolling_fl()](series-rolling-fl.md)|Wendet eine parallele Aggregations Funktion für eine Reihe an. |
