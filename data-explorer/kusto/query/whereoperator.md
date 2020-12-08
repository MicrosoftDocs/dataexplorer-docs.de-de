---
title: 'where-Operator in der Kusto-Abfragesprache: Azure Data Explorer'
description: In diesem Artikel wird der where-Operator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 6ac800cd4b38396e0f32f44976c4594c093747bb
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512008"
---
# <a name="where-operator"></a>where-Operator

Filtert eine Tabelle auf die Teilmenge der Zeilen, die ein Prädikat erfüllen.

```kusto
T | where fruit=="apple"
```

**Alias** `filter`

## <a name="syntax"></a>Syntax

*T* `| where` *Prädikat*

## <a name="arguments"></a>Argumente

* *T*: Die tabellarische Eingabe, deren Datensätze gefiltert werden sollen.
* *Prädikat*: Ein `boolean` [Ausdruck](./scalar-data-types/bool.md) über die Spalten von *T*. Er wird für jede Zeile in *T* ausgewertet.

## <a name="returns"></a>Gibt zurück

Zeilen in *T*, für die *Predicate* auf `true` festgelegt ist.

**Hinweise** NULL-Werte: Alle Filterfunktionen geben beim Vergleich mit NULL-Werten FALSE zurück. Sie können spezielle NULL-fähige Funktionen verwenden, um Abfragen zu schreiben, die NULL-Werte verarbeiten können.

[isnull()](./isnullfunction.md), [isnotnull()](./isnotnullfunction.md), [isempty()](./isemptyfunction.md), [isnotempty()](./isnotemptyfunction.md). 

**Tipps**

So erzielen Sie die optimale Leistung:

* **Verwenden Sie einfache Vergleiche** zwischen dem Spaltennamen und Konstanten. („Konstant“ bedeutet konstant innerhalb der Tabelle, d. h. `now()` und `ago()` sind ebenso zulässig wie Skalarwerte, die mithilfe einer [`let`-Anweisung](./letstatement.md) zugewiesen werden.)

    Beispielsweise wird `where Timestamp >= ago(1d)` gegenüber `where floor(Timestamp, 1d) == ago(1d)` bevorzugt.

* **Einfachste Begriffe zuerst**: Wenn Sie mehrere mit `and` verbundene Klauseln haben, stellen Sie die Klauseln, die nur eine Spalte umfassen, an den Anfang. `Timestamp > ago(1d) and OpId == EventId` ist also besser als anders herum.

Weitere Informationen finden Sie in der Zusammenfassung der [verfügbaren Zeichenfolgenoperatoren](./datatypes-string-operators.md) und in der Zusammenfassung der [verfügbaren numerischen Operatoren](./numoperators.md).

## <a name="example-simple-comparisons-first"></a>Beispiel: Einfache Vergleiche zuerst

```kusto
Traces
| where Timestamp > ago(1h)
    and Source == "MyCluster"
    and ActivityId == SubActivityId 
```

Dieses Beispiel ruft Datensätze ab, die nicht älter als 1 Stunde sind, aus der Quelle namens `MyCluster` stammen und zwei Spalten mit dem gleichen Wert aufweisen. 

Beachten Sie, dass wir den Vergleich zwischen zwei Spalten an das Ende stellen, da der Index nicht verwendet werden kann und ein Scan erzwungen wird.

## <a name="example-columns-contain-string"></a>Beispiel: Spalten enthalten Zeichenfolge

```kusto
Traces | where * has "Kusto"
```

Alle Zeilen, in denen das Wort „Kusto“ in einer beliebigen Spalte vorkommt.
 
