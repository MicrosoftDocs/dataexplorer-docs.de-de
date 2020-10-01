---
title: 'Functions-Bibliothek: Azure Daten-Explorer'
description: In diesem Artikel werden benutzerdefinierte Funktionen beschrieben, mit denen die Funktionen von Azure Daten-Explorer erweitert werden.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 8cdccf261f755a0ea7a3d6a6299aa54ce021f366
ms.sourcegitcommit: 1618cbad18f92cf0cda85cb79a5cc1aa789a2db7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2020
ms.locfileid: "91614974"
---
# <a name="functions-library"></a>Functions-Bibliothek

Der folgende Artikel enthält eine kategorisierte Liste von [UDF (benutzerdefinierte Funktionen)](../query/functions/user-defined-functions.md).

Der Code der benutzerdefinierten Funktionen wird in den Artikeln angegeben.  Sie kann in einer Let-Anweisung verwendet werden, die in eine Abfrage eingebettet ist, oder Sie kann in einer Datenbank mit der [. Create-Funktion](../management/create-function.md)persistent gespeichert werden.

## <a name="machine-learning-functions"></a>Machine Learning-Funktionen

|Funktionsname     |BESCHREIBUNG                                          |
|-------------------------|--------------------------------------------------------|
|[predict_fl()](predict-fl.md)|Vorhersagen mithilfe eines vorhandenen trainierten Machine Learning-Modells. |
|[predict_onnx_fl()](predict-onnx-fl.md)| Prognostizieren Sie die Verwendung eines vorhandenen trainierten Machine Learning-Modells im onnx-Format. |

## <a name="series-processing-functions"></a>Reihen Verarbeitungsfunktionen

|Funktionsname     |BESCHREIBUNG                                          |
|-------------------------|--------------------------------------------------------|
|[quantize_fl()](quantize-fl.md)|Quantisieren Sie metrikspalten. |
|[series_fit_poly_fl()](series-fit-poly-fl.md)|Anpassen eines Polynoms an Reihen mithilfe der Regressionsanalyse. |
|[series_moving_avg_fl()](series-moving-avg-fl.md)|Wendet einen Filter für den gleitenden Durchschnitt für eine Reihe an. |
|[series_rolling_fl()](series-rolling-fl.md)|Wendet eine parallele Aggregations Funktion für eine Reihe an. |
