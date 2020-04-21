---
title: Überläufe - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt Überläufe in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 22db905788e1313cad2eb229239a390c28bcd5c2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523153"
---
# <a name="overflows"></a>Überläufe

Ein Überlauf tritt auf, wenn das Ergebnis einer Berechnung für den Zieltyp zu groß ist.
Dieses Phänomen führt in der Regel zu einem [teilweisen Abfragefehler](partialqueryfailures.md).

Die folgende Abfrage führt z. B. zu einem Überlauf:

```kusto
let Weight = 92233720368547758;
range x from 1 to 3 step 1
| summarize percentilesw(x, Weight * 100, 50)
```

Die Implementierung `percentilesw()` von Kusto `Weight` akkumuliert den Ausdruck für Werte, die "nahe genug" sind.
In diesem Fall löst die Akkumulation einen Überlauf aus, da er nicht in eine signierte 64-Bit-Ganzzahl passt.

In der Regel sind Überläufe jedoch das Ergebnis eines "Fehlers" in der Abfrage, da Kusto 64-Bit-Typen für arithmetische Berechnungen verwendet.
Die beste Vorgehensweise in solchen Fällen besteht darin, aus der Fehlermeldung zu identifizieren, welche Funktion oder Aggregation einen Überlauf ausgelöst hat, und sicherzustellen, dass die Eingabeargumente zu Werten ausgewertet werden, die sinnvoll sind.
