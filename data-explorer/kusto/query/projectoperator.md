---
title: Projektbetreiber - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der Projektoperator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: de13c240180d00b82736a0dd35cb83c08639682f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510930"
---
# <a name="project-operator"></a>Projektbetreiber

Wählen Sie die Spalten aus, die einbezogen, umbenannt oder gelöscht werden sollen, und fügen Sie neue berechnete Spalten ein. 

Die Reihenfolge der Spalten im Ergebnis wird durch die Reihenfolge der Argumente festgelegt. Nur die in den Argumenten angegebenen Spalten werden im Ergebnis enthalten. Alle anderen Spalten in der Eingabe werden gelöscht.  (Siehe auch `extend`.)

```kusto
T | project cost=price*quantity, price
```

**Syntax**

*T* `| project` *ColumnName* [`=` `,` *Ausdruck*] [ ...]
  
oder
  
*T* `| project` [*ColumnName* | `(``,`*ColumnName*[`,` ]`)` `=`] *Ausdruck* [ ...]

**Argumente**

* *T*: Die Eingabetabelle.
* *Spaltenname:* Optionaler Name einer Spalte, die in der Ausgabe angezeigt werden soll. Wenn kein *Ausdruck*vorhanden ist, ist *ColumnName* obligatorisch, und eine Spalte mit diesem Namen muss in der Eingabe angezeigt werden. Wenn nicht angegeben, wird der Name automatisch generiert. Wenn *Ausdruck* mehr als eine Spalte zurückgibt, kann eine Liste von Spaltennamen in Klammern angegeben werden. In diesem Fall erhalten die Ausgabespalten von *Expression*die angegebenen Namen, und alle restlichen Ausgabespalten werden, falls vorhanden, fallen. Wenn die Liste der Spaltennamen nicht angegeben ist, werden alle Ausgabespalten von *Expression*mit generierten Namen zur Ausgabe hinzugefügt.
* *Expression:* Optionaler skalarer Ausdruck, der auf die Eingabespalten verweist. Wenn *ColumnName* nicht weggelassen wird, ist *Ausdruck* obligatorisch.

    Das Zurückgeben einer neuen berechneten Spalte mit dem gleichen Namen wie eine vorhandene Spalte der Eingabe ist zulässig.

**Rückgabe**

Eine Tabelle, deren Spalten als Argumente benannt sind, und die ebenso viele Zeilen wie die Eingabetabelle aufweist.

**Beispiel**

Das folgende Beispiel zeigt mehrere Arten von Manipulationen, die mithilfe des `project` -Operators durchgeführt werden können. Die Eingabetabelle `T` umfasst drei Spalten vom Typ `int`: `A`, `B` und `C`. 

```kusto
T
| project
    X=C,                       // Rename column C to X
    A=2*B,                     // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B                      // Calculate a new column B from the old B
```

[series_stats](series-statsfunction.md) ist ein Beispiel für eine Funktion, die mehrere Spalten zurückgibt.