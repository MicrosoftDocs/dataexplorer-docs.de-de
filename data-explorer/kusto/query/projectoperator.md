---
title: 'Project-Operator: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt den Projekt Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 76ead8fabe755d5e3e200a767cb8b7518121b2ac
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128953"
---
# <a name="project-operator"></a>project-Operator

Wählen Sie die Spalten aus, die einbezogen, umbenannt oder gelöscht werden sollen, und fügen Sie neue berechnete Spalten ein. 

Die Reihenfolge der Spalten im Ergebnis wird durch die Reihenfolge der Argumente festgelegt. Nur die in den Argumenten angegebenen Spalten werden in das Ergebnis eingeschlossen. Alle anderen Spalten in der Eingabe werden gelöscht.  (Siehe auch `extend`.)

```kusto
T | project cost=price*quantity, price
```

**Syntax**

*T* `| project` *ColumnName* [ `=` *Ausdruck*] [ `,` ...]
  
oder
  
*T* `| project` [*ColumnName*  |  `(` *ColumnName*[ `,` ] `)` `=` ] *Ausdruck* [ `,` ...]

**Argumente**

* *T*: die Eingabe Tabelle.
* *ColumnName:* Optionaler Name einer Spalte, die in der Ausgabe angezeigt werden soll. Wenn kein *Ausdruck*vorhanden ist, ist *ColumnName* obligatorisch, und eine Spalte mit diesem Namen muss in der Eingabe angezeigt werden. Wenn keine Angabe erfolgt, wird der Name automatisch generiert. Wenn *Expression* mehr als eine Spalte zurückgibt, kann eine Liste mit Spaltennamen in Klammern angegeben werden. In diesem Fall erhalten die Ausgabespalten des *Ausdrucks*die angegebenen Namen, wobei alle restlichen Ausgabespalten gelöscht werden, sofern vorhanden. Wenn die Liste der Spaltennamen nicht angegeben wird, werden die Ausgabespalten aller *Ausdrücke*mit generierten Namen der Ausgabe hinzugefügt.
* *Expression:* Optionaler skalarer Ausdruck, der auf die Eingabespalten verweist. Wenn *ColumnName* nicht weggelassen wird, ist der *Ausdruck* obligatorisch.

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