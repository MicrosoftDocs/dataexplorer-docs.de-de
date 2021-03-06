---
title: parse_ipv6_mask ()-Azure Daten-Explorer
description: In diesem Artikel wird parse_ipv6_mask ()-Funktion in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: b917d592a00bb2bacd940b4dc943186e10d1bf8c
ms.sourcegitcommit: 5e903c61e779f7bf62f745f13a6038ce2a32e934
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88512545"
---
# <a name="parse_ipv6_mask"></a>parse_ipv6_mask()
 
Konvertiert die IPv6/IPv4-Zeichenfolge und die Netzmaske in eine kanonische IPv6-Zeichen folgen Darstellung.

```kusto
parse_ipv6_mask("127.0.0.1", 24) == '0000:0000:0000:0000:0000:ffff:7f00:0000'
parse_ipv6_mask(":fe80::85d:e82c:9446:7994", 120) == 'fe80:0000:0000:0000:085d:e82c:9446:7900'
```

## <a name="syntax"></a>Syntax

`parse_ipv6_mask(`*`Expr`*`, `*`PrefixMask`*`)`

## <a name="arguments"></a>Argumente

* *`Expr`*: Zeichen folgen Ausdruck, der IPv6/IPv4-Netzwerkadresse darstellt, die in eine kanonische IPv6-Darstellung konvertiert wird. Die Zeichenfolge kann eine Netzwerk Maske mit [IP-Präfix Notation](#ip-prefix-notation)enthalten.
* *`PrefixMask`*: Eine ganze Zahl zwischen 0 und 128, die die Anzahl der signifikantesten Bits darstellt, die berücksichtigt werden.

## <a name="ip-prefix-notation"></a>IP-Präfix Notation

IP-Adressen können `IP-prefix notation` mithilfe eines Schrägstrichs ( `/` ) definiert werden.
Die IP-Adresse links vom Schrägstrich ( `/` ) ist die Basis-IP-Adresse. Die Zahl (1 bis 127) rechts neben dem Schrägstrich ( `/` ) ist die Anzahl von zusammenhängenden 1 Bit in der netmask.

## <a name="returns"></a>Rückgabe

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis eine Zeichenfolge, die eine kanonische IPv6-Netzwerkadresse darstellt.
Wenn die Konvertierung nicht erfolgreich ist, lautet das Ergebnis `null` .

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip_string:string, netmask:long)
[
 // IPv4 addresses
 '192.168.255.255',     120,  // 120-bit netmask is used
 '192.168.255.255/24',  124,  // 120-bit netmask is used, as IPv4 address doesn't use upper 8 bits
 '255.255.255.255', 128,  // 128-bit netmask is used
 // IPv6 addresses
 'fe80::85d:e82c:9446:7994', 128,     // 128-bit netmask is used
 'fe80::85d:e82c:9446:7994/120', 124, // 120-bit netmask is used
 // IPv6 with IPv4 notation
 '::192.168.255.255',    128,  // 128-bit netmask is used
 '::192.168.255.255/24', 128,  // 120-bit netmask is used, as IPv4 address doesn't use upper 8 bits
]
| extend ip6_canonical = parse_ipv6_mask(ip_string, netmask)
```

|ip_string|Netzmaske|ip6_canonical|
|---|---|---|
|192.168.255.255|120|0000:0000:0000:0000:0000: FFFF: c0a8: FF00|
|192.168.255.255/24|124|0000:0000:0000:0000:0000: FFFF: c0a8: FF00|
|255.255.255.255|128|0000:0000:0000:0000:0000: FFFF: FFFF: FFFF|
|FE80:: 85D: e82c: 9446:7994|128|FE80:0000:0000:0000:085d: e82c: 9446:7994|
|FE80:: 85D: e82c: 9446:7994/120|124|FE80:0000:0000:0000:085d: e82c: 9446:7900|
|:: 192.168.255.255|128|0000:0000:0000:0000:0000: FFFF: c0a8: FFFF|
|:: 192.168.255.255/24|128|0000:0000:0000:0000:0000: FFFF: c0a8: FF00|

