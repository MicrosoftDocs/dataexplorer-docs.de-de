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
ms.openlocfilehash: 4b2a9d100cdb7125ebed21be69433fa3cee3a929
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324532"
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
