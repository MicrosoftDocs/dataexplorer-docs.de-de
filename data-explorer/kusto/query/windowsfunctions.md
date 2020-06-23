---
title: 'Fenster Funktionen: Azure Daten-Explorer'
description: Dieser Artikel beschreibt Fenster Funktionen in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: 1e93764058c6d890568e90f67472ec4306a092f8
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128698"
---
# <a name="window-functions-overview"></a>Übersicht über Fensterfunktionen

Fenster Funktionen arbeiten mit mehreren Zeilen (Datensätzen) in einem Zeilen Satz gleichzeitig. Im Gegensatz zu Aggregations Funktionen erfordern Fenster Funktionen, dass die Zeilen im Rowset serialisiert werden (für diese eine bestimmte Reihenfolge). Fenster Funktionen sind möglicherweise abhängig von der Reihenfolge, um das Ergebnis zu bestimmen.

Fenster Funktionen können nur für serialisierte Sätze verwendet werden. Die einfachste Möglichkeit zum Serialisieren eines Zeilen Satzes ist die Verwendung des [serialisieren-Operators](./serializeoperator.md). Dieser Operator "friert" die Reihenfolge der Zeilen in beliebiger Weise ab. Wenn die Reihenfolge serialisierter Zeilen semantisch wichtig ist, verwenden Sie den [Sort-Operator](./sortoperator.md) , um eine bestimmte Reihenfolge zu erzwingen.

Der Serialisierungsprozess weist nicht triviale Kosten auf. Beispielsweise kann die Abfrage Parallelität in vielen Szenarios verhindert werden. Wenden Sie daher keine unnötige Serialisierung an. Ordnen Sie die Abfrage ggf. neu an, um die Serialisierung des kleinsten möglichen Zeilen Satzes auszuführen.

## <a name="serialized-row-set"></a>Serialisierter Zeilen Satz

Ein beliebiges Rowset (z. b. eine Tabelle oder die Ausgabe eines tabellarischen Operators) kann auf eine der folgenden Arten serialisiert werden:

1. Durchsortieren des Zeilen Satzes. Im folgenden finden Sie eine Liste der Operatoren, die sortierte Zeilen Sätze ausgeben.
2. Mithilfe des [serialisieren-Operators](./serializeoperator.md).

Viele tabellarische Operatoren serialisieren die Ausgabe immer dann, wenn die Eingabe bereits serialisiert ist, auch wenn der Operator nicht selbst gewährleistet, dass das Ergebnis serialisiert wird. Diese Eigenschaft ist z. b. für den [Erweiterung-Operator](./extendoperator.md), den [Project-Operator](./projectoperator.md)und den WHERE- [Operator](./whereoperator.md)garantiert.

## <a name="operators-that-emit-serialized-row-sets-by-sorting"></a>Operatoren, die serialisierte Zeilen Sätze durchsortieren ausgeben

* [order-Operator](./orderoperator.md)
* [sort-Operator](./sortoperator.md)
* [top-Operator](./topoperator.md)
* [top-hitters-Operator](./tophittersoperator.md)
* [top-nested operator](./topnestedoperator.md)

## <a name="operators-that-preserve-the-serialized-row-set-property"></a>Operatoren, die die serialisierte Zeilen Satz Eigenschaft beibehalten

* [extend-Operator](./extendoperator.md)
* [mv-expand-Operator](./mvexpandoperator.md)
* [parse-Operator](./parseoperator.md)
* [project-Operator](./projectoperator.md)
* [project-away-Operator](./projectawayoperator.md)
* [project-rename-Operator](./projectrenameoperator.md)
* [take-Operator](./takeoperator.md)
* [where-Operator](./whereoperator.md)
