---
title: extractjson ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: Dieser Artikel beschreibt extractjson () in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9668b173c6b3769113972be2c74382464e7d9819
ms.sourcegitcommit: bc09599c282b20b5be8f056c85188c35b66a52e5
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88610575"
---
# <a name="extractjson"></a>extractjson()

Rufen Sie ein angegebenes Element aus einem JSON-Text mit einem Pfadausdruck ab. 

Konvertieren Sie optional die extrahierte Zeichenfolge in einen bestimmten Typ.

```kusto
extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))
```

## <a name="syntax"></a>Syntax

`extractjson(`*jsonpath* `,` *Daten* Quelle`)` 

## <a name="arguments"></a>Argumente

* *jsonpath*: jsonpath-Zeichenfolge, die einen Accessor im JSON-Dokument definiert.
* *DataSource*: ein JSON-Dokument.

## <a name="returns"></a>Gibt zurück

Diese Funktion führt eine JsonPath-Abfrage in dataSource durch, die eine gültige JSON-Zeichenfolge enthält. Optional wird dieser Wert, je nach drittem Argument, in einen anderen Typ konvertiert.

## <a name="example"></a>Beispiel

Die `[` Notation der Klammer `]` und die Punkt ( `.` )-Notation sind gleichwertig:

```kusto
T 
| extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) 

T
| extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) 
```

### <a name="json-path-expressions"></a>JSON Path-Ausdrücke

|Pfadausdruck|Beschreibung|
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