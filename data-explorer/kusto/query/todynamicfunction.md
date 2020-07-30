---
title: "\"$ Dynamic ()\", \"jeobject ()\": Azure Daten-Explorer | Microsoft-Dokumentation"
description: In diesem Artikel wird "jedynamic ()", "jeobject ()" in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: e11a4d450275fb4d596bd9618c20ef6cefcb0531
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350729"
---
# <a name="todynamic-toobject"></a>todynamic(), toobject()

Interpretiert einen `string` als [JSON-Wert](https://json.org/) und gibt den Wert als zurück [`dynamic`](./scalar-data-types/dynamic.md) . 

Es ist besser, die [extractjson ()-Funktion](./extractjsonfunction.md) zu verwenden, wenn Sie mehr als ein Element eines JSON-Verbund Objekts extrahieren müssen.

Aliase zu [parse_json ()](./parsejsonfunction.md) -Funktion.

## <a name="syntax"></a>Syntax

`todynamic(`*json* `)` 
 JSON `toobject(` *JSON*`)`

## <a name="arguments"></a>Argumente

* *JSON*: ein JSON-Dokument.

## <a name="returns"></a>Rückgabe

Ein Objekt vom Typ `dynamic` , angegeben durch *json*.

*Hinweis*: Verwenden Sie nach Möglichkeit die Verwendung von [Dynamic ()](./scalar-data-types/dynamic.md) .