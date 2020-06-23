---
title: strcat_delim ()-Azure Daten-Explorer
description: In diesem Artikel wird strcat_delim () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f6a78a5abb92aa93fe8b1ae15ea8968f71bde07c
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264554"
---
# <a name="strcat_delim"></a>strcat_delim()

Verkettet zwischen zwei und 64 Argumenten mit Trennzeichen, die als erstes Argument bereitgestellt werden.

 * Wenn Argumente nicht vom Typ "String" sind, werden Sie zwangsweise in eine Zeichenfolge konvertiert.

**Syntax**

`strcat_delim(`*Trennzeichen*, *Argument1*, *Argument2*[, *argumentn*]`)`

**Argumente**

* *Trennzeichen*: Zeichen folgen Ausdruck, der als Trennzeichen verwendet wird.
* *Argument1* ... *argumentn*: Ausdrücke, die verkettet werden sollen.

**Rückgabe**

Argumente, die zu einer einzelnen Zeichenfolge mit einem *Trenn*Zeichen verkettet sind.

**Beispiele**

```kusto
print st = strcat_delim('-', 1, '2', 'A', 1s)

```

|st|
|---|
|1-2-A-00:00:01|
