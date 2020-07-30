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
ms.openlocfilehash: eddf893d3c0d8096f57a3f624ddedf753fe35f91
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346353"
---
# <a name="parse_csv"></a>parse_csv()

Teilt eine angegebene Zeichenfolge, die einen einzelnen Datensatz mit durch Trennzeichen getrennten Werten darstellt, und gibt ein Zeichen folgen Array mit diesen Werten zurück.

```kusto
parse_csv("aaa,bbb,ccc") == ["aaa","bbb","ccc"]
```

## <a name="syntax"></a>Syntax

`parse_csv(`*Ausgangs*`)`

## <a name="arguments"></a>Argumente

* *Source*: die Quell Zeichenfolge, die einen einzelnen Datensatz mit durch Trennzeichen getrennten Werten darstellt.

## <a name="returns"></a>Rückgabe

Ein Zeichen folgen Array, das die geteilten Werte enthält.

**Hinweise**

Eingebettete Zeilen Feeds, Kommas und Anführungszeichen können mit einem doppelten Anführungszeichen ("" ") mit Escapezeichen versehen werden. Diese Funktion unterstützt nicht mehrere Datensätze pro Zeile (nur der erste Datensatz wird erstellt).

## <a name="examples"></a>Beispiele

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
