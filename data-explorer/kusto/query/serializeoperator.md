---
title: 'Serialisieren-Operator: Azure Daten-Explorer'
description: In diesem Artikel wird der serialisierungsoperator in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 708a5ccd5f8402dedb074a6ab8c17b1d7762839c
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717120"
---
# <a name="serialize-operator"></a>serialize-Operator

Markiert, dass die Reihenfolge des Eingabezeilen Satzes sicher für Fenster Funktionen verwendet werden kann.

Der Operator hat eine deklarative Bedeutung. Dadurch wird der Eingabezeilen Satz als serialisiert (geordnet) markiert, sodass [Fenster Funktionen](./windowsfunctions.md) darauf angewendet werden können.

```kusto
T | serialize rn=row_number()
```

**Syntax**

`serialize`[*Name1* `=` *Expr1* [ `,` *name2* `=` *expr2*]...]

* Die *Name*- / *expr* -Paare ähneln diesen Paaren im Erweiterungs [Operator](./extendoperator.md).

**Beispiel**

```kusto
Traces
| where ActivityId == "479671d99b7b"
| serialize

Traces
| where ActivityId == "479671d99b7b"
| serialize rn = row_number()
```

Der Ausgabezeilen Satz der folgenden Operatoren ist als serialisiert markiert.

[Range](./rangeoperator.md), [Sort](./sortoperator.md), [Order](./orderoperator.md), [Top](./topoperator.md), [Top-Hitters](./tophittersoperator.md), [GetSchema](./getschemaoperator.md).

Der Ausgabezeilen Satz der folgenden Operatoren ist als nicht serialisiert markiert.

[Sample](./sampleoperator.md), [Sample-verschieden](./sampledistinctoperator.md), [verschieden](./distinctoperator.md), [Join](./joinoperator.md), [Top-netsted](./topnestedoperator.md), [count](./countoperator.md), zusammen [fassen](./summarizeoperator.md), [Facetten](./facetoperator.md), [MV-Expand](./mvexpandoperator.md), [evaluieren](./evaluateoperator.md), [verringern nach](./reduceoperator.md), [make-Series](./make-seriesoperator.md)

Alle anderen Operatoren behalten die serialisierungseigenschaft bei. Wenn der Eingabezeilen Satz serialisiert wird, wird auch der Ausgabezeilen Satz serialisiert.
