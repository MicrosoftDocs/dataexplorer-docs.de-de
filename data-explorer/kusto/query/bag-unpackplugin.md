---
title: 'bag_unpack-Plug-in: Azure Daten-Explorer'
description: In diesem Artikel wird bag_unpack-Plug-in in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: fd8b0968d90f1c5239cae80c3be9c2a32d0603d6
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225495"
---
# <a name="bag_unpack-plugin"></a>Plug-in bag_unpack

Das `bag_unpack` Plug-in entpackt eine einzelne Spalte vom Typ, `dynamic` indem die einzelnen Eigenschaften Behälter auf oberster Ebene als Spalte behandelt werden.

    T | evaluate bag_unpack(col1)

**Syntax**

*T* - `|` `evaluate` `bag_unpack(` *Spalte* `,` [ *outputcolumnprefix* ]`)`

**Argumente**

* *T*: die tabellarische Eingabe, deren Spalten *Spalte* entpackt werden soll.
* *Column*: die Spalte von *T* , die entpackt werden soll. Der Wert muss vom Typ `dynamic` sein.
* *Outputcolumnprefix*: ein gängiges Präfix, das allen Spalten hinzugefügt wird, die vom Plug-in erzeugt werden.
  Optional.

**Rückgabe**

Das `bag_unpack` Plug-in gibt eine Tabelle mit so vielen Datensätzen zurück wie die Tabellen Eingabe (*T*). Das Schema der Tabelle ist mit den Tabellen Eingaben identisch und weist die folgenden Änderungen auf:

* Die angegebene Eingabe Spalte (*Spalte*) wird entfernt.

* Das Schema wird mit so vielen Spalten erweitert, wie es unterschiedliche Slots in den Eigenschaften Behälter Werten der obersten Ebene von *T*gibt. Der Name jeder Spalte entspricht dem Namen der einzelnen Slots. optional wird das *Präfix outputcolumnprefix*vorangestellt. Der Typ ist entweder der Typ des Slots (wenn alle Werte desselben Slots denselben Typ aufweisen) oder `dynamic` (wenn sich die Werte im Typ unterscheiden).

**Hinweise**

Das Ausgabe Schema des Plug-ins hängt von den Datenwerten ab und macht es als "unvorhersehbare" Daten selbst. Daher können mehrere Ausführungen des Plug-ins mit unterschiedlichen Dateneingaben zu unterschiedlichen Ausgabe Schemas führen.

Die Eingabedaten für das Plug-in müssen so lauten, dass das Ausgabe Schema allen Regeln für ein tabellarisches Schema entspricht. Dies gilt insbesondere für:

1. Der Name einer Ausgabe Spalte darf nicht mit einer vorhandenen Spalte in der tabellarischen Eingabe *T* identisch sein, es sei denn, es handelt sich um die Spalte, für die das Entpacken (*Spalte*) selbst ausgeführt werden soll, da dadurch zwei Spalten mit demselben Namen erstellt werden.

2. Alle slotnamen, bei vorangestellten *outputcolumnprefix*, müssen gültige Entitäts Namen sein und den [bezeichnerbenennungs Regeln](./schema-entities/entity-names.md#identifier-naming-rules)entsprechen.

**Beispiel**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable(d:dynamic)
[
    dynamic({"Name": "John", "Age":20}),
    dynamic({"Name": "Dave", "Age":40}),
    dynamic({"Name": "Smitha", "Age":30}),
]
| evaluate bag_unpack(d)
```

|name  |Age|
|------|---|
|John  |20 |
|David  |40 |
|Smitha|30 |
