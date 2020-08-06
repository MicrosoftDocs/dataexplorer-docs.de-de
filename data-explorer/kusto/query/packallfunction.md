---
title: pack_all ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird pack_all () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0c4ce80c00df4c9c6e257fbe1f8a93802f54438e
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/05/2020
ms.locfileid: "87802943"
---
# <a name="pack_all"></a>pack_all()

Erstellt ein- `dynamic` Objekt (Eigenschaften Behälter) aus allen Spalten des tabellarischen Ausdrucks.

> [!NOTE]
> Die Darstellung des zurückgegebenen Objekts ist nicht garantiert, dass es zwischen den Ausführungen mit Byte-Ebene kompatibel ist. Beispielsweise können Eigenschaften, die im Behälter angezeigt werden, in einer anderen Reihenfolge angezeigt werden.

## <a name="syntax"></a>Syntax

`pack_all()`

## <a name="examples"></a>Beispiele

Bei einer Tabelle mit smsmess 

|Sourcennummer |Targetnumber| Charscount
|---|---|---
|555-555-1234 |555-555-1212 | 46 
|555-555-1234 |555-555-1213 | 50 
|555-555-1212 |555-555-1234 | 32 

Diese Abfrage

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(SourceNumber:string,TargetNumber:string,CharsCount:long)
[
'555-555-1234','555-555-1212',46,
'555-555-1234','555-555-1213',50,
'555-555-1212','555-555-1234',32
]
| extend Packed=pack_all()
```

Rückgabewerte:

|TableName |Sourcennummer |Targetnumber | Stop
|---|---|---|---
|Smsmess|555-555-1234 |555-555-1212 | {"Sourcenenumber": "555-555-1234", "targetnumber": "555-555-1212", "charscount": 46}
|Smsmess|555-555-1234 |555-555-1213 | {"Sourcenenumber": "555-555-1234", "targetnumber": "555-555-1213", "charscount": 50}
|Smsmess|555-555-1212 |555-555-1234 | {"Sourcenenumber": "555-555-1212", "targetnumber": "555-555-1234", "charscount": 32}
