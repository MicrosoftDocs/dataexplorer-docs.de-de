---
title: parse_path ()-Azure Daten-Explorer
description: In diesem Artikel wird parse_path () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 16b80c86f526cb05514577359603e9e21de80064
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83224883"
---
# <a name="parse_path"></a>parse_path()

Analysiert einen Dateipfad `string` und gibt ein- [`dynamic`](./scalar-data-types/dynamic.md) Objekt zurück, das die folgenden Teile des Pfads enthält: Scheme, RootPath, DirectoryPath, DirectoryName, Dateiname, Extension, Alternativen datastreamname.
Zusätzlich zu den einfachen Pfaden mit beiden Arten von Schrägstrichen unterstützt Pfade mit Schemas (z. b. "file://..."), freigegebene Pfade (z. b. " \\ shareddrive\users..."), langen Pfaden (z. b. " \\ ? \c:..." "), Alternative Datenströme (z. b." file1. exe: file2. exe ")

**Syntax**

`parse_path(`*ADS*`)`

**Argumente**

* *path*: eine Zeichenfolge, die einen Dateipfad darstellt.

**Rückgabe**

Ein Objekt vom Typ `dynamic` , das die oben aufgeführten Pfad Komponenten enthielt.

**Beispiel**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(p:string) 
[
    @"C:\temp\file.txt",
    @"temp\file.txt",
    "file://C:/temp/file.txt:some.exe",
    @"\\shared\users\temp\file.txt.gz",
    "/usr/lib/temp/file.txt"
]
| extend path_parts = parse_path(p)

```

|p|path_parts
|---|---
|C:\temp\File.txt|{"Schema": "", "RootPath": "c:", "directerypath": "c: \\ Temp", "directeryname": "Temp", "Dateiname": "file. txt", "Extension": "txt", "alternativen datastreamname": ""}
|temp\file.txt|{"Schema": "", "RootPath": "", "directerypath": "Temp", "directeryname": "Temp", "Dateiname": "file. txt", "Extension": "txt", "alternativen datastreamname": ""}
|file://C:/temp/file.txt:some.exe|{"Schema": "file", "RootPath": "c:", "directerypath": "c:/Temp", "directeryname": "Temp", "Dateiname": "file. txt", "Extension": "txt", "alternativen datastreamname": "some. exe"}
|\\shared\users\temp\file.txt.gz|{"Schema": "", "RootPath": "", "directoriypath": " \\ \\ Shared \\ Users \\ Temp "," directeryname ":" Temp "," Dateiname ":" file. txt. gz "," Extension ":" gz "," alternativen datastreamname ":" "}
|/usr/lib/temp/file.txt|{"Schema": "", "RootPath": "", "directerypath": "/usr/lib/Temp", "directeryname": "Temp", "Dateiname": "file. txt", "Extension": "txt", "alternativen datastreamname": ""}
