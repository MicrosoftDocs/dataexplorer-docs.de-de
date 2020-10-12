---
title: parse_xml ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird parse_xml () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3f003c5e9c6733391d61a2130528c9babc4aae67
ms.sourcegitcommit: d157e661de293aa4c2b5ad334a554eda0295bd2c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91886297"
---
# <a name="parse_xml"></a>parse_xml()

Interpretiert einen `string` als XML-Wert, konvertiert den Wert in einen JSON-Wert und gibt den Wert als zurück `dynamic` .

## <a name="syntax"></a>Syntax

`parse_xml(`*xml*`)`

## <a name="arguments"></a>Argumente

* *XML*: ein Ausdruck vom Typ `string` , der einen XML-formatierten Wert darstellt.

## <a name="returns"></a>Gibt zurück

Ein Objekt vom Typ " [Dynamic](./scalar-data-types/dynamic.md) ", das durch den Wert von *XML*bestimmt wird, oder NULL, wenn das XML-Format ungültig ist.

Die Konvertierung erfolgt wie folgt:

XML                                |JSON                                            |Zugriff
-----------------------------------|------------------------------------------------|--------------         
`<e/>`                             | {"e": NULL}                                  | o. e
`<e>text</e>`                      | {"e": "Text"}                                | o. e
`<e name="value" />`               | {"e": {" @name ": "Value"}}                     | o. e [" @name "]
`<e name="value">text</e>`         | {"e": {" @name ": "Value", "#Text": "Text"}} | o. e [" @name "] o. e ["#Text"]
`<e> <a>text</a> <b>text</b> </e>` | {"e": {"a": "Text", "b": "Text"}}          | o. e. a o. e. b
`<e> <a>text</a> <a>text</a> </e>` | {"e": {"a": ["Text", "Text"]}}             | o. e. a [0] o. e. a [1]
`<e> text <a>text</a> </e>`        | {"e": {"#Text": "Text", "a": "Text"}}      | 1 ' o. e ["#Text"] o. e. a

**Hinweise**

* Die maximale Eingabe `string` Länge für `parse_xml` beträgt 1 MB (1.048.576 Byte). Die Interpretation längerer Zeichen folgen führt zu einem NULL-Objekt.
* Nur Elementknoten, Attribute und Textknoten werden übersetzt. Alles andere wird übersprungen.
 
## <a name="example"></a>Beispiel

Für das folgende Beispiel gilt: Wenn `context_custom_metrics` ein `string`-Element ist, das wie folgt aussieht: 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<duration>
    <value>118.0</value>
    <count>5.0</count>
    <min>100.0</min>
    <max>150.0</max>
    <stdDev>0.0</stdDev>
    <sampledValue>118.0</sampledValue>
    <sum>118.0</sum>
</duration>
```

anschließend übersetzt das folgende CSL-Fragment den XML-Code in den folgenden JSON-Code:

```json
{
    "duration": {
        "value": 118.0,
        "count": 5.0,
        "min": 100.0,
        "max": 150.0,
        "stdDev": 0.0,
        "sampledValue": 118.0,
        "sum": 118.0
    }
}
```

und ruft den Wert des `duration` Slots im-Objekt ab, von dem zwei Slots `duration.value` `duration.min` ( `118.0` `100.0` bzw. bzw.) abgerufen werden.

```kusto
T
| extend d=parse_xml(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```
