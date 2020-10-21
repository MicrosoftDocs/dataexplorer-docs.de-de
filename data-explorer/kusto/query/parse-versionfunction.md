---
title: parse_version ()-Azure Daten-Explorer
description: In diesem Artikel wird parse_version () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cae7fe9a46d885555f1b28d9282962319fd51b87
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246239"
---
# <a name="parse_version"></a>parse_version()

Konvertiert die Darstellung der Eingabe Zeichenfolge von Version in eine vergleichbare Dezimalzahl.

```kusto
parse_version("0.0.0.1")
```

## <a name="syntax"></a>Syntax

`parse_version``(` *Expr*`)`

## <a name="arguments"></a>Argumente

* *`Expr`*: Ein Skalarausdruck vom Typ `string` , der die Version angibt, die analysiert werden soll.

> [!NOTE]
> * Die Eingabe Zeichenfolge muss eine bis vier Versions Bestandteile enthalten, die als Zahlen dargestellt und durch Punkte (".") getrennt sind.
> * Jeder Teil der Version kann bis zu acht Ziffern mit dem maximalen Wert bei 99999999 enthalten.
> * Wenn die Anzahl der Teile kleiner als vier ist, werden alle fehlenden Teile als nachfolgende ( `1.0`  ==  `1.0.0.0` ) betrachtet.

## <a name="returns"></a>Rückgabe

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis ein Dezimaltrennzeichen.
Wenn die Konvertierung nicht erfolgreich ist, lautet das Ergebnis `null` .

## <a name="example"></a>Beispiel

```kusto
let dt = datatable(v:string)
["0.0.0.5","0.0.7.0","0.0.3","0.2","0.1.2.0","1.2.3.4","1","99999999.0.0.0"];
dt | project v1=v, _key=1 
| join kind=inner (dt | project v2=v, _key = 1) on _key | where v1 != v2
| summarize v1 = max(v1),v2 = min(v2) by (hash(v1) + hash(v2)) // removing duplications
| project v1, v2, higher_version = iif(parse_version(v1) > parse_version(v2), v1, v2)

```

|v1|V2|higher_version|
|---|---|---|
|99999999.0.0.0|0.0.0.5|99999999.0.0.0|
|1|0.0.0.5|1|
|1.2.3.4|0.0.0.5|1.2.3.4|
|0.1.2.0|0.0.0.5|0.1.2.0|
|0.2|0.0.0.5|0.2|
|0.0.3|0.0.0.5|0.0.3|
|0.0.7.0|0.0.0.5|0.0.7.0|
|99999999.0.0.0|0.0.7.0|99999999.0.0.0|
|1|0.0.7.0|1|
|1.2.3.4|0.0.7.0|1.2.3.4|
|0.1.2.0|0.0.7.0|0.1.2.0|
|0.2|0.0.7.0|0.2|
|0.0.7.0|0.0.3|0.0.7.0|
|99999999.0.0.0|0.0.3|99999999.0.0.0|
|1|0.0.3|1|
|1.2.3.4|0.0.3|1.2.3.4|
|0.1.2.0|0.0.3|0.1.2.0|
|0.2|0.0.3|0.2|
|99999999.0.0.0|0.2|99999999.0.0.0|
|1|0.2|1|
|1.2.3.4|0.2|1.2.3.4|
|0.2|0.1.2.0|0.2|
|99999999.0.0.0|0.1.2.0|99999999.0.0.0|
|1|0.1.2.0|1|
|1.2.3.4|0.1.2.0|1.2.3.4|
|99999999.0.0.0|1.2.3.4|99999999.0.0.0|
|1.2.3.4|1|1.2.3.4|
|99999999.0.0.0|1|99999999.0.0.0|
