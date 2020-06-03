---
title: 'Über Flüsse: Azure Daten-Explorer'
description: Dieser Artikel beschreibt Überläufe in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 063165c91319ef5f4183a8ce8f83364fd8188072
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84328958"
---
# <a name="overflows"></a>Überläufe

Ein Überlauf tritt auf, wenn das Ergebnis einer Berechnung für den Zieltyp zu groß ist.
Der Überlauf führt in der Regel zu einem [Teil Abfrage Fehler](partialqueryfailures.md).

Die folgende Abfrage führt z. b. zu einem Überlauf.

```kusto
let Weight = 92233720368547758;
range x from 1 to 3 step 1
| summarize percentilesw(x, Weight * 100, 50)
```

Die Kusto- `percentilesw()` Implementierung akkumuliert den `Weight` Ausdruck für Werte, die "close enough" sind.
In diesem Fall löst die Akkumulation einen Überlauf aus, da Sie nicht in eine signierte 64-Bit-Ganzzahl passt.

In der Regel sind Überschreitungen das Ergebnis eines "Fehlers" in der Abfrage, da Kusto 64-Bit-Typen für arithmetische Berechnungen verwendet.
Die beste Vorgehensweise besteht darin, die Fehlermeldung zu überprüfen und die Funktion oder Aggregation zu identifizieren, die den Überlauf ausgelöst hat. Stellen Sie sicher, dass die Eingabeargumente zu den Werten ausgewertet werden.
