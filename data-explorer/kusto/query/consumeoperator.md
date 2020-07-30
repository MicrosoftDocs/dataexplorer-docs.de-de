---
title: 'Operator verwenden: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt den nutzungsoperator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 85fd891590e359e31224ed5d707a837b1cc0eb41
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348835"
---
# <a name="consume-operator"></a>consume-Operator

Verwendet die Tabular Data Stream, die an den Operator übergeben wird. 

Der `consume` -Operator wird größtenteils zum Auslösen des Abfrage neben Effekts verwendet, ohne die Ergebnisse an den Aufrufer zurückzugeben.

```kusto
T | consume
```

## <a name="syntax"></a>Syntax

`consume`[ `decodeblocks` `=` *Decodeblocks*]

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