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
ms.openlocfilehash: f34f2ac316f122034fcf8ee19a8e82e51b6221df
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780472"
---
# <a name="pack_all"></a>pack_all()

Erstellt ein- `dynamic` Objekt (Eigenschaften Behälter) aus allen Spalten des tabellarischen Ausdrucks.

**Syntax**

`pack_all()`

**Notizen**

Die Darstellung des zurückgegebenen Objekts ist nicht garantiert, dass es zwischen den Ausführungen mit Byte-Ebene kompatibel ist. Beispielsweise können Eigenschaften, die im Behälter angezeigt werden, in einer anderen Reihenfolge angezeigt werden.

**Beispiele**

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
