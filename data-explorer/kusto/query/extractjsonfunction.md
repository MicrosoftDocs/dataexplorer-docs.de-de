---
title: extractjson() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird extractjson() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6177a1c8a6ed4390093e6f6fd24c5f5e9fd04f8a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515333"
---
# <a name="extractjson"></a>extractjson()

Rufen Sie ein angegebenes Element aus einem JSON-Text mit einem Pfadausdruck ab. 

Konvertieren Sie optional die extrahierte Zeichenfolge in einen bestimmten Typ.

```kusto
extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))
```

**Syntax**

`extractjson(`*jsonPath* `,` *dataSource*`)` 

**Argumente**

* *jsonPath*: JsonPath-Zeichenfolge, die einen Accessor im JSON-Dokument definiert.
* *dataSource*: Ein JSON-Dokument.

**Rückgabe**

Diese Funktion führt eine JsonPath-Abfrage in dataSource durch, die eine gültige JSON-Zeichenfolge enthält. Optional wird dieser Wert, je nach drittem Argument, in einen anderen Typ konvertiert.

**Beispiel**

Die `[``]` Klammernotatation und`.`die Punktnotation ( ) sind äquivalent:

```kusto
T 
| extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) 

T
| extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) 
```

### <a name="json-path-expressions"></a>JSON Path-Ausdrücke

|||
|---|---|
|`$`|Stammobjekt|
|`@`|Aktuelles Objekt|
|`.` oder `[ ]` | Untergeordnet|
|`[ ]`|Array-Subscript|

*(Derzeit werden keine Platzhalter, Rekursionen, Vereinigungen oder Segmente implementiert.)*


**Leistungstipps**

* Wenden Sie vor der Verwendung von `extractjson()`
* Erwägen Sie stattdessen den Abgleich mit einem regulären Ausdruck mit [extract](extractfunction.md) . Dies kann sehr viel schneller ausgeführt werden und ist effektiv, wenn die JSON aus einer Vorlage erstellt wird.
* Verwenden Sie `parse_json()` , wenn Sie mehr als einen Wert aus dem JSON-Code extrahieren müssen.
* Ziehen Sie es in Betracht, die JSON zum Zeitpunkt der Erfassung zu analysieren, indem Sie den Typ der Spalte als dynamisch deklarieren.