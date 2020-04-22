---
title: parse_xml() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird parse_xml() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ace60d0f9652d75b0f55cce4b1b622af20b42dc1
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663693"
---
# <a name="parse_xml"></a>parse_xml()

Interpretiert a `string` als XML-Wert, konvertiert den Wert in ein `dynamic`JSON und gibt den Wert als zurück.

**Syntax**

`parse_xml(`*xml*`)`

**Argumente**

* *xml*: Ein `string`Ausdruck vom Typ , der einen XML-formatierten Wert darstellt.

**Rückgabe**

Ein Objekt vom Typ [dynamic,](./scalar-data-types/dynamic.md) das durch den Wert *von xml*oder null bestimmt wird, wenn das XML-Format ungültig ist.

Das Konvertieren der XML-Datei in JSON erfolgt mithilfe der [xml2json-Bibliothek.](https://github.com/Cheedoong/xml2json)

Die Konvertierung erfolgt wie folgt:

XML                                |JSON                                            |Zugriff
-----------------------------------|------------------------------------------------|--------------         
`<e/>`                             | • "e": null                                  | o.e
`<e>text</e>`                      | " "e": "text"                                | o.e
`<e name="value" />`               | " "e":""@name": "value"                     | o.e["@name"]
`<e name="value">text</e>`         | " " "wert",@name"#text": "text" | o.e["@name"] o.e["#text"]
`<e> <a>text</a> <b>text</b> </e>` | - "e": "a": "text", "b": "text"          | o.e.a o.e.b
`<e> <a>text</a> <a>text</a> </e>` | " "e": "a": ["text", "text"]             | o.e.a[0] o.e.a[1]
`<e> text <a>text</a> </e>`        | " "e": "#text": "text", "a": "text"      | 1'o.e["#text"] o.e.a

**Hinweise**

* Die `string` maximale `parse_xml` Eingangslänge für 128 KB. Längere Zeichenfolgeninterpretation führt zu einem Nullobjekt 
* Nur Elementknoten, Attribute und Textknoten werden übersetzt. Alles andere wird übersprungen
 
**Beispiel**

Für das folgende Beispiel gilt: Wenn `context_custom_metrics` ein `string`-Element ist, das wie folgt aussieht:
<!--check this code formatting-->

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

Dann übersetzt das folgende CSL-Fragment den XML-Code in die folgende JSON:

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

und ruft den Wert `duration` des Steckplatzes im Objekt ab, `duration.value` und `duration.min` `118.0` von `110.0`diesem ruft er zwei Steckplätze bzw. ( bzw. ) ab.

```kusto
T
| extend d=parse_xml(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```