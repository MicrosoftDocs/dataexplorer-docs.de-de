---
title: parse_ipv6 ()-Azure Daten-Explorer
description: In diesem Artikel wird parse_ipv6 ()-Funktion in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: 25ed06f738e6b2e090ff92be9df026a85a27a89f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346421"
---
# <a name="parse_ipv6"></a>parse_ipv6()

Konvertiert IPv6-oder IPv4-Zeichen folgen in eine kanonische IPv6-Zeichen folgen Darstellung.

```kusto
parse_ipv6("127.0.0.1") == '0000:0000:0000:0000:0000:ffff:7f00:0001'
parse_ipv6(":fe80::85d:e82c:9446:7994") == 'fe80:0000:0000:0000:085d:e82c:9446:7994'
```

## <a name="syntax"></a>Syntax

`parse_ipv6(`*`Expr`*`)`

## <a name="arguments"></a>Argumente

* *`Expr`*: Zeichen folgen Ausdruck, der IPv6/IPv4-Netzwerkadresse darstellt, die in eine kanonische IPv6-Darstellung konvertiert wird. Die Zeichenfolge kann eine Netzwerk Maske mit [IP-Präfix Notation](#ip-prefix-notation)enthalten.

## <a name="ip-prefix-notation"></a>IP-Präfix Notation

IP-Adressen können `IP-prefix notation` mithilfe eines Schrägstrichs ( `/` ) definiert werden.
Die IP-Adresse links vom Schrägstrich ( `/` ) ist die Basis-IP-Adresse. Die Zahl (1 bis 127) rechts neben dem Schrägstrich ( `/` ) ist die Anzahl von zusammenhängenden 1 Bits in der netmask.

## <a name="returns"></a>Rückgabe

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis eine Zeichenfolge, die eine kanonische IPv6-Netzwerkadresse darstellt.
Wenn die Konvertierung nicht erfolgreich ist, lautet das Ergebnis `null` .

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip_string:string, netmask:long)
[
 '192.168.255.255',     32,  // 32-bit netmask is used
 '192.168.255.255/24',  30,  // 24-bit netmask is used, as IPv4 address doesn't use upper 8 bits
 '255.255.255.255',     24,  // 24-bit netmask is used
]
| extend ip_long = parse_ipv4_mask(ip_string, netmask)
```

|ip_string|Netzmaske|ip_long|
|---|---|---|
|192.168.255.255|32|3232301055|
|192.168.255.255/24|30|3232300800|
|255.255.255.255|24|4294967040|


