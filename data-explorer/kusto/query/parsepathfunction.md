---
title: parse_path() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird parse_path() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 2267efb4e47a6d8e45733ad48dd9f7f4019c1fa8
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744660"
---
# <a name="parse_path"></a>parse_path()

Analysiert einen Dateipfad `string` und [`dynamic`](./scalar-data-types/dynamic.md) gibt ein Objekt zurück, das die folgenden Teile des Pfads enthält: Scheme, RootPath, DirectoryPath, DirectoryName, FileName, Extension, AlternateDataStreamName.
Unterstützt neben den einfachen Pfaden mit beiden Schrägstrichtypen Pfade mit Schemas (z.B. "file://..."),\\freigegebene Pfade (z.B. "shareddrive-users..."), lange Pfade (z.B. "?-C:...""),\\alternative Datenströme (z.B. "file1.exe:file2.exe")

**Syntax**

`parse_path(`*Pfad*`)`

**Argumente**

* *path*: Eine Zeichenfolge, die einen Dateipfad darstellt.

**Rückgabe**

Ein Objekt `dynamic` vom Typ, das die Pfadkomponenten wie oben aufgeführt eingeschrieben hat.

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
|C:-temp-Datei.txt|'Scheme":"","RootPath":"C:","DirectoryPath":"C:\\temp","DirectoryName":"temp","Filename":"file.txt","Extension":"txt","AlternateDataStreamName":""
|Temp-Datei.txt|'Scheme":"","RootPath":"","DirectoryPath":"temp","DirectoryName":"temp","Filename":"file.txt","Extension":"txt","AlternateDataStreamName":""
|file://C:/temp/file.txt:some.exe|'Scheme":"file","RootPath":"C:","DirectoryPath":"C:/temp","DirectoryName":"temp","Filename":"file.txt","Extension":"txt","AlternateDataStreamName":"some.exe"
|\\Shared-Benutzer-Temp-Datei.txt.gz|\\\\"Scheme":"","RootPath":"","DirectoryPath":" shared\\\\users temp","DirectoryName":"temp","Filename":"file.txt.gz","Extension":"gz","AlternateDataStreamName":""
|/usr/lib/temp/file.txt|"Scheme":"","RootPath":"","DirectoryPath":"/usr/lib/temp","DirectoryName":"temp","Filename":"file.txt","Extension":"txt","AlternateDataStreamName":""
