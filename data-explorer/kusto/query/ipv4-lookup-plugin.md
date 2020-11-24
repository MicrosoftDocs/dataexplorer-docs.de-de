---
title: 'ipv4_lookup-Plug-in: Azure Daten-Explorer'
description: In diesem Artikel wird ipv4_lookup-Plug-in in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/22/2020
ms.openlocfilehash: ec826e7d16e575fa4904aba93575ab7e605d2b18
ms.sourcegitcommit: 3af95ea6a6746441ac71b1a217bbb02ee23d5f28
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511147"
---
# <a name="ipv4_lookup-plugin"></a>Plug-in ipv4_lookup

Das `ipv4_lookup` Plug-in sucht in einer Nachschlage Tabelle nach einem IPv4-Wert und gibt Zeilen mit übereinstimmenden Werten zurück.

```kusto
T | evaluate ipv4_lookup(LookupTable, SourceIPv4Key, LookupKey)

T | evaluate ipv4_lookup(LookupTable, SourceIPv4Key, LookupKey, return_unmatched = true)
```

## <a name="syntax"></a>Syntax

*T* `|` `evaluate` `ipv4_lookup(` *LookupTable* `,` *SourceIPv4Key* `,` *lookupkey* [ `,` *return_unmatched* ] `)`

## <a name="arguments"></a>Argumente

