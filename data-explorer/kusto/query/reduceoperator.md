---
title: Operator reduzieren - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der Reduzierenoperator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 33d4ac202b61fdaa1b92291407cdd2783d947c6e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510505"
---
# <a name="reduce-operator"></a>reduce-Operator

Gruppiert eine Reihe von Zeichenfolgen basierend auf der Ähnlichkeit der Werte.

```kusto
T | reduce by LogMessage with threshold=0.1
```

Für jede solche Gruppe gibt sie ein **Muster** aus, das die`*`Gruppe am besten beschreibt (möglicherweise mit dem Asterix - ) Zeichen, um Platzhalter darzustellen), eine **Anzahl** der Werte in der Gruppe und einen **Vertreter** der Gruppe (einer der ursprünglichen Werte in der Gruppe).

**Syntax**

*T* `|` `kind` `characters` `=` `threshold` `=` *Threshold*`,` `with` *ReduceKind* `by` *Expr* *Characters*[ `=` ReduceKind ] Expr [ [ Schwellenwert ] [ Zeichen ] ] `reduce`

**Argumente**

* *Expr*: Ein Ausdruck, `string` der zu einem Wert ausgewertet wird.
* *Schwellenwert*: `real` Ein Literal im Bereich (0..1). Der Standardwert ist 0.1. Bei großen Eingaben sollte der Schwellenwert klein sein. 
* *Zeichen*: `string` Ein Literal, das eine Liste von Zeichen enthält, die der Liste der Zeichen hinzugefügt werden sollen, die keinen Ausdruck unterbrechen. (Wenn Sie z. `aaa=bbbb` `aaa:bbb` B. einen ganzen Begriff verwenden `=` möchten `:`und `":="` jeweils einen ganzen Begriff verwenden, anstatt auf zu unterbrechen, und als Zeichenfolgenliteral verwenden.)
* *ReduceKind*: Gibt die Reduzieren-Geschmack. Der vorerst einzige gültige Wert `source`ist .

**Rückgabe**

Dieser Operator gibt eine Tabelle`Pattern` `Count`mit `Representative`drei Spalten ( , , und ) und so vielen Zeilen zurück, wie Gruppen vorhanden sind. `Pattern`ist der Musterwert für `*` die Gruppe, der als Platzhalter `Count` verwendet wird (was beliebige Einfügezeichenfolgen darstellt), `Representative` zählt, wie viele Zeilen in der Eingabe für den Operator durch dieses Muster dargestellt werden, und ist ein Wert aus der Eingabe, die in diese Gruppe fällt.

Wenn `[kind=source]` angegeben, fügt der Operator `Pattern` die Spalte an die vorhandene Tabellenstruktur an.
Beachten Sie, dass die Syntax eines Schemas dieser Variante zukünftigen Änderungen unterliegen kann.

Das Ergebnis von `reduce by city` kann z.B. Folgendes enthalten: 

|Muster     |Anzahl |Representative|
|------------|------|--------------|
| San *      | 5182 |San Bernard   |
| Saint *    | 2846 |Saint Lucy    |
| Moskau     | 3726 |Moskau        |
| \* -auf- \* | 2730 |Eins -on- Eins  |
| Paris      | 2716 |Paris         |

Ein weiteres Beispiel für die angepasste Tokenisierung:

```kusto
range x from 1 to 1000 step 1
| project MyText = strcat("MachineLearningX", tostring(toint(rand(10))))
| reduce by MyText  with threshold=0.001 , characters = "X" 
```

|Muster         |Anzahl|Representative   |
|----------------|-----|-----------------|
|MachineLearning*|1000 |MachineLearningX4|

**Beispiele**

Das folgende Beispiel zeigt, `reduce` wie man den Operator auf eine "sanitisierte" Eingabe anwenden kann, bei der GUIDs in der zu reduzierenden Spalte ersetzt werden, bevor

```kusto
// Start with a few records from the Trace table.
Trace | take 10000
// We will reduce the Text column which includes random GUIDs.
// As random GUIDs interfere with the reduce operation, replace them all
// by the string "GUID".
| extend Text=replace(@"[[:xdigit:]]{8}-[[:xdigit:]]{4}-[[:xdigit:]]{4}-[[:xdigit:]]{4}-[[:xdigit:]]{12}", @"GUID", Text)
// Now perform the reduce. In case there are other "quasi-random" identifiers with embedded '-'
// or '_' characters in them, treat these as non-term-breakers.
| reduce by Text with characters="-_"
```

**Siehe auch**

[autocluster](./autoclusterplugin.md)

**Hinweise**

Die Implementierung `reduce` des Operators basiert weitgehend auf dem Papier [A Data Clustering Algorithm for Mining Patterns From Event Logs](https://ristov.github.io/publications/slct-ipom03-web.pdf)von Risto Vaarandi.