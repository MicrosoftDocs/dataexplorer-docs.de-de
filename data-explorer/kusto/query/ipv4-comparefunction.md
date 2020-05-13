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
ms.openlocfilehash: 9fec1869ee06e4fd9a9932e42c6ab1049b50a04f
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271501"
---
# <a name="ipv4_compare"></a>ipv4_compare ()

Vergleicht zwei IPv4-Zeichen folgen.

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

### <a name="ip-prefix-notation"></a>IP-Präfix Notation

Es ist üblich, IP-Adressen mithilfe `IP-prefix notation` eines Schrägstrichs () zu definieren `/` .
Die IP-Adresse links vom Schrägstrich ( `/` ) ist die Basis-IP-Adresse, und die Zahl (1 bis 32) rechts neben dem Schrägstrich ( `/` ) ist die Anzahl von zusammenhängenden 1 Bits in der netmask. 

Beispiel: 192.168.2.0/24 verfügt über eine zugeordnete net/Subnetmask mit 24 aufeinander folgenden Bits oder 255.255.255.0 im gepunkteten Dezimal Format.

**Rückgabe**

Die beiden IPv4-Zeichen folgen werden analysiert und verglichen, während die kombinierte IP-Präfix Maske, die aus den Argument Präfixen berechnet wurde, und das optionale Argument berücksichtigt werden `PrefixMask` .

Rückgabewerte:
* `0`: Wenn die lange Darstellung des ersten IPv4-Zeichen folgen Arguments gleich dem zweiten IPv4-Zeichen folgen Argument ist
* `1`: Wenn die lange Darstellung des ersten IPv4-Zeichen folgen Arguments größer als das zweite IPv4-Zeichen folgen Argument ist
* `-1`: Wenn die lange Darstellung des ersten IPv4-Zeichen folgen Arguments kleiner ist als das zweite IPv4-Zeichen folgen Argument.

Wenn die Konvertierung für eine der beiden IPv4-Zeichen folgen nicht erfolgreich war, ist das Ergebnis `null` .

## <a name="examples-ipv4-comparison-equality-cases"></a>Beispiele: IPv4-Vergleichs Übereinstimmungs Fälle

Im folgenden Beispiel werden verschiedene IPS mit der in den IPv4-Zeichen folgen angegebenen IP-Präfix Notation verglichen.

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

Im folgenden Beispiel werden verschiedene IPS mithilfe der in den IPv4-Zeichen folgen angegebenen IP-Präfix Notation und als zusätzliches Argument der- `ipv4_compare()` Funktion verglichen.

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
