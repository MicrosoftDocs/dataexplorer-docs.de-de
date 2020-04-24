---
title: strlen() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt strlen() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 33bb1ea56ebc03dc7357264bcdf987c191478cbb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506867"
---
# <a name="strlen"></a>strlen()

Gibt die Länge der Eingabezeichenfolge in Zeichen zurück.

**Syntax**

`strlen(`*Quelle*`)`

**Argumente**

* *source*: Die Quellzeichenfolge, die für die Zeichenfolgenlänge gemessen wird.

**Rückgabe**

Gibt die Länge der Eingabezeichenfolge in Zeichen zurück.

**Hinweise**

Jedes Unicode-Zeichen in der `1`Zeichenfolge ist gleich , einschließlich Ersatzzeichen.
(z. B.: Chinesische Zeichen werden einmal gezählt, obwohl es mehr als einen Wert in der UTF-8-Codierung erfordert).


**Beispiele**

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