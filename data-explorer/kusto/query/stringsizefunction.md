---
title: string_size ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird string_size () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4832d00703ab9e6d1478af5b3f45ec294dfb79ce
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350909"
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