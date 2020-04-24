---
title: parse_ipv4() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird parse_ipv4() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 296c7e77a2397501ae086e16154ca249249c23e4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511746"
---
# <a name="parse_ipv4"></a>parse_ipv4()

Konvertiert IPv4-Zeichenfolge in lange (signierte 64-Bit) Zahlendarstellung.

```kusto
parse_ipv4("127.0.0.1") == 2130706433
parse_ipv4('192.1.168.1') < parse_ipv4('192.1.168.2') == true
```

**Syntax**

`parse_ipv4(`*Expr*`)`

**Argumente**

* *Expr*: String-Ausdruck, der IPv4 darstellt, der in long konvertiert wird. String kann Netzmaske mit [IP-Präfixnotation](#ip-prefix-notation)enthalten.

### <a name="ip-prefix-notation"></a>IP-Präfixnotation

Es ist üblich, IP-Adressen `IP-prefix notation` mit einem`/`Schrägstrich ( ) zu definieren.
Die IP-Adresse für die LINKE`/`des Schrägstrichs ( ) ist die Basis-IP-Adresse, und die Zahl (1 bis 32) zum RECHTS des Schrägstrichs (/) ist die Anzahl der zusammenhängenden 1 Bits in der Netzmaske. Somit verfügt 192.168.2.0/24 über eine zugehörige Netz-/Subnetzmaske, die 24 zusammenhängende Bits oder 255.255.255.0 im gepunkteten Dezimalformat enthält.

**Rückgabe**

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis eine lange Zahl.
Wenn die Konvertierung nicht erfolgreich `null`ist, wird das Ergebnis .
 
**Beispiel**

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