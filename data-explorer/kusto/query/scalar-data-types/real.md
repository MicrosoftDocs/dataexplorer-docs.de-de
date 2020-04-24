---
title: Der reale Datentyp - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der tatsächliche Datentyp in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: f69b74670fefcff6f6c24d5235f58beb98b0405a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509672"
---
# <a name="the-real-data-type"></a>Der reale Datentyp

Der `real` Datentyp stellt eine 64-Bit breite Gleitkommazahl mit doppelter Genauigkeit dar.

Literale des `real` Datentyps haben die gleiche Darstellung wie . NET es `System.Double`. `1.0`, `0.1`, `1e5` und sind alle `real`Literale vom Typ .

Es gibt mehrere spezielle Literalformen:
* `real(null)`: Der ist der [NULL-Wert](null-values.md).
* `real(nan)`: Not-a-Number (NaN). Beispiel: Das Ergebnis der `0.0` Teilung `0.0`eines durch einen anderen .
* `real(+inf)`: Positive Unendlichkeit. Beispiel: Das Ergebnis `1.0` der `0.0`Teilung durch .
* `real(-inf)`: Negative Unendlichkeit. Beispiel: Das Ergebnis `-1.0` der `0.0`Teilung durch .