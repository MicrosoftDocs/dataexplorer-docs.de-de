---
title: strinlen ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird das Thema in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2d28eae6852faedf2c6071164d8f80f9c3567602
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350892"
---
# <a name="strlen"></a>strlen()

Gibt die Länge der Eingabe Zeichenfolge in Zeichen zurück.

## <a name="syntax"></a>Syntax

`strlen(`*Ausgangs*`)`

## <a name="arguments"></a>Argumente

* *Source*: die Quell Zeichenfolge, die für die Zeichen folgen Länge gemessen wird.

## <a name="returns"></a>Rückgabe

Gibt die Länge der Eingabe Zeichenfolge in Zeichen zurück.

**Hinweise**

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