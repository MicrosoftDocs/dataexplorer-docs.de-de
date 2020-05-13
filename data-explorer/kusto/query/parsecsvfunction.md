---
title: parse_csv ()-Azure Daten-Explorer
description: In diesem Artikel wird parse_csv () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f01faae3d9339aa23e7e2bb2b1fdae7a652db360
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271212"
---
# <a name="parse_csv"></a>parse_csv()

Teilt eine angegebene Zeichenfolge, die einen einzelnen Datensatz von Komma getrennten Werten darstellt, und gibt ein Zeichen folgen Array mit diesen Werten zurück.

```kusto
parse_csv("aaa,bbb,ccc") == ["aaa","bbb","ccc"]
```

**Syntax**

`parse_csv(`*Ausgangs*`)`

**Argumente**

* *Source*: die Quell Zeichenfolge, die einen einzelnen Datensatz von Komma getrennten Werten darstellt.

**Rückgabe**

Ein Zeichen folgen Array, das die geteilten Werte enthält.

**Hinweise**

Eingebettete Zeilen Feeds, Kommas und Anführungszeichen können mit einem doppelten Anführungszeichen ("" ") mit Escapezeichen versehen werden. Diese Funktion unterstützt nicht mehrere Datensätze pro Zeile (es wird nur der erste Datensatz erstellt).

**Beispiele**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print result=parse_csv('aa,"b,b,b",cc,"Escaping quotes: ""Title""","line1\nline2"')
```

|result|
|---|
|[<br>  "AA",<br>  "b, b, b",<br>  "CC",<br>  "Escapezeichen: \" Titel \" ",<br>  "line1\nline2"<br>]|

CSV-Nutzlast mit mehreren Datensätzen:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print result_multi_record=parse_csv('record1,a,b,c\nrecord2,x,y,z')
```

|result_multi_record|
|---|
|[<br>  "Record1",<br>  "a",<br>  "b",<br>  "c"<br>]|
