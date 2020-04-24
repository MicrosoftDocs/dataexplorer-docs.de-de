---
title: sqrt() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt sqrt() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 660235a60893732288a551e1febd9b7b044b4f00
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507258"
---
# <a name="sqrt"></a>sqrt()

Gibt die Quadratwurzelfunktion zurück.  

**Syntax**

`sqrt(`*X*`)`

**Argumente**

* *x*: Eine reelle Zahl >= 0.

**Rückgabe**

* Eine positive Zahl, sodass Folgendes gilt: `sqrt(x) * sqrt(x) == x`
* `null`, wenn das Argument negativ ist oder nicht in einen `real`-Wert konvertiert werden kann. 