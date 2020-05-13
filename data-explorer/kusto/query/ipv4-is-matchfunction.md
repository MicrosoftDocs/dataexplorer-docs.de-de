---
title: ipv4_is_match ()-Azure Daten-Explorer
description: In diesem Artikel wird ipv4_is_match () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: b63a73efe73223ba7c6bd2b42c6e05a6c60e94b6
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271450"
---
# <a name="ipv4_is_match"></a>ipv4_is_match ()

Entspricht zwei IPv4-Zeichen folgen.

```kusto
ipv4_is_match("127.0.0.1", "127.0.0.1") == true
ipv4_is_match('192.168.1.1', '192.168.1.255') == false
ipv4_is_match('192.168.1.1/24', '192.168.1.255/24') == true
ipv4_is_match('192.168.1.1', '192.168.1.255', 24) == true
```

**Syntax**

`ipv4_is_match(`*Expr1* `, ` *Expr2* `[ ,` *Prefixmask*`])`

**Argumente**

* *Expr1*, *expr2*: ein Zeichen folgen Ausdruck, der eine IPv4-Adresse darstellt. IPv4-Zeichen folgen können mithilfe der [IP-Präfix Notation](#ip-prefix-notation)maskiert werden.
* *Prefixmask*: eine ganze Zahl zwischen 0 und 32, die die Anzahl der signifikantesten Bits darstellt, die berücksichtigt werden.

### <a name="ip-prefix-notation"></a>IP-Präfix Notation

Es ist üblich, IP-Adressen mithilfe `IP-prefix notation` eines Schrägstrichs () zu definieren `/` . Die IP-Adresse links vom Schrägstrich ( `/` ) ist die Basis-IP-Adresse, und die Zahl (1 bis 32) rechts neben dem Schrägstrich ( `/` ) ist die Anzahl von zusammenhängenden 1 Bits in der netmask. 

Beispiel: 192.168.2.0/24 verfügt über eine zugeordnete net/Subnetmask mit 24 aufeinander folgenden Bits oder 255.255.255.0 im gepunkteten Dezimal Format.

**Rückgabe**

Die beiden IPv4-Zeichen folgen werden analysiert und verglichen, während die kombinierte IP-Präfix Maske, die aus den Argument Präfixen berechnet wurde, und das optionale Argument berücksichtigt werden `PrefixMask` .

Rückgabewerte:
* `true`: Wenn die lange Darstellung des ersten IPv4-Zeichen folgen Arguments gleich dem zweiten IPv4-Zeichen folgen Argument ist.
*  `false`Sonst.

Wenn die Konvertierung für eine der beiden IPv4-Zeichen folgen nicht erfolgreich war, ist das Ergebnis `null` .

**Beispiele**

## <a name="ipv4-comparison-equality-cases"></a>Gleichheits Fälle für IPv4-Vergleiche

Im folgenden Beispiel werden verschiedene IPS mit IP-Präfix Notation verglichen, die in den IPv4-Zeichen folgen angegeben sind.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string)
[
 '192.168.1.0',    '192.168.1.0',       // Equal IPs
 '192.168.1.1/24', '192.168.1.255',     // 24 bit IP-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255/24',  // 24 bit IP-prefix is used for comparison
 '192.168.1.1/30', '192.168.1.255/24',  // 24 bit IP-prefix is used for comparison
]
| extend result = ipv4_is_match(ip1_string, ip2_string)
```

|ip1_string|ip2_string|result|
|---|---|---|
|192.168.1.0|192.168.1.0|1|
|192.168.1.1/24|192.168.1.255|1|
|192.168.1.1|192.168.1.255/24|1|
|192.168.1.1/30|192.168.1.255/24|1|

Im folgenden Beispiel werden verschiedene IPS mithilfe der in den IPv4-Zeichen folgen angegebenen IP-Präfix Notation und als zusätzliches Argument der- `ipv4_is_match()` Funktion verglichen.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string, prefix:long)
[
 '192.168.1.1',    '192.168.1.0',   31, // 31 bit IP-prefix is used for comparison
 '192.168.1.1/24', '192.168.1.255', 31, // 24 bit IP-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255', 24, // 24 bit IP-prefix is used for comparison
]
| extend result = ipv4_is_match(ip1_string, ip2_string, prefix)
```

|ip1_string|ip2_string|prefix|result|
|---|---|---|---|
|192.168.1.1|192.168.1.0|31|1|
|192.168.1.1/24|192.168.1.255|31|1|
|192.168.1.1|192.168.1.255|24|1|
