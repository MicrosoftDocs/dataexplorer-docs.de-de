---
title: bag_unpack-Plugin - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird bag_unpack-Plugin in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: 374ff3ca8101e24a53926a78a1b8781447f32dbc
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518223"
---
# <a name="bag_unpack-plugin"></a>bag_unpack Plugin

Das `bag_unpack` Plugin packt eine `dynamic` einzelne Spalte des Typs aus, indem es jeden Slot der obersten Ebene der Eigenschaftentasche als Spalte behandelt.

    T | evaluate bag_unpack(col1)

**Syntax**

*T* `|` `evaluate` `bag_unpack(` *Column* T-Spalte `,` [ *OutputColumnPrefix* ]`)`

**Argumente**

* *T*: Die tabellarische Eingabe, deren *Spalte* entpackt werden soll.
* *Spalte*: Die Spalte *von T* zum Auspacken. Der Wert muss vom Typ `dynamic` sein.
* *OutputColumnPrefix*: Ein gemeinsames Präfix, das allen Spalten hinzugefügt werden soll, die vom Plugin erstellt werden.
  Optional.

**Rückgabe**

Das `bag_unpack` Plugin gibt eine Tabelle mit so vielen Datensätzen zurück wie seine tabellarische Eingabe (*T*). Das Schema der Tabelle entspricht dem der tabellarischen Eingabe mit den folgenden Änderungen:

* Die angegebene Eingabespalte (*Spalte*) wird entfernt.

* Das Schema wird um so viele Spalten erweitert, wie es unterschiedliche Steckplätze in den Eigenschaftsbeutelwerten der obersten Ebene von *T*gibt. Der Name jeder Spalte entspricht dem Namen jedes Steckplatzes, dem optional *OutputColumnPrefix*vorangestellt ist. Sein Typ ist entweder der Typ des Steckplatzes (wenn alle `dynamic` Werte desselben Steckplatzes denselben Typ haben), oder (wenn sich die Werte im Typ unterscheiden).

**Hinweise**

Das Ausgabeschema des Plugins hängt von den Datenwerten ab, was es so "unvorhersehbar" macht wie die Daten selbst. Daher können mehrere Ausführungen des Plugins mit unterschiedlicher Dateneingabe unterschiedliche Ausgabeschemas erzeugen.

Die Eingabedaten für das Plugin müssen so sein, dass das Ausgabeschema allen Regeln für ein tabellarisches Schema entspricht. Dies gilt insbesondere für:

1. Ein Ausgabespaltenname kann nicht mit einer vorhandenen Spalte in der Tabellarischen Eingabe *T* identisch sein, es sei denn, es handelt sich um die Spalte, die entpackt werden soll (*Spalte*) selbst, da dadurch zwei Spalten mit demselben Namen entstehen.

2. Alle Slotnamen müssen, wenn von *OutputColumnPrefix*vorangestellt, gültige Entitätsnamen sein und den [Idbezeichnerbenennungsregeln](./schema-entities/entity-names.md#identifier-naming-rules)entsprechen.

**Beispiel**

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