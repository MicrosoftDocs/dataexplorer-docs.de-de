---
title: parse_ipv4 ()-Azure Daten-Explorer
description: In diesem Artikel wird parse_ipv4 () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: f5d65fa75a4f53bea7d27202c5aed2228c167bbd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250907"
---
# <a name="parse_ipv4"></a>parse_ipv4()

Konvertiert die IPv4-Zeichenfolge in eine Long-Darstellung (signed 64-Bit).

```kusto
parse_ipv4("127.0.0.1") == 2130706433
parse_ipv4('192.1.168.1') < parse_ipv4('192.1.168.2') == true
```

## <a name="syntax"></a>Syntax

`parse_ipv4(`*`Expr`*`)`

## <a name="arguments"></a>Argumente

* *`Expr`*: Zeichen folgen Ausdruck, der IPv4 darstellt, der in Long konvertiert wird. Die Zeichenfolge kann eine Netzwerk Maske mit [IP-Präfix Notation](#ip-prefix-notation)enthalten.

## <a name="ip-prefix-notation"></a>IP-Präfix Notation

IP-Adressen können `IP-prefix notation` mithilfe eines Schrägstrichs ( `/` ) definiert werden.
Die IP-Adresse links vom Schrägstrich ( `/` ) ist die Basis-IP-Adresse. Die Zahl (1 bis 32) rechts vom Schrägstrich (/) ist die Anzahl von zusammenhängenden 1 Bit in der netmask.

Beispielsweise verfügt 192.168.2.0/24 über eine zugeordnete net/Subnetmask, die 24 aufeinander folgende Bits oder 255.255.255.0 im gepunkteten Dezimal Format enthält.

## <a name="returns"></a>Rückgabe

Wenn die Konvertierung erfolgreich ist, wird das Ergebnis eine lange Zahl sein.
Wenn die Konvertierung nicht erfolgreich ist, lautet das Ergebnis `null` .
 
## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip_string:string)
[
 '192.168.1.1',
 '192.168.1.1/24',
 '255.255.255.255/31'
]
| extend ip_long = parse_ipv4(ip_string)
```

|ip_string|ip_long|
|---|---|
|192.168.1.1|3232235777|
|192.168.1.1/24|3232235776|
|255.255.255.255/31|4294967294|
