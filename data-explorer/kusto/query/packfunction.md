---
title: pack() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt Pack() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a7b43be96f272ab929434f10cac910bd4072e650
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511831"
---
# <a name="pack"></a>pack()

Erstellt `dynamic` ein Objekt (Eigenschaftsbeutel) aus einer Liste von Namen und Werten.

Alias `pack_dictionary()` zu funktionieren.

**Syntax**

`pack(`*schlüssel1* `,` *wert1* `,` *schlüssel2 wert2* `,` *value2*`,... )`

**Argumente**

* Eine abwechselnde Liste von Schlüsseln und Werten (die Gesamtlänge der Liste muss gerade sein)
* Alle Schlüssel müssen nicht leere konstante Zeichenfolgen sein.

**Beispiele**

Das folgende Beispiel gibt `{"Level":"Information","ProcessID":1234,"Data":{"url":"www.bing.com"}}`zurück:

```kusto
pack("Level", "Information", "ProcessID", 1234, "Data", pack("url", "www.bing.com"))
```

Nehmen Sie 2 Tabellen, SmsMessages und MmsMessages:

Tabelle SmsMessages 

|SourceNumber |TargetNumber| CharsCount
|---|---|---
|555-555-1234 |555-555-1212 | 46 
|555-555-1234 |555-555-1213 | 50 
|555-555-1212 |555-555-1234 | 32 

Tabelle MmsMessages 

|SourceNumber |TargetNumber| AttachmnetSize | AttachmnetType | AttachmnetName
|---|---|---|---|---
|555-555-1212 |555-555-1213 | 200 | JPEG | Bild1
|555-555-1234 |555-555-1212 | 250 | JPEG | Pic2
|555-555-1234 |555-555-1213 | 300 | png | Pic3

Diese Abfrage
```kusto
SmsMessages 
| extend Packed=pack("CharsCount", CharsCount) 
| union withsource=TableName kind=inner 
( MmsMessages 
  | extend Packed=pack("AttachmnetSize", AttachmnetSize, "AttachmnetType", AttachmnetType, "AttachmnetName", AttachmnetName))
| where SourceNumber == "555-555-1234"
``` 

Rückgabewerte:

|TableName |SourceNumber |TargetNumber | Verpackt
|---|---|---|---
|SmsMessages|555-555-1234 |555-555-1212 | "CharsCount": 46
|SmsMessages|555-555-1234 |555-555-1213 | "CharsCount": 50
|MmsMessages|555-555-1234 |555-555-1212 | "AttachmnetSize": 250, "AttachmnetType": "jpg", "AttachmnetName": "Pic2""
|MmsMessages|555-555-1234 |555-555-1213 | "AttachmnetSize": 300, "AttachmnetType": "png", "AttachmnetName": "Pic3""