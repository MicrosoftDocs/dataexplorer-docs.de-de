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
ms.openlocfilehash: 9ddb94e005e88855bbeddd7d3cf8ab537a42d413
ms.sourcegitcommit: a8575e80c65eab2a2118842e59f62aee0ff0e416
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84943082"
---
# <a name="clear-query-results-cache"></a>Abfrageergebnis Cache löschen

Löschen Sie alle [zwischengespeicherten Abfrageergebnisse](../query/query-results-cache.md) , die für die Kontext Datenbank erstellt wurden.

**Syntax**

`.clear` `database` `cache` `queryresults`

**Rückgabe**

Dieser Befehl gibt eine Tabelle mit den folgenden Spalten zurück:

|Spalte    |Typ    |Beschreibung
|---|---|---
|NodeId|`string`|Der Bezeichner des Cluster Knotens.
|Gewinner|`long`|Die Anzahl von Einträgen, die vom Knoten gelöscht werden.

**Beispiel**

```kusto
.clear database cache queryresults
```

|NodeId|Gewinner|
|---|---|
|Knoten1|42
|Knoten2|0
