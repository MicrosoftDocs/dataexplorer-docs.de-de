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
ms.openlocfilehash: 92ee54ce675c2e8396d842fdf029f2d3f3d5380b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345673"
---
# <a name="serialize-operator"></a>serialize-Operator

Markiert, dass die Reihenfolge des Eingabezeilen Satzes sicher für Fenster Funktionen verwendet werden kann.

Der Operator hat eine deklarative Bedeutung. Dadurch wird der Eingabezeilen Satz als serialisiert (geordnet) markiert, sodass [Fenster Funktionen](./windowsfunctions.md) darauf angewendet werden können.

```kusto
T | serialize rn=row_number()
```

## <a name="syntax"></a>Syntax

`serialize`[*Name1* `=` *Expr1* [ `,` *name2* `=` *expr2*]...]

* Die *Name*- / *expr* -Paare ähneln diesen Paaren im Erweiterungs [Operator](./extendoperator.md).

## <a name="example"></a>Beispiel

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
