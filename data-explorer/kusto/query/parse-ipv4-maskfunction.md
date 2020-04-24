---
title: parse_ipv4_mask() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird parse_ipv4_mask() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 994c1e551d7c38bb1493579bcf10438acd2923f2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511780"
---
# <a name="parse_ipv4_mask"></a>parse_ipv4_mask()

Konvertiert die Eingabezeichenfolge von IPv4 und Netmask in eine lange Zahlendarstellung (signiert 64-Bit).

```kusto
parse_ipv4_mask("127.0.0.1", 24) == 2130706432

parse_ipv4_mask('192.1.168.2', 31) == parse_ipv4_mask('192.1.168.3', 31) 
```

**Syntax**

`parse_ipv4_mask(`*Expr*`, `*PrefixMask*`)`

**Argumente**

* *Expr*: Eine Zeichenfolgendarstellung der IPv4-Adresse, die in long konvertiert wird. 
* *PrefixMask*: Eine ganze Zahl von 0 bis 32, die die Anzahl der wichtigsten Bits darstellt, die berücksichtigt werden.

**Rückgabe**

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis eine lange Zahl.
Wenn die Konvertierung nicht erfolgreich `null`ist, wird das Ergebnis .