---
title: 'Where-Operator: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird beschrieben, wo der Operator (has, enthält, StartWith, EndsWith, Regex) in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fadf8aa8c21dac364793c73a38e68d55fc2a6f6d
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83370368"
---
# <a name="where-operator"></a>where-Operator

Filtert eine Tabelle auf die Teilmenge der Zeilen, die ein Prädikat erfüllen.

```kusto
T | where fruit=="apple"
```

**Alias**`filter`

**Syntax**

*T* - `| where` *Prädikat*

**Argumente**

* *T*: die tabellarische Eingabe, deren Datensätze gefiltert werden sollen.
* *Predicate*: ein- `boolean` [Ausdruck](./scalar-data-types/bool.md) für die Spalten von *T*. Sie wird für jede Zeile in *T*ausgewertet.

**Rückgabe**

Zeilen in *T*, für die *Predicate* auf `true` festgelegt ist.

**Hinweise** NULL-Werte: alle Filterfunktionen geben false zurück, wenn Sie mit NULL-Werten verglichen werden. Sie können spezielle NULL-fähige Funktionen verwenden, um Abfragen zu schreiben, die NULL-Werte in das Konto aufnehmen: [IsNull ()](./isnullfunction.md), [IsNotNull ()](./isnotnullfunction.md), [IsEmpty ()](./isemptyfunction.md), [isnotempty ()](./isnotemptyfunction.md). 

**Tipps**

So erzielen Sie die optimale Leistung:

* **Verwenden Sie einfache Vergleiche** zwischen dem Spaltennamen und Konstanten. ("Constant" bedeutet Konstante über der Tabelle. daher `now()` sind und in `ago()` Ordnung, und es handelt sich um skalare Werte, die mit einer- [ `let` Anweisung](./letstatement.md)zugewiesen werden.)

    Beispielsweise wird `where Timestamp >= ago(1d)` gegenüber `where floor(Timestamp, 1d) == ago(1d)` bevorzugt.

* **Einfachste Begriffe zuerst**: Wenn Sie mehrere mit `and` verbundene Klauseln haben, stellen Sie die Klauseln, die nur eine Spalte umfassen, an den Anfang. `Timestamp > ago(1d) and OpId == EventId` ist also besser als anders herum.

Weitere Informationen finden Sie in der Zusammenfassung der [verfügbaren Zeichen folgen Operatoren](./datatypes-string-operators.md) und der Zusammenfassung der [verfügbaren numerischen Operatoren](./numoperators.md).

**Beispiel**

```kusto
Traces
| where Timestamp > ago(1h)
    and Source == "MyCluster"
    and ActivityId == SubActivityId 
```

Datensätze, die nicht älter als 1 Stunde sind und aus der Quelle mit dem Namen "mycluster" stammen und zwei Spalten desselben Werts haben. 

Beachten Sie, dass wir den Vergleich zwischen zwei Spalten an das Ende stellen, da der Index nicht genutzt werden kann und ein Scan erzwungen wird.

**Beispiel**

```kusto
Traces | where * has "Kusto"
```

Alle Zeilen, in denen das Wort "Kusto" in einer beliebigen Spalte angezeigt wird.
