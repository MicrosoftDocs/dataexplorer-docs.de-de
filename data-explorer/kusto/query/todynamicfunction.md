---
title: todynamic(), toobject() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt todynamic(), toobject() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 138b0f978df699817c5dc5c14bafc4c06a95afc7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506153"
---
# <a name="todynamic-toobject"></a>todynamic(), toobject()

Interpretiert einen `string` als [JSON-Wert](https://json.org/) und [`dynamic`](./scalar-data-types/dynamic.md)gibt den Wert als zurück. 

Es ist der Verwendung der [extractjson()-Funktion](./extractjsonfunction.md) überlegen, wenn Sie mehr als ein Element eines JSON-Verbundobjekts extrahieren müssen.

Aliase in [parse_json()](./parsejsonfunction.md) Funktion.

**Syntax**

`todynamic(`*json*`)`
`toobject(`*json*`)`

**Argumente**

* *json*: Ein JSON-Dokument.

**Rückgabe**

Ein Objekt vom Typ `dynamic` , angegeben durch *json*.

*Hinweis*: Bevorzugen Sie die Verwendung von [dynamic()](./scalar-data-types/dynamic.md) wenn möglich.