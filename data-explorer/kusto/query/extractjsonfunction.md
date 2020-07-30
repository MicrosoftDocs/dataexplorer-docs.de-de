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
ms.openlocfilehash: 21f366e8f211acf9c7c542b420006cfb23e683e7
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348036"
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

## <a name="returns"></a>Rückgabe

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