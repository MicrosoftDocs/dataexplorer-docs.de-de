---
title: to_utf8 ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird to_utf8 () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c47c37dbbde7bd2276f1b5788dc6eb7b062f39a3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251260"
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
