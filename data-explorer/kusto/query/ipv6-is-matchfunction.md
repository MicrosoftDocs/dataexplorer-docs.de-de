---
title: ipv6_is_match ()-Azure Daten-Explorer
description: In diesem Artikel wird ipv6_is_match ()-Funktion in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: d03e28c0f7df404d6bee81f7f749aab361cc45b9
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803878"
---
# <a name="ipv6_is_match"></a>ipv6_is_match()

Entspricht zwei IPv6-oder IPv4-Netzwerk Adress Zeichenfolgen. Die beiden IPv6/IPv4-Zeichen folgen werden analysiert und verglichen, während die kombinierte IP-Präfix Maske, die aus den Argument Präfixen berechnet wurde, und das optionale Argument berücksichtigt werden `PrefixMask` .

```kusto
ipv6_is_match('::ffff:7f00:1', '127.0.0.1') == true
ipv6_is_match('fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995') == false
ipv6_is_match('192.168.1.1/24', '192.168.1.255/24') == true
ipv6_is_match('fe80::85d:e82c:9446:7994/127', 'fe80::85d:e82c:9446:7995/127') == true
ipv6_is_match('fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995', 127) == true
```

> [!NOTE]
> Die Funktion kann Argumente akzeptieren und vergleichen, die sowohl IPv6-als auch IPv4-Netzwerkadressen darstellen. Wenn der Aufrufer weiß, dass Argumente im IPv4-Format vorliegen, verwenden Sie die Funktion [ipv4_is_match ()](./ipv4-is-matchfunction.md) . Diese Funktion führt zu einer besseren Laufzeitleistung.

## <a name="syntax"></a>Syntax

`ipv6_is_match(`*Expr1* `, ` *Expr2* `[ ,` *Prefixmask*`])`

## <a name="arguments"></a>Argumente

