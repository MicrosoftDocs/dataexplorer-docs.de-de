---
title: to_utf8() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird to_utf8() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9d48ed99e517e0b1e5d498e80deaa48dc1cd3601
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505830"
---
# <a name="to_utf8"></a>to_utf8()

Gibt ein dynamisches Array der Unicode-Zeichen einer Eingabezeichenfolge zurück (die inverse Operation von make_string).

**Syntax**

`to_utf8(`*Quelle*`)`

**Argumente**

* *quelle*: Die zu konvertierende Quellzeichenfolge.

**Rückgabe**

Gibt ein dynamisches Array der Unicodezeichen zurück, aus denen die Zeichenfolge besteht, die für diese Funktion bereitgestellt wird.
Siehe [`make_string()`](makestringfunction.md))

**Beispiele**

```kusto
print arr = to_utf8("⒦⒰⒮⒯⒪")
```

|Arr|
|---|
|[9382, 9392, 9390, 9391, 9386]|

```kusto
print arr = to_utf8("קוסטו - Kusto")
```

|Arr|
|---|
|[1511, 1493, 1505, 1496, 1493, 32, 45, 32, 75, 117, 115, 116, 111]|

```kusto
print str = make_string(to_utf8("Kusto"))
```

|str|
|---|
|Kusto|
