---
title: arg_min () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird arg_min () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/12/2019
ms.openlocfilehash: 5c3e7912839dd3258c4a3f96530fbf438bf4ef4f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245617"
---
# <a name="arg_min-aggregation-function"></a>arg_min () (Aggregations Funktion)

Sucht eine Zeile in der Gruppe, die *exprtominimize*minimiert, und gibt den Wert von *exprtoreturn* zurück (oder `*` , um die gesamte Zeile zurückzugeben).

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

## <a name="syntax"></a>Syntax

`summarize`[ `(` *Nameexprtominimize* `,` *nameexprtoreturn* [ `,` ...] `)=` ] `arg_min` `(` *Exprtominimize*, `*`  |  *exprtoreturn* [ `,` ...]`)`

## <a name="arguments"></a>Argumente

* *Exprtominimize*: der Ausdruck, der für die Aggregations Berechnung verwendet wird. 
* *Exprtoreturn*: Ausdruck, der zum Zurückgeben des Werts verwendet wird, wenn " *exprtominimize* " minimal ist. Der zurück zugebende Ausdruck kann ein Platzhalter Zeichen (*) sein, um alle Spalten der Eingabe Tabelle zurückzugeben.
* *Nameexprtominimize*: ein optionaler Name für die Ergebnisspalte, die *exprtominimize*darstellt.
* *Nameexprtoreturn*: zusätzliche optionale Namen für die Ergebnis Spalten, die " *exprtoreturn*" darstellen.

## <a name="returns"></a>Rückgabe

Sucht eine Zeile in der Gruppe, die *exprtominimize*minimiert, und gibt den Wert von *exprtoreturn* zurück (oder `*` , um die gesamte Zeile zurückzugeben).

## <a name="examples"></a>Beispiele

Günstigste Lieferanten der einzelnen Produkte anzeigen:

```kusto
Supplies | summarize arg_min(Price, Supplier) by Product
```

Alle Details anzeigen, nicht nur der Lieferanten Name:

```kusto
Supplies | summarize arg_min(Price, *) by Product
```

Auffinden der südlichsten Stadt in jedem Kontinent mit dem Land:

```kusto
PageViewLog 
| summarize (latitude, min_lat_City, min_lat_country)=arg_min(latitude, City, country) 
    by continent
```

:::image type="content" source="images/arg-min-aggfunction/arg-min.png" alt-text="Arg min.":::
