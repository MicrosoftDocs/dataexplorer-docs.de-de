---
title: 'Abfrageergebnis Cache löschen: Azure Daten-Explorer'
description: Erfahren Sie, wie Sie zwischengespeicherte Abfrageergebnisse in Azure Daten-Explorer löschen. Informieren Sie sich über den zu verwendenden Befehl und ein Beispiel.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 72678453211ada2a6366b771153eeb11a717d7a3
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002956"
---
# <a name="clear-query-results-cache"></a>Abfrageergebnis Cache löschen

Löschen Sie alle [zwischengespeicherten Abfrageergebnisse](../query/query-results-cache.md) , die für die Kontext Datenbank erstellt wurden.

**Syntax**

`.clear` `database` `cache` `query_results`

**Rückgabe**

Dieser Befehl gibt eine Tabelle mit den folgenden Spalten zurück:

|Spalte    |Typ    |Beschreibung
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
