---
title: parse_url ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird parse_url () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 94a35dbf742b6df31012e68b5f2b2f09bec9b7e5
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346285"
---
# <a name="parse_url"></a>parse_url()

Analysiert ein absolute URL `string` und gibt ein-Objekt zurück, das `dynamic` URL-Teile enthält.


## <a name="syntax"></a>Syntax

`parse_url(`*Urne*`)`

## <a name="arguments"></a>Argumente

* *URL*: eine Zeichenfolge stellt eine URL oder den Abfrage Teil der URL dar.

## <a name="returns"></a>Rückgabe

Ein Objekt vom Typ " [Dynamic](./scalar-data-types/dynamic.md) ", das die URL-Komponenten enthielt: Schema, Host, Port, Pfad, Benutzername, Kennwort, Abfrage Parameter, Fragment.

## <a name="example"></a>Beispiel

```kusto
T | extend Result = parse_url("scheme://username:password@host:1234/this/is/a/path?k1=v1&k2=v2#fragment")
```

Ergebnis

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