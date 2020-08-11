---
title: format_ipv4 ()-Azure Daten-Explorer
description: In diesem Artikel wird format_ipv4 () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/09/2020
ms.openlocfilehash: 5f941a640ffc0bf30859509cf3e3eb1235f464ff
ms.sourcegitcommit: ed902a5a781e24e081cd85910ed15cd468a0db1e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88075391"
---
# <a name="format_ipv4"></a>format_ipv4 ()

Analysiert Eingaben mit einer Netzmaske und gibt eine Zeichenfolge zurück, die die IPv4-Adresse darstellt.

```kusto
print format_ipv4('192.168.1.255', 24) == '192.168.1.0'
print format_ipv4(3232236031, 24) == '192.168.1.0'
```

## <a name="syntax"></a>Syntax

`format_ipv4(`*Expr* [ `,` *prefixmask*`])`

## <a name="arguments"></a>Argumente

* *`Expr`*: Eine Zeichen folgen-oder Zahlen Darstellung der IPv4-Adresse.
* *`PrefixMask`*: (Optional) eine ganze Zahl zwischen 0 und 32, die die Anzahl der signifikantesten Bits darstellt, die berücksichtigt werden. Wenn das Argument nicht angegeben wird, werden alle Bitmasken verwendet (32).

## <a name="returns"></a>Gibt zurück

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis eine Zeichenfolge, die die IPv4-Adresse darstellt.
Wenn die Konvertierung nicht erfolgreich ist, ist das Ergebnis eine leere Zeichenfolge.

## <a name="see-also"></a>Weitere Informationen

- [format_ipv4_mask ()](format-ipv4-mask-function.md): für die IPv4-Adress Formatierung, einschließlich CIDR-Notation.
- [IPv4-und IPv6-Funktionen](scalarfunctions.md#ipv4ipv6-functions)

## <a name="examples"></a>Beispiele

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(address:string, mask:long)
[
 '192.168.1.1', 24,          
 '192.168.1.1', 32,          
 '192.168.1.1/24', 32,       
 '192.168.1.1/24', long(-1), 
]
| extend result = format_ipv4(address, mask), 
         result_mask = format_ipv4_mask(address, mask)
```

|address|mask|result|result_mask|
|---|---|---|---|
|192.168.1.1|24|192.168.1.0|192.168.1.0/24|
|192.168.1.1|32|192.168.1.1|192.168.1.1/32|
|192.168.1.1/24|32|192.168.1.0|192.168.1.0/24|
|192.168.1.1/24|-1|||
