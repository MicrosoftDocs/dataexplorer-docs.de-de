---
title: strcat_delim ()-Azure Daten-Explorer
description: In diesem Artikel wird strcat_delim () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: de34f4a2f6efb3af84b61f53da8d58cb60dea7ae
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243709"
---
# <a name="strcat_delim"></a>strcat_delim()

Verkettet zwischen zwei und 64 Argumenten mit Trennzeichen, die als erstes Argument bereitgestellt werden.

 * Wenn Argumente nicht vom Typ "String" sind, werden Sie zwangsweise in eine Zeichenfolge konvertiert.

## <a name="syntax"></a>Syntax

`strcat_delim(`*Trennzeichen*, *Argument1*, *Argument2*[, *argumentn*]`)`

## <a name="arguments"></a>Argumente

* *Trennzeichen*: Zeichen folgen Ausdruck, der als Trennzeichen verwendet wird.
* *Argument1* ... *argumentn*: Ausdrücke, die verkettet werden sollen.

## <a name="returns"></a>Rückgabe

Argumente, die zu einer einzelnen Zeichenfolge mit einem *Trenn*Zeichen verkettet sind.

## <a name="examples"></a>Beispiele

```kusto
print st = strcat_delim('-', 1, '2', 'A', 1s)

```

|st|
|---|
|1-2-A-00:00:01|
