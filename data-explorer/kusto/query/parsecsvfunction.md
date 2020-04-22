---
title: parse_csv() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird parse_csv() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6645fd0dcc7062a4afb60fb34ade1c519af27294
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663668"
---
# <a name="parse_csv"></a>parse_csv()

Teilt eine bestimmte Zeichenfolge, die einen einzelnen Datensatz mit durch Kommas getrennten Werten darstellt, und gibt ein Zeichenfolgenarray mit diesen Werten zurück.

```kusto
parse_csv("aaa,bbb,ccc") == ["aaa","bbb","ccc"]
```

**Syntax**

`parse_csv(`*Quelle*`)`

**Argumente**

* *source*: Die Quellzeichenfolge, die einen einzelnen Datensatz mit durch Kommas getrennten Werten darstellt.

**Rückgabe**

Ein Zeichenfolgenarray, das die geteilten Werte enthält.

**Hinweise**

Eingebettete Zeilenvorschübe, Kommas und Anführungszeichen können mithilfe des doppelten Anführungszeichens (""') mit Escapezeichen versehen werden. Diese Funktion unterstützt nicht mehrere Datensätze pro Zeile (nur der erste Datensatz wird aufgenommen).

**Beispiele**

```kusto
print result=parse_csv('aa,"b,b,b",cc,"Escaping quotes: ""Title""","line1\nline2"')
```

|result|
|---|
|[<br>  "aa",<br>  "b,b,b",<br>  "cc",<br>  "Escaping \"quotes:\"Title ",<br>  "zeile1-nline2"<br>]|

CSV-Nutzlast mit mehreren Datensätzen:

```kusto
print result_multi_record=parse_csv('record1,a,b,c\nrecord2,x,y,z')
```

|result_multi_record|
|---|
|[<br>  "record1",<br>  "a",<br>  "b",<br>  "c"<br>]|