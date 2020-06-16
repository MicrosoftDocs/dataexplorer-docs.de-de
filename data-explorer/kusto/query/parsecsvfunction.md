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
ms.openlocfilehash: b61934ec2efbfb22c17fe93a4f3969a1592cefab
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780659"
---
# <a name="parse_csv"></a>parse_csv()

Teilt eine angegebene Zeichenfolge, die einen einzelnen Datensatz mit durch Trennzeichen getrennten Werten darstellt, und gibt ein Zeichen folgen Array mit diesen Werten zurück.

```kusto
parse_csv("aaa,bbb,ccc") == ["aaa","bbb","ccc"]
```

**Syntax**

`parse_csv(`*Ausgangs*`)`

**Argumente**

* *Source*: die Quell Zeichenfolge, die einen einzelnen Datensatz mit durch Trennzeichen getrennten Werten darstellt.

**Rückgabe**

Ein Zeichen folgen Array, das die geteilten Werte enthält.

**Notizen**

Eingebettete Zeilen Feeds, Kommas und Anführungszeichen können mit einem doppelten Anführungszeichen ("" ") mit Escapezeichen versehen werden. Diese Funktion unterstützt nicht mehrere Datensätze pro Zeile (nur der erste Datensatz wird erstellt).

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
