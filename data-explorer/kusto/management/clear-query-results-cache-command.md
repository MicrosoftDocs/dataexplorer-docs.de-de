---
title: 'Abfrageergebnis Cache löschen: Azure Daten-Explorer'
description: Dieser Artikel beschreibt den Verwaltungs Befehl zum Löschen des zwischengespeicherten Datenbankschemas in Azure Daten-Explorer.
services: data-explorer
author: amitof
ms.author: amitof
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 68d6493176696f0241467303f166b8c7160859b7
ms.sourcegitcommit: cf1da667be12656a8c4727c23144421b5a4b1099
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/21/2020
ms.locfileid: "86565441"
---
# <a name="clear-query-results-cache"></a>Abfrageergebnis Cache löschen

Löschen Sie alle [zwischengespeicherten Abfrageergebnisse](../query/query-results-cache.md) , die für die Kontext Datenbank erstellt wurden.

**Syntax**

`.clear` `database` `cache` `query_results`

**Rückgabe**

Dieser Befehl gibt eine Tabelle mit den folgenden Spalten zurück:

|Spalte    |Typ    |BESCHREIBUNG
|---|---|---
|NodeId|`string`|Der Bezeichner des Cluster Knotens.
|Anzahl|`long`|Die Anzahl von Einträgen, die vom Knoten gelöscht wurden.

**Beispiel**

```kusto
.clear database cache queryresults
```

|NodeId|Gewinner|
|---|---|
|Knoten1|42
|Knoten2|0
