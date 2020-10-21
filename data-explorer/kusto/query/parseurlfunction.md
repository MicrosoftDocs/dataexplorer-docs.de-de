---
title: parse_url ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird parse_url () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0ce68151270369bd7dc0898b98029bacddf0c243
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248630"
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