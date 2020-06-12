---
title: Pack ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: Dieser Artikel beschreibt Pack () in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a94629ae8f4795e28cbfb0c41f06596731cdd8d9
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717324"
---
# <a name="pack"></a>pack()

Erstellt ein `dynamic` -Objekt (Eigenschaften Behälter) aus einer Liste von Namen und Werten.

Alias für die `pack_dictionary()` Funktion.

**Syntax**

`pack(`*key1* `,` *value1* `,` *key2* `,` *value2*`,... )`

**Argumente**

* Eine abwechselnde Liste von Schlüsseln und Werten (die Gesamtlänge der Liste muss gleich sein).
* Alle Schlüssel müssen nicht leere Konstante Zeichen folgen sein.

**Beispiele**

Das folgende Beispiel gibt `{"Level":"Information","ProcessID":1234,"Data":{"url":"www.bing.com"}}`zurück:

```kusto
pack("Level", "Information", "ProcessID", 1234, "Data", pack("url", "www.bing.com"))
```

Es sind 2 Tabellen, smsmess Ages und mmsmess erforderlich:

Tabellen-smsmess 

|Sourcennummer |Targetnumber| Charscount
|---|---|---
|555-555-1234 |555-555-1212 | 46 
|555-555-1234 |555-555-1213 | 50 
|555-555-1212 |555-555-1234 | 32 

Tabellen-mmsmess 

|Sourcennummer |Targetnumber| Attachmentsize | AttachmentType | AttachmentName
|---|---|---|---|---
|555-555-1212 |555-555-1213 | 200 | jpeg | Pic1
|555-555-1234 |555-555-1212 | 250 | jpeg | Pic2
|555-555-1234 |555-555-1213 | 300 | png | Pic3

Diese Abfrage
```kusto
SmsMessages 
| extend Packed=pack("CharsCount", CharsCount) 
| union withsource=TableName kind=inner 
( MmsMessages 
  | extend Packed=pack("AttachmentSize", AttachmentSize, "AttachmentType", AttachmentType, "AttachmentName", AttachmentName))
| where SourceNumber == "555-555-1234"
``` 

Rückgabewerte:

|TableName |Sourcennummer |Targetnumber | Stop
|---|---|---|---
|Smsmess|555-555-1234 |555-555-1212 | {"Charscount": 46}
|Smsmess|555-555-1234 |555-555-1213 | {"Charscount": 50}
|Mmsmess Ages|555-555-1234 |555-555-1212 | {"Attachmentsize": 250, "AttachmentType": "JPEG", "attachmentName": "pic2"}
|Mmsmess Ages|555-555-1234 |555-555-1213 | {"Attachmentsize": 300, "AttachmentType": "PNG", "attachmentName": "pic3"}
