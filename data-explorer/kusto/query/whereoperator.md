---
title: wobei Operator (hat, enthält, startet, endet, mit regex übereinstimmt) - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird beschrieben, wo der Operator (hat, enthält, startet, endet, mit regex übereinstimmt) in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a0e1486691740600fb5de4316982e8d43b13ce3a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504300"
---
# <a name="where-operator-has-contains-startswith-endswith-matches-regex"></a>wobei Operator (hat, enthält, startet, endet, entspricht regex)

Filtert eine Tabelle auf die Teilmenge der Zeilen, die ein Prädikat erfüllen.

```kusto
T | where fruit=="apple"
```

**Alias** `filter`

**Syntax**

*T* `| where` *Prädikat*

**Argumente**

* *T*: Die tabellarische Eingabe, deren Datensätze gefiltert werden sollen.
* *Prädikat*: `boolean` Ein [Ausdruck](./scalar-data-types/bool.md) über den Spalten von *T*. Sie wird für jede Zeile in *T*ausgewertet.

**Rückgabe**

Zeilen in *T*, für die *Predicate* auf `true` festgelegt ist.

**Anmerkungen** Nullwerte: Alle Filterfunktionen geben false zurück, wenn sie mit NULL-Werten verglichen werden. Sie können spezielle null-aware Funktionen verwenden, um Abfragen zu schreiben, die null-Werte berücksichtigen: [isnull()](./isnullfunction.md), [isnotnull()](./isnotnullfunction.md), [isempty()](./isemptyfunction.md), [isnotempty()](./isnotemptyfunction.md). 

**Tipps**

So erzielen Sie die optimale Leistung:

* **Verwenden Sie einfache Vergleiche** zwischen dem Spaltennamen und Konstanten. ('Konstant' bedeutet Konstante über `now()` der `ago()` Tabelle - also und sind OK, und so werden Skalarwerte mit einer [ `let` Anweisung](./letstatement.md)zugewiesen .)

    Beispielsweise wird `where Timestamp >= ago(1d)` gegenüber `where floor(Timestamp, 1d) == ago(1d)` bevorzugt.

* **Einfachste Begriffe zuerst**: Wenn Sie mehrere mit `and` verbundene Klauseln haben, stellen Sie die Klauseln, die nur eine Spalte umfassen, an den Anfang. `Timestamp > ago(1d) and OpId == EventId` ist also besser als anders herum.

Weitere Informationen finden Sie in der Zusammenfassung der [verfügbaren String-Operatoren](./datatypes-string-operators.md) und in der Zusammenfassung der [verfügbaren numerischen Operatoren](./numoperators.md).

**Beispiel**

```kusto
Traces
| where Timestamp > ago(1h)
    and Source == "MyCluster"
    and ActivityId == SubActivityId 
```

Datensätze, die nicht älter als 1 Stunde sind und aus der Quelle mit dem Namen "MyCluster" stammen und zwei Spalten mit demselben Wert aufweisen. 

Beachten Sie, dass wir den Vergleich zwischen zwei Spalten an das Ende stellen, da der Index nicht genutzt werden kann und ein Scan erzwungen wird.

**Beispiel**

```kusto
Traces | where * has "Kusto"
```

Alle Zeilen, in denen das Wort "Kusto" in einer beliebigen Spalte angezeigt wird.