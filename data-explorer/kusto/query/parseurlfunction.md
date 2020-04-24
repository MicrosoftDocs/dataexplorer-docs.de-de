---
title: parse_url() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird parse_url() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: dfc093964ce5b91acc01f798f8f62651266ab153
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511491"
---
# <a name="parse_url"></a>parse_url()

Analysiert eine absolute `string` URL `dynamic` und gibt ein Objekt zurück, das URL-Teile enthält.


**Syntax**

`parse_url(`*Url*`)`

**Argumente**

* *url*: Eine Zeichenfolge stellt eine URL oder den Abfrageteil der URL dar.

**Rückgabe**

Ein Objekt vom Typ ["Dynamik",](./scalar-data-types/dynamic.md) das die URL-Komponenten schema, Host, Port, Pfad, Benutzername, Kennwort, Abfrageparameter, Fragment enthielt.

**Beispiel**

```kusto
T | extend Result = parse_url("scheme://username:password@host:1234/this/is/a/path?k1=v1&k2=v2#fragment")
```

wird die Folge haben

```
 {
    "Scheme":"scheme",
    "Host":"host",
    "Port":"1234",
    "Path":"this/is/a/path",
    "Username":"username",
    "Password":"password",
    "Query Parameters":"{"k1":"v1", "k2":"v2"}",
    "Fragment":"fragment"
 }
```