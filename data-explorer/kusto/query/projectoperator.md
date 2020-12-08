---
title: 'Project-Operator: Azure Data Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird der Project-Operator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: f94003573cab076d8fa83537cb7868e21b9b084c
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513266"
---
# <a name="project-operator"></a>project-Operator

Wählen Sie die Spalten aus, die einbezogen, umbenannt oder gelöscht werden sollen, und fügen Sie neue berechnete Spalten ein. 

Die Reihenfolge der Spalten im Ergebnis wird durch die Reihenfolge der Argumente festgelegt. Nur die in den Argumenten angegebenen Spalten sind im Ergebnis enthalten. Alle anderen Spalten in der Eingabe werden gelöscht.  (Siehe auch `extend`.)

```kusto
T | project cost=price*quantity, price
```

## <a name="syntax"></a>Syntax

*T* `| project` *ColumnName* [`=` *Expression*] [`,` ...]
  
oder
  
*T* `| project` [*ColumnName* | `(`*ColumnName*[`,`]`)` `=`] *Expression* [`,` ...]

## <a name="arguments"></a>Argumente

* *T*: Die Eingabetabelle.
* *ColumnName:* Optionaler Name einer Spalte, die in der Ausgabe angezeigt werden soll. Wenn *Expression* nicht vorhanden ist, ist *ColumnName* zwingend erforderlich, und die Eingabe muss eine Spalte mit diesem Namen enthalten. Wenn keine Angabe erfolgt, wird der Name automatisch generiert. Wenn *Expression* mehrere Spalten zurückgibt, kann eine Liste mit Spaltennamen in Klammern angegeben werden. In diesem Fall erhalten die Ausgabespalten von *Expression* die angegebenen Namen, wobei alle restlichen Ausgabespalten gelöscht werden, sofern vorhanden. Wenn keine Liste der Spaltennamen angegeben wird, werden alle Ausgabespalten von *Expression* mit generierten Namen der Ausgabe hinzugefügt.
* *Expression:* Optionaler skalarer Ausdruck, der auf die Eingabespalten verweist. Wenn *ColumnName* angegeben wird, ist *Expression* obligatorisch.

    Das Zurückgeben einer neuen berechneten Spalte mit dem gleichen Namen wie eine vorhandene Spalte der Eingabe ist zulässig.

## <a name="returns"></a>Gibt zurück

Eine Tabelle, deren Spalten als Argumente benannt sind, und die ebenso viele Zeilen wie die Eingabetabelle aufweist.

## <a name="example"></a>Beispiel

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