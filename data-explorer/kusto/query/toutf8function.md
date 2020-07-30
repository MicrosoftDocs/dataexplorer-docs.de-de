---
title: to_utf8 ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird to_utf8 () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 891a2bb079136d9a7c21c1992b79e3e0eab4c970
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350671"
---
# <a name="to_utf8"></a>to_utf8()

Gibt ein dynamisches Array von Unicode-Zeichen einer Eingabe Zeichenfolge zurück (umgekehrter Vorgang make_string).

## <a name="syntax"></a>Syntax

`to_utf8(`*Ausgangs*`)`

## <a name="arguments"></a>Argumente

* *Quelle*: die zu konvertierende Quell Zeichenfolge.

## <a name="returns"></a>Rückgabe

Gibt ein dynamisches Array von Unicode-Zeichen zurück, die die für diese Funktion bereitgestellte Zeichenfolge bilden.
Siehe [`make_string()`](makestringfunction.md) )

## <a name="examples"></a>Beispiele

```kusto
print arr = to_utf8("⒦⒰⒮⒯⒪")
```

|r|
|---|
|[9382, 9392, 9390, 9391, 9386]|

```kusto
print arr = to_utf8("קוסטו - Kusto")
```

|r|
|---|
|[1511, 1493, 1505, 1496, 1493, 32, 45, 32, 75, 117, 115, 116, 111]|

```kusto
print str = make_string(to_utf8("Kusto"))
```

|str|
|---|
|Kusto|
