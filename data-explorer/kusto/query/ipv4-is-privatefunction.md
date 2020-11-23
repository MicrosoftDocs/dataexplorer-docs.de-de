---
title: ipv4_is_private ()-Azure Daten-Explorer
description: In diesem Artikel wird die Funktion "ipv4_is_private ()" in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/20/2020
ms.openlocfilehash: 161e0a2ca94bb9b037fc619c0fc5dfbddd8d28da
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324913"
---
# <a name="ipv4_is_private"></a>ipv4_is_private ()

Überprüft, ob die IPv4-Zeichen folgen Adresse zu einem Satz privater Netzwerk-IPS gehört.

[Private Netzwerkadressen](https://en.wikipedia.org/wiki/Private_network) wurden ursprünglich definiert, um das Verzögern der IPv4-Adress Erschöpfung zu unterstützen. IP-Pakete, die von einer privaten IP-Adresse stammen oder an diese adressiert sind, können nicht über das öffentliche Internet geleitet werden

## <a name="private-ipv4-addresses"></a>Private IPv4-Adressen

Die Internet Engineering Task Force (IETF) hat die Internet Assigned Numbers Authority (IANA) angewiesen, die folgenden IPv4-Adressbereiche für private Netzwerke zu reservieren:

| IP-Adressbereich|Anzahl der Adressen|Größter CIDR-Block (Subnetzmaske)|
|-----------------|-------------------|--------------------------------|
|10.0.0.0–10.255.255.255|16777216|10.0.0.0/8 (255.0.0.0)|
|172.16.0.0–172.31.255.255|1048576|172.16.0.0/12 (255.240.0.0)|
|192.168.0.0–192.168.255.255|65536|192.168.0.0/16 (255.255.0.0)|


```kusto
ipv4_is_private('192.168.1.1/24') == true
ipv4_is_private('10.1.2.3/24') == true
ipv4_is_private('202.1.2.3') == false
ipv4_is_private("127.0.0.1") == false
```

## <a name="syntax"></a>Syntax

`ipv4_is_private(`*Expr*`)`

## <a name="arguments"></a>Argumente

*Expr*: ein Zeichen folgen Ausdruck, der eine IPv4-Adresse darstellt. IPv4-Zeichen folgen können mithilfe der [IP-Präfix Notation](#ip-prefix-notation)maskiert werden.

### <a name="ip-prefix-notation"></a>IP-Präfix Notation

IP-Adressen können `IP-prefix notation` mithilfe eines Schrägstrichs ( `/` ) definiert werden. Die IP-Adresse links vom Schrägstrich ( `/` ) ist die Basis-IP-Adresse. Die Zahl (1 bis 32) rechts neben dem Schrägstrich ( `/` ) ist die Anzahl von zusammenhängenden 1 Bit in der netmask. 

Beispielsweise verfügt 192.168.2.0/24 über eine zugeordnete net/Subnetmask, die 24 aufeinander folgende Bits oder 255.255.255.0 im gepunkteten Dezimal Format enthält.

## <a name="returns"></a>Gibt zurück

* `true`: Wenn die IPv4-Adresse zu einem der privaten Netzwerk Bereiche gehört.
*  `false`Sonst.
* `null`:, Wenn die Konvertierung für eine der beiden IPv4-Zeichen folgen nicht erfolgreich war.

## <a name="example-check-if-ipv4-belongs-to-a-private-network"></a>Beispiel: überprüfen, ob IPv4 zu einem privaten Netzwerk gehört

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip_string:string)
[
 '10.1.2.3',
 '192.168.1.1/24',
 '127.0.0.1',
]
| extend result = ipv4_is_private(ip_string)
```

|ip_string|result|
|---|---|
|10.1.2.3|1|
|192.168.1.1/24|1|
|127.0.0.1|0|