* *Expr1*, *expr2*: ein Zeichen folgen Ausdruck, der eine IPv6-oder IPv4-Adresse darstellt. IPv6-und IPv4-Zeichen folgen können mithilfe der [IP-Präfix Notation](#ip-prefix-notation)maskiert werden.
* *Prefixmask*: eine ganze Zahl zwischen 0 und 128, die die Anzahl der signifikantesten Bits darstellt, die berücksichtigt werden.

## <a name="ip-prefix-notation"></a>IP-Präfix Notation
 
IP-Adressen können `IP-prefix notation` mithilfe eines Schrägstrichs ( `/` ) definiert werden.
Die IP-Adresse links vom Schrägstrich ( `/` ) ist die Basis-IP-Adresse. Die Zahl (1 bis 127) rechts neben dem Schrägstrich ( `/` ) ist die Anzahl von zusammenhängenden 1 Bit in der netmask. 

Beispielsweise wird FE80:: 85D: e82c: 9446:7994/120 eine zugeordnete net/Subnetmask mit 120 angrenzenden Bits aufweisen.

## <a name="returns"></a>Gibt zurück

* `true`: Wenn die lange Darstellung des ersten IPv6/IPv4-Zeichen folgen Arguments gleich dem zweiten IPv6/IPv4-Zeichen folgen Argument ist.
* `false`Sonst.
* `null`: Wenn die Konvertierung für eine der beiden IPv6/IPv4-Zeichen folgen nicht erfolgreich war.

## <a name="examples"></a>Beispiele

### <a name="ipv6ipv4-comparison-equality-case---ip-prefix-notation-specified-inside-the-ipv6ipv4-strings"></a>IPv6/IPv4-Vergleichs Gleichheits Fall-IP-Präfix-Notation innerhalb der IPv6/IPv4-Zeichen folgen

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string)
[
 // IPv4 are compared as IPv6 addresses
 '192.168.1.1',    '192.168.1.1',       // Equal IPs
 '192.168.1.1/24', '192.168.1.255',     // 24 bit IP4-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255/24',  // 24 bit IP4-prefix is used for comparison
 '192.168.1.1/30', '192.168.1.255/24',  // 24 bit IP4-prefix is used for comparison
  // IPv6 cases
 'fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7994',         // Equal IPs
 'fe80::85d:e82c:9446:7994/120', 'fe80::85d:e82c:9446:7998',     // 120 bit IP6-prefix is used for comparison
 'fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7998/120',     // 120 bit IP6-prefix is used for comparison
 'fe80::85d:e82c:9446:7994/120', 'fe80::85d:e82c:9446:7998/120', // 120 bit IP6-prefix is used for comparison
 // Mixed case of IPv4 and IPv6
 '192.168.1.1',      '::ffff:c0a8:0101', // Equal IPs
 '192.168.1.1/24',   '::ffff:c0a8:01ff', // 24 bit IP-prefix is used for comparison
 '::ffff:c0a8:0101', '192.168.1.255/24', // 24 bit IP-prefix is used for comparison
 '::192.168.1.1/30', '192.168.1.255/24', // 24 bit IP-prefix is used for comparison
]
| extend result = ipv6_is_match(ip1_string, ip2_string)
```

|ip1_string|ip2_string|result|
|---|---|---|
|192.168.1.1|192.168.1.1|1|
|192.168.1.1/24|192.168.1.255|1|
|192.168.1.1|192.168.1.255/24|1|
|192.168.1.1/30|192.168.1.255/24|1|
|FE80:: 85D: e82c: 9446:7994|FE80:: 85D: e82c: 9446:7994|1|
|FE80:: 85D: e82c: 9446:7994/120|FE80:: 85D: e82c: 9446:7998|1|
|FE80:: 85D: e82c: 9446:7994|FE80:: 85D: e82c: 9446:7998/120|1|
|FE80:: 85D: e82c: 9446:7994/120|FE80:: 85D: e82c: 9446:7998/120|1|
|192.168.1.1|:: FFFF: c0a8:0101|1|
|192.168.1.1/24|:: FFFF: c0a8:01FF|1|
|:: FFFF: c0a8:0101|192.168.1.255/24|1|
|:: 192.168.1.1/30|192.168.1.255/24|1|


### <a name="ipv6ipv4-comparison-equality-case--ip-prefix-notation-specified-inside-the-ipv6ipv4-strings-and-as-additional-argument-of-the-ipv6_is_match-function"></a>IPv6/IPv4-Vergleichs Gleichheits Fall-IP-Präfix Notation in den IPv6/IPv4-Zeichen folgen und als zusätzliches Argument der `ipv6_is_match()` Funktion

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string, prefix:long)
[
 // IPv4 are compared as IPv6 addresses 
 '192.168.1.1',    '192.168.1.0',   31, // 31 bit IP4-prefix is used for comparison
 '192.168.1.1/24', '192.168.1.255', 31, // 24 bit IP4-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255', 24, // 24 bit IP4-prefix is used for comparison
   // IPv6 cases
 'fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995',     127, // 127 bit IP6-prefix is used for comparison
 'fe80::85d:e82c:9446:7994/127', 'fe80::85d:e82c:9446:7998', 120, // 120 bit IP6-prefix is used for comparison
 'fe80::85d:e82c:9446:7994/120', 'fe80::85d:e82c:9446:7998', 127, // 120 bit IP6-prefix is used for comparison
 // Mixed case of IPv4 and IPv6
 '192.168.1.1/24',   '::ffff:c0a8:01ff', 127, // 127 bit IP6-prefix is used for comparison
 '::ffff:c0a8:0101', '192.168.1.255',    120, // 120 bit IP6-prefix is used for comparison
 '::192.168.1.1/30', '192.168.1.255/24', 127, // 120 bit IP6-prefix is used for comparison
]
| extend result = ipv6_is_match(ip1_string, ip2_string, prefix)
```

|ip1_string|ip2_string|prefix|result|
|---|---|---|---|
|192.168.1.1|192.168.1.0|31|1|
|192.168.1.1/24|192.168.1.255|31|1|
|192.168.1.1|192.168.1.255|24|1|
|FE80:: 85D: e82c: 9446:7994|FE80:: 85D: e82c: 9446:7995|127|1|
|FE80:: 85D: e82c: 9446:7994/127|FE80:: 85D: e82c: 9446:7998|120|1|
|FE80:: 85D: e82c: 9446:7994/120|FE80:: 85D: e82c: 9446:7998|127|1|
|192.168.1.1/24|:: FFFF: c0a8:01FF|127|1|
|:: FFFF: c0a8:0101|192.168.1.255|120|1|
|:: 192.168.1.1/30|192.168.1.255/24|127|1|
