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
ms.openlocfilehash: e2914e913402de7442d2533cf5159c2bd30fac60
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346302"
---
# <a name="parse_path"></a>parse_path()

Analysiert einen Dateipfad `string` und gibt ein- [`dynamic`](./scalar-data-types/dynamic.md) Objekt zurück, das die folgenden Teile des Pfads enthält:
* Schema
* RootPath
* DirectoryPath
* DirectoryName
* FileName
* Durchwahl
* Alternativen datastreamname

Zusätzlich zu den einfachen Pfaden mit beiden Arten von Schrägstrichen unterstützt die-Funktion Pfade mit:
* Schemas: Beispiel: "file://..."
* Freigegebene Pfade. Beispiel: " \\ shareddrive\users..."
* Lange Pfade. Beispiel: " \\ ? \c:..."
* Alternative Datenströme Beispiel: "file1.exe:file2.exe"

## <a name="syntax"></a>Syntax

`parse_path(`*path*`)`

## <a name="arguments"></a>Argumente

* *path*: eine Zeichenfolge, die einen Dateipfad darstellt.

## <a name="returns"></a>Rückgabe

Ein Objekt vom Typ `dynamic` , das die oben aufgeführten Pfad Komponenten enthielt.

## <a name="example"></a>Beispiel

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
|C:\temp\file.txt|{"Schema": "", "RootPath": "c:", "directerypath": "c: \\ Temp", "directeryname": "Temp", "Dateiname": "file.txt", "Extension": "txt", "alternativen datastreamname": ""}
|temp\file.txt|{"Schema": "", "RootPath": "", "directerypath": "Temp", "directeryname": "Temp", "Dateiname": "file.txt", "Extension": "txt", "alternativen datastreamname": ""}
|file://C:/Temp/file.txt:some.exe|{"Schema": "file", "RootPath": "c:", "directerypath": "c:/Temp", "directeryname": "Temp", "filename": "file.txt", "Extension": "txt", "alternativen datastreamname": "some.exe"}
|\\shared\users\temp\file.txt. gz|{"Schema": "", "RootPath": "", "directoriypath": " \\ \\ Shared \\ Users \\ Temp "," directeryname ":" Temp "," Dateiname ":" file.txt. gz "," Extension ":" gz "," alternativen datastreamname ":" "}
|/usr/lib/Temp/file.txt|{"Schema": "", "RootPath": "", "directerypath": "/usr/lib/Temp", "directeryname": "Temp", "Dateiname": "file.txt", "Extension": "txt", "alternativen datastreamname": ""}
