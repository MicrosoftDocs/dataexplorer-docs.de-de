---
title: parse_urlquery ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird parse_urlquery () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b1092edfaca8c6789ec6c0dc478fb27505531b1e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244701"
---
# <a name="parse_urlquery"></a>parse_urlquery()

Gibt ein-Objekt zurück, `dynamic` das die Abfrage Parameter enthält.

## <a name="syntax"></a>Syntax

`parse_urlquery(`*Such*`)`

## <a name="arguments"></a>Argumente

* *Query*: eine Zeichenfolge stellt eine URL-Abfrage dar.

## <a name="returns"></a>Rückgabe

Ein Objekt vom Typ " [Dynamic](./scalar-data-types/dynamic.md) ", das die Abfrage Parameter enthält.

## <a name="example"></a>Beispiel

```kusto
parse_urlquery("k1=v1&k2=v2&k3=v3")
```

Ergebnis:

```kusto
 {
    "Query Parameters":"{"k1":"v1", "k2":"v2", "k3":"v3"}",
 }
```

**Notizen**

* Das Eingabeformat muss den URL-Abfrage Standards folgen (Key = Value&...).
 