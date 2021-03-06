---
title: ipv6_compare ()-Azure Daten-Explorer
description: In diesem Artikel wird ipv6_compare ()-Funktion in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: ce14c466d927dd2431ae8e057bceec0d5fdd38b8
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803895"
---
# <a name="ipv6_compare"></a>ipv6_compare()

Vergleicht zwei IPv6-oder IPv4-Netzwerk Adress Zeichenfolgen. Die beiden IPv6-Zeichen folgen werden analysiert und verglichen, während die kombinierte IP-Präfix Maske, die aus den Argument Präfixen berechnet wurde, und das optionale Argument berücksichtigt werden `PrefixMask` .

```kusto
ipv6_compare('::ffff:7f00:1', '127.0.0.1') == 0
ipv6_compare('fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995')  < 0
ipv6_compare('192.168.1.1/24', '192.168.1.255/24') == 0
ipv6_compare('fe80::85d:e82c:9446:7994/127', 'fe80::85d:e82c:9446:7995/127') == 0
ipv6_compare('fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995', 127) == 0
```

> [!Note]
> Die Funktion kann Argumente akzeptieren und vergleichen, die sowohl IPv6-als auch IPv4-Netzwerkadressen darstellen. Wenn der Aufrufer jedoch weiß, dass Argumente im IPv4-Format vorliegen, verwenden Sie [ipv4_is_compare ()](./ipv4-comparefunction.md) -Funktion. Diese Funktion führt zu einer besseren Laufzeitleistung.

## <a name="syntax"></a>Syntax

`ipv6_compare(`*Expr1* `, ` *Expr2* `[ ,` *Prefixmask*`])`

## <a name="arguments"></a>Argumente

* *Expr1*, *expr2*: ein Zeichen folgen Ausdruck, der eine IPv6-oder IPv4-Adresse darstellt. IPv6-und IPv4-Zeichen folgen können mithilfe der IP-Präfix Notation maskiert werden (siehe Hinweis).
* *Prefixmask*: eine ganze Zahl zwischen 0 und 128, die die Anzahl der wichtigsten Bits darstellt, die berücksichtigt werden.

## <a name="ip-prefix-notation"></a>IP-Präfix Notation

Es ist üblich, IP-Adressen mit `IP-prefix notation` einem Schrägstrich () zu definieren `/` .
Die IP-Adresse links vom Schrägstrich ( `/` ) ist die Basis-IP-Adresse, und die Zahl (1 bis 127) rechts neben dem Schrägstrich ( `/` ) ist die Anzahl von zusammenhängenden 1 Bits in der netmask. 

Beispielsweise wird FE80:: 85D: e82c: 9446:7994/120 eine zugeordnete net/Subnetmask mit 120 angrenzenden Bits aufweisen.

## <a name="returns"></a>Gibt zurück

* `0`: Wenn die lange Darstellung des ersten IPv6-Zeichen folgen Arguments gleich dem zweiten IPv6-Zeichen folgen Argument ist.
* `1`: Wenn die lange Darstellung des ersten IPv6-Zeichen folgen Arguments größer als das zweite IPv6-Zeichen folgen Argument ist.
* `-1`: Wenn die lange Darstellung des ersten IPv6-Zeichen folgen Arguments kleiner als das zweite IPv6-Zeichen folgen Argument ist.
* `null`:, Wenn die Konvertierung für eine der beiden IPv6-Zeichen folgen nicht erfolgreich war.

## <a name="examples-ipv6ipv4-comparison-equality-cases"></a>Beispiele: IPv6/IPv4-Vergleichs Gleichheits Fälle

### <a name="compare-ips-using-the-ip-prefix-notation-specified-inside-the-ipv6ipv4-strings"></a>Vergleichen von IPS mithilfe der IP-Präfix Notation, die in den IPv6/IPv4-Zeichen folgen angegeben ist

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
| extend result = ipv6_compare(ip1_string, ip2_string)
```

|ip1_string|ip2_string|result|
|---|---|---|
|192.168.1.1|192.168.1.1|0|
|192.168.1.1/24|192.168.1.255|0|
|192.168.1.1|192.168.1.255/24|0|
|192.168.1.1/30|192.168.1.255/24|0|
|FE80:: 85D: e82c: 9446:7994|FE80:: 85D: e82c: 9446:7994|0|
|FE80:: 85D: e82c: 9446:7994/120|FE80:: 85D: e82c: 9446:7998|0|
|FE80:: 85D: e82c: 9446:7994|FE80:: 85D: e82c: 9446:7998/120|0|
|FE80:: 85D: e82c: 9446:7994/120|FE80:: 85D: e82c: 9446:7998/120|0|
|192.168.1.1|:: FFFF: c0a8:0101|0|
|192.168.1.1/24|:: FFFF: c0a8:01FF|0|
|:: FFFF: c0a8:0101|192.168.1.255/24|0|
|:: 192.168.1.1/30|192.168.1.255/24|0|

### <a name="compare-ips-using-ip-prefix-notation-specified-inside-the-ipv6ipv4-strings-and-as-additional-argument-of-the-ipv6_compare-function"></a>Vergleichen Sie IPS mit IP-Präfix Notation, die in den IPv6/IPv4-Zeichen folgen angegeben sind, und als zusätzliches Argument der `ipv6_compare()` Funktion.

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
| extend result = ipv6_compare(ip1_string, ip2_string, prefix)
```

|ip1_string|ip2_string|prefix|result|
|---|---|---|---|
|192.168.1.1|192.168.1.0|31|0|
|192.168.1.1/24|192.168.1.255|31|0|
|192.168.1.1|192.168.1.255|24|0|
|FE80:: 85D: e82c: 9446:7994|FE80:: 85D: e82c: 9446:7995|127|0|
|FE80:: 85D: e82c: 9446:7994/127|FE80:: 85D: e82c: 9446:7998|120|0|
|FE80:: 85D: e82c: 9446:7994/120|FE80:: 85D: e82c: 9446:7998|127|0|
|192.168.1.1/24|:: FFFF: c0a8:01FF|127|0|
|:: FFFF: c0a8:0101|192.168.1.255|120|0|
|:: 192.168.1.1/30|192.168.1.255/24|127|0|

