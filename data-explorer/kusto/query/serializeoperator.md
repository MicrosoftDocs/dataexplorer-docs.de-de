---
title: Serialisierungsoperator - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der Serialisierungsoperator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5402d1e1fcceb42f02643bf24918ed07beddaed7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509111"
---
# <a name="serialize-operator"></a>serialize-Operator

Markiert, dass die Reihenfolge des Eingabezeilensatzes für die Verwendung von Fensterfunktionen sicher ist.

Der Operator hat eine deklarative Bedeutung und markiert den Eingabezeilensatz als serialisiert (geordnet), sodass [Fensterfunktionen](./windowsfunctions.md) darauf angewendet werden können.

```kusto
T | serialize rn=row_number()
```

**Syntax**

`serialize`[*Name1* `=` *Expr1* [`,` *Name2* `=` *Expr2*]...]

* Die *Name*/*Expr-Paare* ähneln denen im [Extend-Operator](./extendoperator.md).

**Beispiel**

```kusto
Traces
| where ActivityId == "479671d99b7b"
| serialize

Traces
| where ActivityId == "479671d99b7b"
| serialize rn = row_number()
```

Der Ausgabezeilensatz der folgenden Operatoren wird als serialisiert markiert:

[bereich](./rangeoperator.md), [sortieren](./sortoperator.md), [bestellen](./orderoperator.md), [top](./topoperator.md), [top-hitters](./tophittersoperator.md), [getschema](./getschemaoperator.md).

Der Ausgabezeilensatz der folgenden Operatoren ist als nicht serialisiert gekennzeichnet:

[Beispiel](./sampleoperator.md), [Sample-distinct](./sampledistinctoperator.md), [distinct](./distinctoperator.md), [join](./joinoperator.md), [top-nested](./topnestedoperator.md), [count](./countoperator.md), [summarize](./summarizeoperator.md), [facet](./facetoperator.md), [mv-expand](./mvexpandoperator.md), [evaluate](./evaluateoperator.md) [, reduce by](./reduceoperator.md), [make-series](./make-seriesoperator.md)

Alle anderen Operatoren behalten die Serialisierungseigenschaft bei (wenn der Eingabezeilensatz serialisiert wird, ebenso wie der Ausgabezeilensatz).