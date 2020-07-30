---
title: 'Abfrageergebnis Cache löschen: Azure Daten-Explorer'
description: Dieser Artikel beschreibt den Verwaltungs Befehl zum Löschen des zwischengespeicherten Datenbankschemas in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 27806155d105a109c7419d04d945fbc854c44286
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349923"
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
