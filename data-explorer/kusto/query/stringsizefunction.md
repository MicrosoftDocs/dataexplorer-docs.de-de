---
title: string_size() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden string_size() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7886e7b8fd5039c9b197ae435bce4f40b93e5038
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506884"
---
# <a name="string_size"></a>string_size()

Gibt die Größe der Eingabezeichenfolge in Bytes zurück.

**Syntax**

`string_size(`*Quelle*`)`

**Argumente**

* *quelle*: Die Quellzeichenfolge, die für die Zeichenfolgengröße gemessen wird.

**Rückgabe**

Gibt die Länge der Eingabezeichenfolge in Bytes zurück.

**Beispiele**

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