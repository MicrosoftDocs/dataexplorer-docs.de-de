---
title: ipv4_compare ()-Azure Daten-Explorer
description: In diesem Artikel wird ipv4_compare () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 67887aac4ab04e016ed63045e66ebcfab343c135
ms.sourcegitcommit: 8953d09101f4358355df60ab09e55e71bc255ead
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2020
ms.locfileid: "84420865"
---
# <a name="ipv4_compare"></a>ipv4_compare()

Vergleicht zwei IPv4-Zeichen folgen. Die beiden IPv4-Zeichen folgen werden analysiert und verglichen, während die kombinierte IP-Präfix Maske, die aus den Argument Präfixen berechnet wurde, und das optionale Argument berücksichtigt werden `PrefixMask` .

```kusto
ipv4_compare("127.0.0.1", "127.0.0.1") == 0
ipv4_compare('192.168.1.1', '192.168.1.255') < 0
ipv4_compare('192.168.1.1/24', '192.168.1.255/24') == 0
ipv4_compare('192.168.1.1', '192.168.1.255', 24) == 0
```

**Syntax**

`ipv4_compare(`*Expr1* `, ` *Expr2* `[ ,` *Prefixmask*`])`

**Argumente**

* *Expr1*, *expr2*: ein Zeichen folgen Ausdruck, der eine IPv4-Adresse darstellt. IPv4-Zeichen folgen können mithilfe der [IP-Präfix Notation](#ip-prefix-notation)maskiert werden.
* *Prefixmask*: eine ganze Zahl zwischen 0 und 32, die die Anzahl der signifikantesten Bits darstellt, die berücksichtigt werden.

## <a name="ip-prefix-notation"></a>IP-Präfix Notation
 
IP-Adressen können `IP-prefix notation` mithilfe eines Schrägstrichs ( `/` ) definiert werden.
Die IP-Adresse links vom Schrägstrich ( `/` ) ist die Basis-IP-Adresse. Die Zahl (1 bis 32) rechts neben dem Schrägstrich ( `/` ) ist die Anzahl von zusammenhängenden 1 Bit in der netmask. 

**Beispiel:** 192.168.2.0/24 verfügt über eine zugeordnete net/Subnetmask mit 24 aufeinander folgenden Bits oder 255.255.255.0 im gepunkteten Dezimal Format.

**Rückgabe**

* `0`: Wenn die lange Darstellung des ersten IPv4-Zeichen folgen Arguments gleich dem zweiten IPv4-Zeichen folgen Argument ist
* `1`: Wenn die lange Darstellung des ersten IPv4-Zeichen folgen Arguments größer als das zweite IPv4-Zeichen folgen Argument ist
* `-1`: Wenn die lange Darstellung des ersten IPv4-Zeichen folgen Arguments kleiner ist als das zweite IPv4-Zeichen folgen Argument.
* `null`:, Wenn die Konvertierung für eine der beiden IPv4-Zeichen folgen nicht erfolgreich war.

## <a name="examples-ipv4-comparison-equality-cases"></a>Beispiele: IPv4-Vergleichs Übereinstimmungs Fälle

### <a name="compare-ips-using-the-ip-prefix-notation-specified-inside-the-ipv4-strings"></a>Vergleichen von IPS mithilfe der in den IPv4-Zeichen folgen angegebenen IP-Präfix Notation

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string)
[
 '192.168.1.0',    '192.168.1.0',       // Equal IPs
 '192.168.1.1/24', '192.168.1.255',     // 24 bit IP-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255/24',  // 24 bit IP-prefix is used for comparison
 '192.168.1.1/30', '192.168.1.255/24',  // 24 bit IP-prefix is used for comparison
]
| extend result = ipv4_compare(ip1_string, ip2_string)
```

|ip1_string|ip2_string|result|
|---|---|---|
|192.168.1.0|192.168.1.0|0|
|192.168.1.1/24|192.168.1.255|0|
|192.168.1.1|192.168.1.255/24|0|
|192.168.1.1/30|192.168.1.255/24|0|

### <a name="compare-ips-using-ip-prefix-notation-specified-inside-the-ipv4-strings-and-as-additional-argument-of-the-ipv4_compare-function"></a>Vergleichen Sie IPS mithilfe der in den IPv4-Zeichen folgen angegebenen IP-Präfix Notation und als zusätzliches Argument der `ipv4_compare()` Funktion.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string, prefix:long)
[
 '192.168.1.1',    '192.168.1.0',   31, // 31 bit IP-prefix is used for comparison
 '192.168.1.1/24', '192.168.1.255', 31, // 24 bit IP-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255', 24, // 24 bit IP-prefix is used for comparison
]
| extend result = ipv4_compare(ip1_string, ip2_string, prefix)
```

|ip1_string|ip2_string|prefix|result|
|---|---|---|---|
|192.168.1.1|192.168.1.0|31|0|
|192.168.1.1/24|192.168.1.255|31|0|
|192.168.1.1|192.168.1.255|24|0|

