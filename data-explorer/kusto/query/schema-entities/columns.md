---
title: Spalten - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden Spalten in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 17406eb94f8e29f4b8ab87653ab41df737fa15ef
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509485"
---
# <a name="columns"></a>Spalten

Jede [Tabelle](tables.md) in Kusto und jeder tabellarische Datenstrom ist ein rechteckiges Raster aus Spalten und Zeilen. Jede Spalte in der Tabelle hat einen Namen und einen bestimmten [skalaren Datentyp](../scalar-data-types/index.md). Die Spalten einer Tabelle oder eines tabellarischen Datenstroms sind geordnet, sodass eine Spalte auch eine bestimmte Position in der Spaltenauflistung der Tabelle hat.

**Hinweise**  

* Bei Spaltennamen wird die Groß-/Kleinschreibung beachtet.
* Spaltennamen folgen den Regeln für [Entitätsnamen](./entity-names.md).
* Die maximale Anzahl von Spalten pro Tabelle beträgt 10.000.

In Abfragen sind Spalten in der Regel nur Verweise nach Namen. Sie können nur in Ausdrücken angezeigt werden, und der Abfrageoperator, unter dem der Ausdruck angezeigt wird, bestimmt die Tabelle oder den tabellentabellarischen Datenstrom, sodass der Name der Spalte nicht weiter begrenzt werden muss. In der folgenden Abfrage haben wir beispielsweise einen unbenannten tabellentabellarischen Datenstrom (definiert durch den [Datatable-Operator,](../datatableoperator.md) der über eine einzelne Spalte verfügt, `c`. Der tabellarische Datenstrom wird dann nach einem Prädikat für den Wert dieser Spalte gefiltert, wodurch ein neuer unbenannter tabellenförmiger Datenstrom mit denselben Spalten, aber weniger Zeilen entsteht. Der [as-Operator](../asoperator.md) benennt dann den tabellarischen Datenstrom, und sein Wert wird als Ergebnis der Abfrage zurückgegeben.
Beachten Sie insbesondere, `c` wie auf die Spalte mit Namen verwiesen wird, ohne auf ihren Container verweisen zu müssen (in der Tat hat dieser Container keinen Namen):

```kusto
datatable (c:int) [int(-1), 0, 1, 2, 3]
| where c*c >= 2
| as Result
```

> Wie in der Welt der relationalen Datenbanken häufig üblich, werden Zeilen manchmal **als Datensätze** und Spalten manchmal als **Attribute**bezeichnet.

Details zum Verwalten von Spalten finden Sie unter [Verwalten von Spalten](../../management/columns.md).