---
title: Der bool-Datentyp - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt den bool-Datentyp in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: dd9cfa4f97f3baa4353f967f5e1ca31186f4815f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509927"
---
# <a name="the-bool-data-type"></a>Der bool-Datentyp

Der `bool` `boolean`Datentyp ( ) kann einen `true` `false` von zwei Zuständen `1` `0`haben: bzw. (intern als bzw. ) sowie den NULL-Wert.

## <a name="bool-literals"></a>bool literale

Der `bool` Datentyp hat die folgenden Literale:
* `true`und `bool(true)`: Darstellung der Wahrhaftigkeit
* `false`und `bool(false)`: Die Falschheit darstellen
* `bool(null)`: Siehe [NULL-Werte](null-values.md)

## <a name="bool-operators"></a>bool-Operatoren

Der `bool` Datentyp unterstützt die folgenden`==`Operatoren:`!=`Gleichheit (`and`), Ungleichheit (`or`), logisch und ( ), und logisch-oder ( ).