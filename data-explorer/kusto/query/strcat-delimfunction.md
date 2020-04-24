---
title: strcat_delim() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden strcat_delim() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f944af741cd5f655c2c9b090ddebc6cc35a47766
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506935"
---
# <a name="strcat_delim"></a>strcat_delim()

Verkettet zwischen 2 und 64 Argumente mit Trennzeichen, die als erstes Argument angeführt werden.

 * Wenn Argumente nicht vom Zeichenfolgentyp sind, werden sie zwangsweise in Zeichenfolgen konvertiert.

**Syntax**

`strcat_delim(`*Trennzeichen*,*Argument1*,*Argument2* [, *argumentN*]`)`

**Argumente**

* *Trennzeichen*: Zeichenfolgenausdruck, der als Trennzeichen verwendet wird.
* *Argument1* ... *argumentN* : Ausdrücke, die verkettet werden sollen.

**Rückgabe**

Argumente, verkettet auf eine einzelne Zeichenfolge mit *Trennzeichen*.

**Beispiele**

```kusto
print st = strcat_delim('-', 1, '2', 'A', 1s)

```

|st|
|---|
|1-2-A-00:00:01|