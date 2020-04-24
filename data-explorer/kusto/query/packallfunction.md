---
title: pack_all() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird pack_all() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3c6a22b656e28b8b7113864e0b3f9636a4fb364d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511933"
---
# <a name="pack_all"></a>pack_all()

Erstellt `dynamic` ein Objekt (Eigenschaftsbeutel) aus allen Spalten des Tabellarischen Ausdrucks.

**Syntax**

`pack_all()`

**Beispiele**

Geben einer Tabelle SmsMessages 

|SourceNumber |TargetNumber| CharsCount
|---|---|---
|555-555-1234 |555-555-1212 | 46 
|555-555-1234 |555-555-1213 | 50 
|555-555-1212 |555-555-1234 | 32 

Diese Abfrage
```kusto
SmsMessages | extend Packed=pack_all()
``` 

Rückgabewerte:

|TableName |SourceNumber |TargetNumber | Verpackt
|---|---|---|---
|SmsMessages|555-555-1234 |555-555-1212 | "SourceNumber":"555-555-1234", "TargetNumber":"555-555-1212", "CharsCount": 46.
|SmsMessages|555-555-1234 |555-555-1213 | "SourceNumber":"555-555-1234", "TargetNumber":"555-555-1213", "CharsCount": 50.
|SmsMessages|555-555-1212 |555-555-1234 | "SourceNumber":"555-555-1212", "TargetNumber":"555-555-1234", "CharsCount": 32.