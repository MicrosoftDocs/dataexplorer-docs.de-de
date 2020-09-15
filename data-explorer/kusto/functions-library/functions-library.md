---
title: 'Functions-Bibliothek: Azure Daten-Explorer'
description: In diesem Artikel werden benutzerdefinierte Funktionen beschrieben, mit denen die Funktionen von Azure Daten-Explorer erweitert werden.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 5b3457d52be37d4c0090db2f34c89994bc829a53
ms.sourcegitcommit: 50c799c60a3937b4c9e81a86a794bdb189df02a3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90067537"
---
# <a name="functions-library"></a>Functions-Bibliothek

Der folgende Artikel enthält eine kategorisierte Liste von benutzerdefinierten Funktionen.

## <a name="machine-learning-functions"></a>Machine Learning-Funktionen

|Funktionsname     |BESCHREIBUNG                                          |
|-------------------------|--------------------------------------------------------|
|[predict_fl ()](predict-fl.md)|Vorhersagen mithilfe eines vorhandenen trainierten Machine Learning-Modells. |
|[predict_onnx_fl ()](predict-onnx-fl.md)| Prognostizieren Sie die Verwendung eines vorhandenen trainierten Machine Learning-Modells im onnx-Format. |

## <a name="series-processing-functions"></a>Reihen Verarbeitungsfunktionen

|Funktionsname     |BESCHREIBUNG                                          |
|-------------------------|--------------------------------------------------------|
|[quantize_fl ()](quantize-fl.md)|Quantisieren Sie metrikspalten. |
|[series_fit_poly_fl ()](series-fit-poly-fl.md)|Anpassen eines Polynoms an Reihen mithilfe der Regressionsanalyse. |
|[series_moving_avg_fl ()](series-moving-avg-fl.md)|Wendet einen Filter für den gleitenden Durchschnitt für eine Reihe an. |
|[series_rolling_fl ()](series-rolling-fl.md)|Wendet eine parallele Aggregations Funktion für eine Reihe an. |
