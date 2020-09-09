---
title: 'Functions-Bibliothek: Azure Daten-Explorer'
description: In diesem Artikel werden benutzerdefinierte Funktionen beschrieben, mit denen die Funktionen von Azure Daten-Explorer erweitert werden.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 66f8da1655fada7429fcd3087810904bd184546c
ms.sourcegitcommit: 993bc7b69096ab5516d3c650b9df97a1f419457b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89614505"
---
# <a name="functions-library"></a>Functions-Bibliothek

Der folgende Artikel enthält eine kategorisierte Liste von benutzerdefinierten Funktionen.

## <a name="machine-learning-functions"></a>Machine Learning-Funktionen

|Funktionsname     |BESCHREIBUNG                                          |
|-------------------------|--------------------------------------------------------|
|[predict_lf ()](predict-lf.md)|Vorhersagen mithilfe eines vorhandenen trainierten Machine Learning-Modells. |
|[predict_onnx_lf ()](predict-onnx-lf.md)| Prognostizieren Sie die Verwendung eines vorhandenen trainierten Machine Learning-Modells im onnx-Format. |

## <a name="series-processing-functions"></a>Reihen Verarbeitungsfunktionen

|Funktionsname     |BESCHREIBUNG                                          |
|-------------------------|--------------------------------------------------------|
|[quantize_lf ()](quantize-lf.md)|Quantisieren Sie metrikspalten. |
|[series_fit_poly_lf ()](series-fit-poly-lf.md)|Anpassen eines Polynoms an Reihen mithilfe der Regressionsanalyse. |
|[series_moving_avg_lf ()](series-moving-avg-lf.md)|Wendet einen Filter für den gleitenden Durchschnitt für eine Reihe an. |
|[series_rolling_lf ()](series-rolling-lf.md)|Wendet eine parallele Aggregations Funktion für eine Reihe an. |
