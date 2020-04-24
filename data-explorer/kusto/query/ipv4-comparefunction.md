---
title: ipv4_compare() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird ipv4_compare() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: ddaaa4e1f9dcf9dfbcd55406cd26e8f1ff4a02fa
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513684"
---
# <a name="ipv4_compare"></a>ipv4_compare()

Vergleicht zwei IPv4-Zeichenfolgen.

```kusto
ipv4_compare("127.0.0.1", "127.0.0.1") == 0
ipv4_compare('192.168.1.1', '192.168.1.255') < 0
ipv4_compare('192.168.1.1/24', '192.168.1.255/24') == 0
ipv4_compare('192.168.1.1', '192.168.1.255', 24) == 0
```

**Syntax**

`ipv4_compare(`*Expr1*`, `*Expr2*`[ ,`*PräfixMaske*`])`

**Argumente**

* *Expr1*, *Expr2*: Ein Zeichenfolgenausdruck, der eine IPv4-Adresse darstellt. IPv4-Zeichenfolgen können mithilfe der [IP-Präfixnotation](#ip-prefix-notation)maskiert werden.
* *PrefixMask*: Eine ganze Zahl von 0 bis 32, die die Anzahl der wichtigsten Bits darstellt, die berücksichtigt werden.

### <a name="ip-prefix-notation"></a>IP-Präfixnotation

Es ist üblich, IP-Adressen `IP-prefix notation` mit einem`/`Schrägstrich ( ) zu definieren.
Die IP-Adresse des Schrägstrichs`/`( ) an die LINKE ist die Basis-IP-Adresse, und`/`die Zahl (1 bis 32) zum RECHTS des Schrägstrichs ( ) ist die Anzahl der zusammenhängenden 1 Bits in der Netzmaske. 

Beispiel: 192.168.2.0/24 enthält eine zugeordnete Netz-/Subnetzmaske mit 24 zusammenhängenden Bits oder 255.255.255.0 im gepunkteten Dezimalformat.

**Rückgabe**

Die beiden IPv4-Zeichenfolgen werden analysiert und verglichen, während die kombinierte IP-Präfixmaske `PrefixMask` berücksichtigt wird, die aus Argumentpräfixen berechnet wird, und das optionale Argument.

Rückgabewerte:
* `0`: Wenn die lange Darstellung des ersten IPv4-Zeichenfolgenarguments gleich dem zweiten IPv4-Zeichenfolgenargument ist
* `1`: Wenn die lange Darstellung des ersten IPv4-Zeichenfolgenarguments größer als das zweite IPv4-Zeichenfolgenargument ist
* `-1`: Wenn die lange Darstellung des ersten IPv4-Zeichenfolgenarguments kleiner als das zweite IPv4-Zeichenfolgenargument ist

Wenn die Konvertierung für eine der beiden IPv4-Zeichenfolgen nicht erfolgreich war, wird `null`das Ergebnis .

## <a name="examples-ipv4-comparison-equality-cases"></a>Beispiele: IPv4-Vergleichsgleichheitsfälle

Im folgenden Beispiel werden verschiedene IPs mit der IP-Präfixnotation verglichen, die in den IPv4-Zeichenfolgen angegeben ist.

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

Im folgenden Beispiel werden verschiedene IPs mit IP-Präfixnotation verglichen, die in `ipv4_compare()` den IPv4-Zeichenfolgen angegeben sind, sowie als zusätzliches Argument für die Funktion.

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