---
title: 'Beispiel Operator: Azure Daten-Explorer'
description: In diesem Artikel wird der Beispiel Operator in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: b5d0624504744bb28dfdb68ee27c48b2119242b8
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351504"
---
# <a name="sample-operator"></a>sample-Operator

Gibt bis zur angegebenen Anzahl zufälliger Zeilen aus der Eingabe Tabelle zurück.

```kusto
T | sample 5
```

## <a name="syntax"></a>Syntax

_T_ - `| sample` _numofrows_

## <a name="arguments"></a>Argumente

- _Nummeriofrows_: die _Anzahl von Zeilen, die_ zurückgegeben werden sollen. Sie können einen beliebigen numerischen Ausdruck angeben.

**Notizen**

- `sample`ist auf eine Geschwindigkeit und nicht auf eine gleichmäßige Verteilung von Werten ausgelegt. Insbesondere bedeutet dies, dass keine "Fair" Ergebnisse erzeugt werden, wenn Sie nach Operatoren verwendet werden, die Union 2-Datensätze mit unterschiedlichen Größen (z. b. ein- `union` oder- `join` Operator) verwenden. Es wird empfohlen, `sample` direkt hinter der Tabellen Referenz und den Filtern zu verwenden.

- `sample`bei handelt es sich um einen nicht deterministischen Operator, der jedes Mal ein anderes Resultset zurückgibt, wenn er während der Abfrage ausgewertet wird. Beispielsweise ergibt die folgende Abfrage zwei verschiedene Zeilen (auch wenn davon ausgegangen wird, dass die gleiche Zeile zweimal zurückgegeben wird).

```kusto
let _data = range x from 1 to 100 step 1;
let _sample = _data | sample 1;
union (_sample), (_sample)
```

| x   |
| --- |
| 83  |
| 3   |

Um sicherzustellen, dass das obige Beispiel `_sample` einmal berechnet wird, kann eine [Materialize ()](./materializefunction.md) -Funktion verwendet werden:

```kusto
let _data = range x from 1 to 100 step 1;
let _sample = materialize(_data | sample 1);
union (_sample), (_sample)
```

| x   |
| --- |
| 34  |
| 34  |

**Tipps**

- Wenn Sie einen bestimmten Prozentsatz der Daten (anstatt eine angegebene Anzahl von Zeilen) als Stichprobe verwenden möchten, können Sie

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | where rand() < 0.1
```

- Wenn Sie anstelle von Zeilen Stichproben (z. b. Beispiel 10-IDs und alle Zeilen für diese IDs) verwenden möchten, können Sie [`sample-distinct`](./sampledistinctoperator.md) in Kombination mit dem- `in` Operator verwenden.

## <a name="examples"></a>Beispiele

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let sampleEpisodes = StormEvents | sample-distinct 10 of EpisodeId;
StormEvents
| where EpisodeId in (sampleEpisodes)
```
