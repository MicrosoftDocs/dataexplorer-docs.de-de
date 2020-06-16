---
title: 'Löschen eines zwischengespeicherten Schemas für die streamingansung: Azure Daten-Explorer'
description: Dieser Artikel beschreibt den Verwaltungs Befehl zum Löschen des zwischengespeicherten Datenbankschemas in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/20/2020
ms.openlocfilehash: 23d86e528dfe687b79ce225b16712184a9bfcde4
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/15/2020
ms.locfileid: "84784496"
---
# <a name="clear-schema-cache-for-streaming-ingestion"></a>Schema Cache für streamingerfassung löschen

Cluster Knoten: Cache Schema der Datenbanken, die Daten über die streamingansung empfangen. Dieser Prozess optimiert die Leistung und Auslastung von Cluster Ressourcen, kann jedoch zu Weiterleitungs Verzögerungen bei der Schema Änderung führen.
Löschen Sie den Cache, um sicherzustellen, dass nachfolgende streamingzuerungsanforderungen Datenbank-oder Tabellen Schema Änderungen einschließen
Weitere Informationen finden Sie unter [streamingerfassung und Schema Änderungen](streaming-ingestion-schema-changes.md).

**Schema Cache löschen**

Der Befehl leert das `.clear cache streamingingestion schema` zwischengespeicherte Schema von allen Cluster Knoten.

**Syntax**

`.clear``table` &lt; &gt; Tabellen `cache` Name `streamingingestion``schema`

`.clear` `database` `cache` `streamingingestion` `schema`

**Rückgabe**

Dieser Befehl gibt eine Tabelle mit den folgenden Spalten zurück:

|Spalte    |Typ    |BESCHREIBUNG
|---|---|---
|NodeId|`string`|Bezeichner des Cluster Knotens
|Status|`string`|Erfolgreich/fehlerhaft

**Beispiel**

```kusto
.clear database cache streamingingestion schema

.show table T1 cache streamingingestion schema
```

|NodeId|Status|
|---|---|
|Knoten1|Erfolgreich
|Knoten2|Failed

> [!NOTE]
> Wenn der Befehl fehlschlägt oder eine der Zeilen in der zurückgegebenen Tabelle den *Status = failed* enthält, kann der Befehl sicher wiederholt werden.
