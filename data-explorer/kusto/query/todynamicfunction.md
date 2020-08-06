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
ms.openlocfilehash: acd1b5328150e61bc81930f94b8ea9e8025e1ebb
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/05/2020
ms.locfileid: "87804082"
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

## <a name="returns"></a>Gibt zurück

Ein Objekt vom Typ `dynamic` , angegeben durch *json*.
