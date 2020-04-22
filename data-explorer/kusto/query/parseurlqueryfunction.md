---
title: parse_urlquery() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird parse_urlquery() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3f00fefcd6245528d7ae50d6046d97289a92317d
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744619"
---
# <a name="parse_urlquery"></a>parse_urlquery()

Gibt `dynamic` ein Objekt zurück, das die Abfrageparameter enthält.

**Syntax**

`parse_urlquery(`*Abfrage*`)`

**Argumente**

* *abfrage*: Eine Zeichenfolge stellt eine URL-Abfrage dar.

**Rückgabe**

Ein Objekt vom Typ ["Dynamik",](./scalar-data-types/dynamic.md) das die Abfrageparameter enthält.

**Beispiel**

```kusto
parse_urlquery("k1=v1&k2=v2&k3=v3")
```

ergibt sich:

```kusto
 {
    "Query Parameters":"{"k1":"v1", "k2":"v2", "k3":"v3"}",
 }
```

**Hinweise**

* Das Eingabeformat sollte den URL-Abfragestandards folgen (key=value& ...)
 