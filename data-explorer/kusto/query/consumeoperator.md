---
title: Verwenden des Operators - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der Verbrauchsoperator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 65c2f2befc074042131b5c0d705fa942a1622035
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517118"
---
# <a name="consume-operator"></a>consume-Operator

Verbraucht den tabellarischen Datenstrom, der dem Operator übergeben wird. 

Der `consume` Operator wird hauptsächlich zum Auslösen des Abfrage-Nebeneffekts verwendet, ohne die Ergebnisse tatsächlich an den Aufrufer zurückzugeben.

```kusto
T | consume
```

**Syntax**

`consume`[`decodeblocks` `=` *DecodeBlocks*]

**Argumente**

* *DecodeBlocks*: Ein konstanter boolescher Wert. Wenn auf `true`festgelegt ist oder `perftrace` die `true`Request-Eigenschaft auf festgelegt ist, führt der `consume` Operator nicht nur die Datensätze an seiner Eingabe auf, sondern erzwingt tatsächlich, dass jeder Wert in diesen Datensätzen dekomprimiert und dekodiert wird.

Der `consume` Operator kann verwendet werden, um die Kosten einer Abfrage abzuschätzen, ohne die Ergebnisse tatsächlich an den Client zurückzusenden.
(Die Schätzung ist aus einer Vielzahl von `consume` Gründen nicht genau; z. B. wird verteilungsfähig berechnet, so `T | consume` dass die Daten der Tabelle nicht zwischen den Knoten des Clusters übertragen werden.)

<!--
* *WithStats*: A constant Boolean value. If set to `true` (or if the global
  property `perftrace` is set), the operator will return a single
  row with a single column called `Stats` of type `dynamic` holding the statistics
  of the data source fed to the `consume` operator.
-->