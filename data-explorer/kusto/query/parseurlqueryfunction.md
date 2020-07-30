---
title: parse_urlquery ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird parse_urlquery () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6d34ece3a945485b8a809089d030fa954b070a28
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346268"
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

**Hinweise**

* Das Eingabeformat muss den URL-Abfrage Standards folgen (Key = Value&...).
 