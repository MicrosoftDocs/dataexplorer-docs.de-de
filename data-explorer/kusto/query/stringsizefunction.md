---
title: string_size ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird string_size () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ea06e7e41ebe48e09839109745f3fe7ba5a96e7a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251479"
---
# <a name="string_size"></a>string_size()

Gibt die Größe der Eingabe Zeichenfolge in Bytes zurück.

## <a name="syntax"></a>Syntax

`string_size(`*Ausgangs*`)`

## <a name="arguments"></a>Argumente

* *Source*: die Quell Zeichenfolge, die für die Zeichen folgen Größe gemessen wird.

## <a name="returns"></a>Rückgabe

Gibt die Länge der Eingabe Zeichenfolge in Bytes zurück.

## <a name="examples"></a>Beispiele

```kusto
print size = string_size("hello")
```

|size|
|---|
|5|

```kusto
print size = string_size("⒦⒰⒮⒯⒪")
```

|size|
|---|
|15|