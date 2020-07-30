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
ms.openlocfilehash: 6c157c014ec3b83aa39d4bdfcadda12e97e84f3e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346540"
---
# <a name="pack_all"></a>pack_all()

Erstellt ein- `dynamic` Objekt (Eigenschaften Behälter) aus allen Spalten des tabellarischen Ausdrucks.

## <a name="syntax"></a>Syntax

`pack_all()`

**Hinweise**

Die Darstellung des zurückgegebenen Objekts ist nicht garantiert, dass es zwischen den Ausführungen mit Byte-Ebene kompatibel ist. Beispielsweise können Eigenschaften, die im Behälter angezeigt werden, in einer anderen Reihenfolge angezeigt werden.

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
