---
title: Beispieloperator - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der Beispieloperator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: 757830bde0c56ac727d5240c01ca4768eab28877
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510029"
---
# <a name="sample-operator"></a>sample-Operator

Gibt bis zur angegebenen Anzahl zufälliger Zeilen aus der Eingabetabelle zurück.

```kusto
T | sample 5
```

**Syntax**

_t_ `| sample` _NumberOfRows_

**Argumente**

- _NumberOfRows_: Die Anzahl der zurückzugebenden Zeilen von _T._ Sie können einen beliebigen numerischen Ausdruck angeben.

**Hinweise**

- `sample`ist eher auf Geschwindigkeit als auf gleichmäßige Verteilung von Werten ausgerichtet. Insbesondere bedeutet dies, dass es keine "fairen" Ergebnisse hervorbringt, wenn es nach `union` `join` Betreibern verwendet wird, die 2 Datensätze unterschiedlicher Größe (z. B. a oder Operatoren) verwenden. Es wird empfohlen, `sample` direkt nach dem Tabellenverweis und den Filtern zu verwenden.

- `sample`ist ein nicht deterministischer Operator und gibt jedes Mal, wenn er während der Abfrage ausgewertet wird, eine andere Ergebnismenge zurück. Die folgende Abfrage ergibt z. B. zwei verschiedene Zeilen (auch wenn man erwarten würde, dieselbe Zeile zweimal zurückzugeben).

```kusto
let _data = range x from 1 to 100 step 1;
let _sample = _data | sample 1;
union (_sample), (_sample)
```

| x   |
| --- |
| 83  |
| 3   |

Um sicherzustellen, dass im `_sample` Obigen Beispiel einmal berechnet wird, kann man die [materialize()-Funktion](./materializefunction.md) verwenden:

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

- Wenn Sie einen bestimmten Prozentsatz Ihrer Daten (anstelle einer angegebenen Anzahl von Zeilen)

```kusto
StormEvents | where rand() < 0.1
```

- Wenn Sie Tasten anstelle von Zeilen testen möchten (z. B. 10-IDs- [`sample-distinct`](./sampledistinctoperator.md) und alle `in` Zeilen für diese IDs abrufen), können Sie sie in Kombination mit dem Operator verwenden.

**Beispiele**

```kusto
let sampleEpisodes = StormEvents | sample-distinct 10 of EpisodeId;
StormEvents
| where EpisodeId in (sampleEpisodes)
```