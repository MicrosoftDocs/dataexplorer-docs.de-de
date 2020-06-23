---
title: DateTime ()-Azure-Daten-Explorer
description: In diesem Artikel wird "DateTime ()" in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f5c108b670534728f34db8975f16d713848dd8f4
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264607"
---
# <a name="todatetime"></a>todatetime()

Konvertiert die Eingabe in einen [DateTime](./scalar-data-types/datetime.md) -skalaren Wert.

```kusto
todatetime("2015-12-24") == datetime(2015-12-24)
```

**Syntax**

`todatetime(`*Expr*`)`

**Argumente**

* *Expr*: Ausdruck, der in [DateTime](./scalar-data-types/datetime.md)konvertiert wird.

**Rückgabe**

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis ein [DateTime](./scalar-data-types/datetime.md) -Wert.
Andernfalls ist das Ergebnis NULL.
 
> [!NOTE]
> Verwenden Sie nach Möglichkeit die Verwendung von [DateTime ()](./scalar-data-types/datetime.md) .
