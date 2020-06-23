---
title: 'bag_unpack-Plug-in: Azure Daten-Explorer'
description: In diesem Artikel wird bag_unpack-Plug-in in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/15/2020
ms.openlocfilehash: 45dc0a02aae7cc39c7a287036055e9ca447187f3
ms.sourcegitcommit: 085e212fe9d497ee6f9f477dd0d5077f7a3e492e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2020
ms.locfileid: "85133464"
---
# <a name="bag_unpack-plugin"></a>Plug-in bag_unpack

Das `bag_unpack` Plug-in entpackt eine einzelne Spalte vom Typ `dynamic` , indem die einzelnen Eigenschaften Behälter auf oberster Ebene als Spalte behandelt werden.

    T | evaluate bag_unpack(col1)

**Syntax**

*T* - `|` `evaluate` `bag_unpack(` *Spalte* [ `,` *outputcolumnprefix* ] [ `,` *columnsconflict* ] [ `,` *ignoredproperties* ]`)`

**Argumente**

* *T*: die tabellarische Eingabe, deren Spalten *Spalte* entpackt werden soll.
* *Column*: die Spalte von *T* , die entpackt werden soll. Der Wert muss vom Typ `dynamic` sein.
* *Outputcolumnprefix*: ein gängiges Präfix, das allen Spalten hinzugefügt wird, die vom Plug-in erzeugt werden. Dieses Argument ist optional.
* *columnsconflict*: eine Richtung für die Auflösung von Spalten Konflikten. Dieses Argument ist optional. Wenn Argument angegeben wird, wird erwartet, dass es sich um ein Zeichenfolgenliteral handelt, das einem der folgenden Werte entspricht:
    - `error`-Die Abfrage erzeugt einen Fehler (Standard).
    - `replace_source`-Quell Spalte wird ersetzt
    - `keep_source`-Die Quell Spalte wird beibehalten.
* *ignoredproperties*: Optionaler Satz von Behälter Eigenschaften, die ignoriert werden sollen. Wenn Argument angegeben wird, wird davon ausgegangen, dass es sich um eine Konstante des `dynamic` Arrays mit mindestens einem Zeichenfolgenliteralen handelt.

**Rückgabe**

Das `bag_unpack` Plug-in gibt eine Tabelle mit so vielen Datensätzen zurück wie die Tabellen Eingabe (*T*). Das Schema der Tabelle ist identisch mit dem Schema der Tabellen Eingabe mit den folgenden Änderungen:

* Die angegebene Eingabe Spalte (*Spalte*) wird entfernt.
* Das Schema wird mit so vielen Spalten erweitert, wie es unterschiedliche Slots in den Eigenschaften Behälter Werten der obersten Ebene von *T*gibt. Der Name jeder Spalte entspricht dem Namen der einzelnen Slots. optional wird das *Präfix outputcolumnprefix*vorangestellt. Der Typ ist entweder der Typ des Slots, wenn alle Werte desselben Slots denselben Typ aufweisen oder `dynamic` , wenn sich die Werte im Typ unterscheiden.

**Notizen**

Das Ausgabe Schema des Plug-ins hängt von den Datenwerten ab und macht es als "unvorhersehbare" Daten selbst. Mehrere Ausführungen des Plug-ins mit unterschiedlichen Dateneingaben können ein anderes Ausgabe Schema ergeben.

Die Eingabedaten für das Plug-in müssen so lauten, dass das Ausgabe Schema allen Regeln für ein tabellarisches Schema folgt. Dies gilt insbesondere für:

* Der Name einer Ausgabe Spalte darf nicht mit einer vorhandenen Spalte in der tabellarischen Eingabe *t*identisch sein, es sei denn, es handelt sich um die Spalte, deren Verpackung entpackt werden soll (*Spalte*), da dadurch zwei Spalten mit demselben Namen erstellt werden.

* Alle slotnamen, bei vorangestellten *outputcolumnprefix*, müssen gültige Entitäts Namen sein und den [bezeichnerbenennungs Regeln](./schema-entities/entity-names.md#identifier-naming-rules)entsprechen.

## <a name="examples"></a>Beispiele

### <a name="expand-a-bag"></a>Einen Behälter erweitern


<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(d:dynamic)
[
    dynamic({"Name": "John", "Age":20}),
    dynamic({"Name": "Dave", "Age":40}),
    dynamic({"Name": "Jasmine", "Age":30}),
]
| evaluate bag_unpack(d)
```

|Name  |Age|
|------|---|
|John  |20 |
|David  |40 |
|Jasmine|30 |


### <a name="expand-a-bag-with-outputcolumnprefix"></a>Erweitern eines Behälters mit outputcolumnprefix

Erweitern Sie einen Behälter, und verwenden `OutputColumnPrefix` Sie die Option, um Spaltennamen zu verwenden, die mit dem Präfix ' Property_ ' beginnen.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(d:dynamic)
[
    dynamic({"Name": "John", "Age":20}),
    dynamic({"Name": "Dave", "Age":40}),
    dynamic({"Name": "Jasmine", "Age":30}),
]
| evaluate bag_unpack(d, 'Property_')
```

|Property_Name|Property_Age|
|---|---|
|John|20|
|David|40|
|Jasmine|30|

### <a name="expand-a-bag-with-columnsconflict"></a>Erweitern eines Behälters mit columnsconflict

Erweitern Sie einen Behälter, und verwenden `columnsConflict` Sie die Option, um Konflikte zwischen vorhandenen Spalten und Spalten aufzulösen, die vom-Operator erzeugt werden `bag_unpack()` .

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(Name:string, d:dynamic)
[
    'Old_name', dynamic({"Name": "John", "Age":20}),
    'Old_name', dynamic({"Name": "Dave", "Age":40}),
    'Old_name', dynamic({"Name": "Jasmine", "Age":30}),
]
| evaluate bag_unpack(d, columnsConflict='replace_source') // Use new name
```

|Name|Age|
|---|---|
|John|20|
|David|40|
|Jasmine|30|

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(Name:string, d:dynamic)
[
    'Old_name', dynamic({"Name": "John", "Age":20}),
    'Old_name', dynamic({"Name": "Dave", "Age":40}),
    'Old_name', dynamic({"Name": "Jasmine", "Age":30}),
]
| evaluate bag_unpack(d, columnsConflict='keep_source') // Keep old name
```

|Name|Age|
|---|---|
|Old_name|20|
|Old_name|40|
|Old_name|30|

### <a name="expand-a-bag-with-ignoredproperties"></a>Einen Behälter mit ignoredproperties erweitern

Erweitern Sie einen Behälter, und verwenden `ignoredProperties` Sie die Option, um bestimmte Eigenschaften im Eigenschaften Behälter zu ignorieren.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(d:dynamic)
[
    dynamic({"Name": "John", "Age":20, "Address": "Address-1" }),
    dynamic({"Name": "Dave", "Age":40, "Address": "Address-2"}),
    dynamic({"Name": "Jasmine", "Age":30, "Address": "Address-3"}),
]
// Ignore 'Age' and 'Address' properties
| evaluate bag_unpack(d, ignoredProperties=dynamic(['Address', 'Age']))
```

|Name|
|---|
|John|
|David|
|Jasmine|
