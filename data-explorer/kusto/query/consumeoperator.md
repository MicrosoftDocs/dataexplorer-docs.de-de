---
title: 'Operator verwenden: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt den nutzungsoperator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 1e4b2ff4ee45fd92e7e16f9468c18e1d0a70ca82
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245166"
---
# <a name="consume-operator"></a>consume-Operator

Verwendet die Tabular Data Stream, die an den Operator übergeben wird. 

Der `consume` -Operator wird größtenteils zum Auslösen des Abfrage neben Effekts verwendet, ohne die Ergebnisse an den Aufrufer zurückzugeben.

```kusto
T | consume
```

## <a name="syntax"></a>Syntax

`consume` [ `decodeblocks` `=` *Decodeblocks*]

## <a name="arguments"></a>Argumente

* *Decodeblocks*: ein konstanter boolescher Wert. Wenn die Eigenschaft auf festgelegt `true` ist, oder wenn die Anforderungs Eigenschaft `perftrace` auf festgelegt ist `true` , `consume` listet der Operator nicht nur die Datensätze bei der Eingabe auf, sondern erzwingt tatsächlich, dass jeder Wert in diesen Datensätzen dekomprimiert und decodiert wird.

Der- `consume` Operator kann verwendet werden, um die Kosten einer Abfrage zu schätzen, ohne dass die Ergebnisse an den Client zurückgeliefert werden.
(Die Schätzung ist aus verschiedenen Gründen nicht exakt; Sie wird z. b. `consume` Verteil Bar berechnet, sodass `T | consume` die Daten der Tabelle nicht zwischen den Knoten des Clusters übertragen werden.)

<!--
* *WithStats*: A constant Boolean value. If set to `true` (or if the global
  property `perftrace` is set), the operator will return a single
  row with a single column called `Stats` of type `dynamic` holding the statistics
  of the data source fed to the `consume` operator.
-->