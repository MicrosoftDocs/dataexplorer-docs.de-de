---
title: strrep() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt strrep() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 39b398e8fadb400c25cfeb97487c2ecf0669ad83
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506714"
---
# <a name="strrep"></a>strrep()

Wiederholt die angegebene [Zeichenfolge,](./scalar-data-types/string.md) die die Anzahl der Zeiten angegeben hat.

* Wenn das erste oder dritte Argument nicht vom Zeichenfolgentyp ist, wird es zwangsweise in string konvertiert.

**Syntax**

`strrep(`*Wert*,*Multiplikator*,[*Trennzeichen*]`)`

**Argumente**

* *Wert*: Eingabeausdruck
* *Multiplikator*: positiver Ganzzahlwert (von 1 bis 1024)
* *Trennzeichen*: ein optionaler Zeichenfolgenausdruck (Standard: leere Zeichenfolge)

**Rückgabe**

Wert, der für eine bestimmte Anzahl von Malen wiederholt wird, verkettet mit *Trennzeichen*.

Wenn der *Multiplikator* mehr als den maximal zulässigen Wert (1024) ist, wird die Eingabezeichenfolge 1024 Mal wiederholt.
 
**Beispiel**

```kusto
print from_str = strrep('ABC', 2), from_int = strrep(123,3,'.'), from_time = strrep(3s,2,' ')
```

|from_str|from_int|from_time|
|---|---|---|
|ABCABC|123.123.123|00:00:03 00:00:03|