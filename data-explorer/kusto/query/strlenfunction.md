---
title: strinlen ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird das Thema in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4256218669685ec12a939156021803105e5ddfc6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248413"
---
# <a name="strlen"></a>strlen()

Gibt die Länge der Eingabe Zeichenfolge in Zeichen zurück.

## <a name="syntax"></a>Syntax

`strlen(`*Ausgangs*`)`

## <a name="arguments"></a>Argumente

* *Source*: die Quell Zeichenfolge, die für die Zeichen folgen Länge gemessen wird.

## <a name="returns"></a>Rückgabe

Gibt die Länge der Eingabe Zeichenfolge in Zeichen zurück.

**Notizen**

Jedes Unicode-Zeichen in der Zeichenfolge ist gleich `1` , einschließlich Surrogates.
(Beispiel: chinesische Zeichen werden einmal gezählt, trotz der Tatsache, dass mehr als ein Wert in der UTF-8-Codierung erforderlich ist).


## <a name="examples"></a>Beispiele

```kusto
print length = strlen("hello")
```

|length|
|---|
|5|

```kusto
print length = strlen("⒦⒰⒮⒯⒪")
```

|length|
|---|
|5|