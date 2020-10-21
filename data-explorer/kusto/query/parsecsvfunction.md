---
title: parse_csv ()-Azure Daten-Explorer
description: In diesem Artikel wird parse_csv () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1b0f1a0279dcb35e4e196b0f2170f4a64d58eca9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246121"
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

**Notizen**

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
