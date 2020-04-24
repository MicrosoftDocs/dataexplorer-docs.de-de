---
title: Fensterfunktionen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden Fensterfunktionen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: ab4f6da2478ba4de81b2034c1cb07458daa80bd0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504249"
---
# <a name="window-functions"></a>Fensterfunktionen

Fensterfunktionen arbeiten auf mehreren Zeilen (Datensätzen) in einer Zeile, die gleichzeitig festgelegt ist.
Im Gegensatz zu Aggregationsfunktionen müssen die Zeilen im Zeilensatz **serialisiert** werden (sie haben eine bestimmte Reihenfolge), da Fensterfunktionen von der Reihenfolge abhängen können, um das Ergebnis zu bestimmen.

Fensterfunktionen können nicht für Zeilensätze verwendet werden, die nicht serialisiert sind, und geben einen Fehler aus, wenn sie in einem solchen Kontext von einer Abfrage verwendet werden. Die einfachste Möglichkeit, einen Zeilensatz zu serialisieren, besteht darin, den [Serialisierungsoperator](./serializeoperator.md)zu verwenden, der einfach die Reihenfolge der Zeilen "einfriert" (in einer nicht spezifizierten willkürlichen Weise).
Wenn die Reihenfolge der serialisierten Zeilen semantisch wichtig ist, kann man den [Sortieroperator](./sortoperator.md) verwenden, um eine bestimmte Reihenfolge zu erzwingen.

Dem Serialisierungsprozess sind nicht triviale Kosten verbunden. Beispielsweise kann es in vielen Szenarien abfrageparallelität verhindern. Daher wird dringend empfohlen, die Serialisierung nicht unnötig anzuwenden und die Abfrage bei Bedarf neu anzurichten, damit die Serialisierung auf der kleinsten Zeilenmenge möglich ist.

## <a name="serialized-row-set"></a>Serialisierter Zeilensatz

Ein beliebiger Zeilensatz (z. B. eine Tabelle oder die Ausgabe eines tabellarischen Operators) kann auf eine der folgenden Arten serialisiert werden:

1. Durch Sortieren des Zeilensatzes. Eine Liste der Operatoren, die sortierte Zeilensätze aussenden, finden Sie unten.
2. Verwenden des [Serialisierungsoperators](./serializeoperator.md).

Beachten Sie, dass viele tabellarische Operatoren, während sie an sich nicht garantieren, dass ihr Ergebnis serialisiert wird, die Eigenschaft haben, dass, wenn die Eingabe serialisiert wird, dies auch die Ausgabe ist. Diese Eigenschaft ist z. B. für den [Extendoperator](./extendoperator.md), den [Projektoperator](./projectoperator.md)und den [where-Operator](./whereoperator.md)garantiert.

## <a name="operators-that-emit-serialized-row-sets-by-sorting"></a>Operatoren, die serialisierte Zeilensätze durch Sortieren aussenden

* [order-Operator](./orderoperator.md)
* [sort-Operator](./sortoperator.md)
* [Top-Operator](./topoperator.md)
* [top-hitters-Operator](./tophittersoperator.md)
* [top-nested operator](./topnestedoperator.md)

## <a name="operators-that-preserve-the-serialized-row-set-property"></a>Operatoren, die die serialisierte Zeilensatzeigenschaft beibehalten

* [extend-Operator](./extendoperator.md)
* [mv-expand-Operator](./mvexpandoperator.md)
* [parse-Operator](./parseoperator.md)
* [project-Operator](./projectoperator.md)
* [project-away-Operator](./projectawayoperator.md)
* [project-rename-Operator](./projectrenameoperator.md)
* [take-Operator](./takeoperator.md)
* [wo der Betreiber](./whereoperator.md)