* *T*: die tabellarische Eingabe, deren Spalte *SourceIPv4Key* für die IPv4-Übereinstimmung verwendet wird.
* *LookupTable*: Tabelle oder tabellarischer Ausdruck mit IPv4- *lookupdaten, deren lookupkey* -Spalte für IPv4-Abgleich verwendet wird. IPv4-Werte können mithilfe der [IP-Präfix Notation](#ip-prefix-notation)maskiert werden.
* *SourceIPv4Key*: die Spalte von *T* mit einer IPv4-Zeichenfolge, die in *LookupTable* gesucht werden soll. IPv4-Werte können mithilfe der [IP-Präfix Notation](#ip-prefix-notation)maskiert werden.
* *Lookupkey*: die Spalte von *LookupTable* mit IPv4-Zeichenfolge, die mit den einzelnen *SourceIPv4Key* -Werten abgeglichen wird.
* *return_unmatched*: ein boolesches Flag, das definiert, ob das Ergebnis alle oder nur übereinstimmende Zeilen einschließen soll (Standard: `false` nur übereinstimmende Zeilen, die zurückgegeben werden).

### <a name="ip-prefix-notation"></a>IP-Präfix Notation
 
IP-Adressen können `IP-prefix notation` mithilfe eines Schrägstrichs ( `/` ) definiert werden.
Die IP-Adresse links vom Schrägstrich ( `/` ) ist die Basis-IP-Adresse. Die Zahl (1 bis 32) rechts neben dem Schrägstrich ( `/` ) ist die Anzahl von zusammenhängenden 1 Bit in der netmask. 

Beispielsweise verfügt 192.168.2.0/24 über eine zugeordnete net/Subnetmask, die 24 aufeinander folgende Bits oder 255.255.255.0 im gepunkteten Dezimal Format enthält.

## <a name="returns"></a>Gibt zurück

Das `ipv4_lookup` Plug-in gibt das Ergebnis von Join (Lookup) basierend auf dem IPv4-Schlüssel zurück. Das Schema der Tabelle ist die Vereinigung der Quell Tabelle und der Nachschlage Tabelle, ähnlich wie das Ergebnis des- [ `lookup` Operators](lookupoperator.md).

Wenn das *return_unmatched* -Argument auf festgelegt ist `true` , enthält die resultierende Tabelle sowohl übereinstimmende als auch nicht übereinstimmende Zeilen (mit Nullen gefüllt).

Wenn das *return_unmatched* -Argument auf festgelegt ist `false` oder ausgelassen wird (der Standardwert von `false` wird verwendet), enthält die resultierende Tabelle so viele Datensätze wie übereinstimmende Ergebnisse. Diese Variante der Suche bietet im Vergleich zur Ausführung eine bessere Leistung `return_unmatched=true` .

> [!NOTE]
> * Dieses Plug-in deckt das Szenario des IPv4-basierten Joins ab, wobei eine kleine Such Tabellengröße (100 KB-200K Zeilen) angenommen wird, wobei die Eingabe Tabelle optional eine größere Größe hat.
> * Die Leistung des Plug-ins hängt von den Größen der Such-und Datenquellen Tabellen, der Anzahl der Spalten und der Anzahl der übereinstimmenden Datensätze ab.

## <a name="examples"></a>Beispiele

### <a name="ipv4-lookup---matching-rows-only"></a>IPv4-Suche-nur übereinstimmende Zeilen

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// IP lookup table: IP_Data
// Partial data from: https://raw.githubusercontent.com/datasets/geoip2-ipv4/master/data/geoip2-ipv4.csv
let IP_Data = datatable(network:string, continent_code:string ,continent_name:string, country_iso_code:string, country_name:string)
[
  "111.68.128.0/17","AS","Asia","JP","Japan",
  "5.8.0.0/19","EU","Europe","RU","Russia",
  "223.255.254.0/24","AS","Asia","SG","Singapore",
  "46.36.200.51/32","OC","Oceania","CK","Cook Islands",
  "2.20.183.0/24","EU","Europe","GB","United Kingdom",
];
let IPs = datatable(ip:string)
[
  '2.20.183.12',   // United Kingdom
  '5.8.1.2',       // Russia
  '192.165.12.17', // Unknown
];
IPs
| evaluate ipv4_lookup(IP_Data, ip, network)
```

|ip|Netzwerk|continent_code|continent_name|country_iso_code|country_name|
|---|---|---|---|---|---|
|2.20.183.12|2.20.183.0/24|EU|Europa|GB|United Kingdom|
|5.8.1.2|5.8.0.0/19|EU|Europa|RU|Russland|

### <a name="ipv4-lookup---return-both-matching-and-non-matching-rows"></a>IPv4-Suche: Rückgabe von übereinstimmenden und nicht übereinstimmenden Zeilen

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// IP lookup table: IP_Data
// Partial data from: 
// https://raw.githubusercontent.com/datasets/geoip2-ipv4/master/data/geoip2-ipv4.csv
let IP_Data = datatable(network:string,continent_code:string ,continent_name:string ,country_iso_code:string ,country_name:string )
[
    "111.68.128.0/17","AS","Asia","JP","Japan",
    "5.8.0.0/19","EU","Europe","RU","Russia",
    "223.255.254.0/24","AS","Asia","SG","Singapore",
    "46.36.200.51/32","OC","Oceania","CK","Cook Islands",
    "2.20.183.0/24","EU","Europe","GB","United Kingdom",
];
let IPs = datatable(ip:string)
[
    '2.20.183.12',   // United Kingdom
    '5.8.1.2',       // Russia
    '192.165.12.17', // Unknown
];
IPs
| evaluate ipv4_lookup(IP_Data, ip, network, return_unmatched = true)
```

|ip|Netzwerk|continent_code|continent_name|country_iso_code|country_name|
|---|---|---|---|---|---|
|2.20.183.12|2.20.183.0/24|EU|Europa|GB|United Kingdom|
|5.8.1.2|5.8.0.0/19|EU|Europa|RU|Russland|
|192.165.12.17||||||

### <a name="ipv4-lookup---using-source-in-external_data"></a>IPv4-Suche-using Source in external_data ()

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let IP_Data = external_data(network:string,geoname_id:long,continent_code:string,continent_name:string ,country_iso_code:string,country_name:string,is_anonymous_proxy:bool,is_satellite_provider:bool)
    ['https://raw.githubusercontent.com/datasets/geoip2-ipv4/master/data/geoip2-ipv4.csv'];
let IPs = datatable(ip:string)
[
    '2.20.183.12',   // United Kingdom
    '5.8.1.2',       // Russia
    '192.165.12.17', // Sweden
];
IPs
| evaluate ipv4_lookup(IP_Data, ip, network, return_unmatched = true)
```

|ip|Netzwerk|geoname_id|continent_code|continent_name|country_iso_code|country_name|is_anonymous_proxy|is_satellite_provider|
|---|---|---|---|---|---|---|---|---|
|2.20.183.12|2.20.183.0/24|2635167|EU|Europa|GB|United Kingdom|0|0|
|5.8.1.2|5.8.0.0/19|2017370|EU|Europa|RU|Russland|0|0|
|192.165.12.17|192.165.8.0/21|2661886|EU|Europa|SE|Schweden|0|0|
