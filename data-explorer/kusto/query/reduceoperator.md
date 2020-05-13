---
title: 'Reduce-Operator: Azure Daten-Explorer'
description: In diesem Artikel wird der reduzierungsoperator in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7d157244a167e1264b454cd8cd3c103e297c3263
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373046"
---
# <a name="reduce-operator"></a>reduce-Operator

Gruppiert einen Satz von Zeichen folgen basierend auf der Ähnlichkeit von Werten.

```kusto
T | reduce by LogMessage with threshold=0.1
```

Für jede solche Gruppe gibt Sie ein **Muster** aus, das die Gruppe am besten beschreibt (möglicherweise das Zeichen "Asterix ()", `*` um Platz **count** Halter darzustellen), eine Anzahl der Werte in der Gruppe und einen **Vertreter** der Gruppe (einer der ursprünglichen Werte in der Gruppe).

**Syntax**

*T* `|` `reduce` [ `kind` `=` *reducekind*] `by` *expr* [ `with` [ `threshold` `=` *Schwellenwert*] [ `,` `characters` `=` *Zeichen*]]

**Argumente**

* *Expr*: ein Ausdruck, der zu einem `string` Wert ausgewertet wird.
* *Schwellenwert*: ein `real` Literalwert im Bereich (0.. 1). Der Standardwert ist 0,1. Bei großen Eingaben sollte der Schwellenwert klein sein. 
* *Zeichen*: ein `string` Literalwert, der eine Liste von Zeichen enthält, die der Liste der Zeichen hinzugefügt werden sollen, die keinen Begriff unterbrechen. (Wenn Sie z. b. `aaa=bbbb` und `aaa:bbb` zu jedem ein ganzer Begriff sein möchten, anstatt an und zu unterbrechen `=` `:` , verwenden Sie `":="` als Zeichenfolgenliterale.)
* *Reducekind*: gibt den reduzierungstyp an. Der einzige gültige Wert für die Zeit, die ist `source` .

**Rückgabe**

Dieser Operator gibt eine Tabelle mit drei Spalten ( `Pattern` , `Count` und `Representative` ) sowie so viele Zeilen zurück, wie es Gruppen gibt. `Pattern`der Pattern-Wert für die Gruppe, der `*` als Platzhalter verwendet wird (die beliebige Einfügezeichenfolgen darstellt), `Count` zählt, wie viele Zeilen in der Eingabe für den Operator durch dieses Muster dargestellt werden, und `Representative` ist ein Wert aus der Eingabe, der in diese Gruppe fällt.

Wenn `[kind=source]` angegeben wird, fügt der Operator die `Pattern` Spalte an die vorhandene Tabellenstruktur an.
Beachten Sie, dass die Syntax für ein Schema dieser Art möglicherweise zukünftigen Änderungen unterliegt.

Das Ergebnis von `reduce by city` kann z.B. Folgendes enthalten: 

|Muster     |Anzahl |Representative|
|------------|------|--------------|
| San *      | 5182 |San Bernard   |
| Saint *    | 2846 |St. Lucy    |
| Moskau     | 3726 |Moskau        |
| \* -auf- \* | 2730 |One-on-One  |
| Paris      | 2716 |Paris         |

Ein weiteres Beispiel mit angepasster Tokenisierung:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 1000 step 1
| project MyText = strcat("MachineLearningX", tostring(toint(rand(10))))
| reduce by MyText  with threshold=0.001 , characters = "X" 
```

|Muster         |Anzahl|Representative   |
|----------------|-----|-----------------|
|MachineLearning|1000 |MachineLearningX4|

**Beispiele**

Im folgenden Beispiel wird gezeigt, wie der- `reduce` Operator auf eine "berattete" Eingabe angewendet werden kann, in der GUIDs in der Spalte, die reduziert wird, vor der Reduzierung ersetzt werden.

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

Die Implementierung des- `reduce` Operators basiert größtenteils auf dem Papier [A Data Clustering-Algorithmus für Mining Muster von Ereignisprotokollen](https://ristov.github.io/publications/slct-ipom03-web.pdf)von Risto vaarandi.
