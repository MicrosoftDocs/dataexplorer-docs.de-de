---
title: parse_ipv4_mask ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird parse_ipv4_mask ()-Funktion in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: fd6bd97befc376d13573a0a85169524cf01b9d2d
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294609"
---
# <a name="parse_ipv4_mask"></a>parse_ipv4_mask()

Konvertiert die Eingabe Zeichenfolge von IPv4 und Netzmaske in eine lange Zahlen Darstellung (64-Bit signiert).

```kusto
parse_ipv4_mask("127.0.0.1", 24) == 2130706432
parse_ipv4_mask('192.1.168.2', 31) == parse_ipv4_mask('192.1.168.3', 31)
```

**Syntax**

`parse_ipv4_mask(`*`Expr`*`, `*`PrefixMask`*`)`

**Argumente**

* *`Expr`*: Eine Zeichen folgen Darstellung der IPv4-Adresse, die in Long konvertiert wird. 
* *`PrefixMask`*: Eine ganze Zahl zwischen 0 und 32, die die Anzahl der signifikantesten Bits darstellt, die berücksichtigt werden.

**Rückgabe**

Wenn die Konvertierung erfolgreich ist, wird das Ergebnis eine lange Zahl sein.
Wenn die Konvertierung nicht erfolgreich ist, lautet das Ergebnis `null` .
