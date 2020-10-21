---
title: "\"$ Dynamic ()\", \"jeobject ()\": Azure Daten-Explorer | Microsoft-Dokumentation"
description: In diesem Artikel wird "jedynamic ()", "jeobject ()" in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: c92ff96b3ae6a0369de1c8fa715e64499fb051b7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250486"
---
# <a name="todynamic-toobject"></a>todynamic(), toobject()

Interpretiert einen `string` als [JSON-Wert](https://json.org/) und gibt den Wert als zurück [`dynamic`](./scalar-data-types/dynamic.md) . 

Es ist besser, die [extractjson ()-Funktion](./extractjsonfunction.md) zu verwenden, wenn Sie mehr als ein Element eines JSON-Verbund Objekts extrahieren müssen.

Aliase zu [parse_json ()](./parsejsonfunction.md) -Funktion.

> [!NOTE]
> Verwenden Sie nach Möglichkeit die Verwendung von [Dynamic ()](./scalar-data-types/dynamic.md) .

## <a name="syntax"></a>Syntax

`todynamic(`*json* `)` 
 JSON `toobject(` *JSON*`)`

## <a name="arguments"></a>Argumente

* *JSON*: ein JSON-Dokument.

## <a name="returns"></a>Rückgabe

Ein Objekt vom Typ `dynamic` , angegeben durch *json*.
