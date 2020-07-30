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
ms.openlocfilehash: 2568196dc20042e95521ed0818bd625f3394599b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87342562"
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
