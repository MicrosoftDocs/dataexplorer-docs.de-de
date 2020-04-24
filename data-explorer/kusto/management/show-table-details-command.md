---
title: Tabellendetails anzeigen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden Tabellendetails in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 6197e173df417acb1f5dc506337773f9534564bf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519787"
---
# <a name="show-table-details"></a>.Show Tabellendetails
Gibt einen Satz zurück, der die angegebene Tabelle oder alle Tabellen in der Datenbank mit einer detaillierten Zusammenfassung der Eigenschaften jeder Tabelle enthält.

Erfordert [Database Viewer-Berechtigung](../management/access-control/role-based-authorization.md).

```
.show table T1 details
.show tables (T1, ..., Tn) details
.show tables details
```

**Ausgabe**

| Ausgabeparameter           | type     | BESCHREIBUNG                                                                                     |
|----------------------------|----------|-------------------------------------------------------------------------------------------------|
| `TableName`                | String   | Der Name der Tabelle.                                                                          |
| `DatabaseName`             | String   | Die Datenbank, zu der die Tabelle gehört.                                                         |
| `Folder`                   | String   | Der Ordner der Tabelle.                                                                             |
| `DocString`                | String   | Eine Zeichenfolge, die die Tabelle dokumentiert.                                                                 |
| `TotalExtents`             | Int64    | Die Gesamtzahl der Ausdehnungen in der Tabelle.                                                       |
| `TotalExtentSize`          | Double   | Die Gesamtgröße der Ausdehnungen (komprimierte Größe + Indexgröße) in der Tabelle.                          |
| `TotalOriginalSize`        | Double   | Die ursprüngliche Gesamtgröße der Daten in der Tabelle.                                                   |
| `TotalRowCount`            | Int64    | Die Gesamtanzahl der Zeilen in der Tabelle.                                                          |
| `HotExtents`               | Int64    | Die Gesamtanzahl der Ausdehnungen in der Tabelle, die im hot cache gespeichert sind.                              |
| `HotExtentSize`            | Double   | Die Gesamtgröße der Ausdehnungen (komprimierte Größe + Indexgröße) in der Tabelle, die im hot cache gespeichert sind. |
| `HotOriginalSize`          | Double   | Die ursprüngliche Gesamtgröße der Daten in der Tabelle, die im Hot-Cache gespeichert sind.                          |
| `HotRowCount`              | Int64    | Die Gesamtzahl der Zeilen in der Tabelle, die im Hot Cache gespeichert sind.                                 |
| `AuthorizedPrincipals`     | String   | Die autorisierten Prinzipale der Tabelle, serialisiert als JSON.                                          |
| `RetentionPolicy`          | String   | Die effektive`*` Aufbewahrungsrichtlinie der Tabelle, serialisiert als JSON.                                  |
| `CachingPolicy`            | String   | Die effektive`*` Caching-Richtlinie der Tabelle, serialisiert als JSON.                                    |
| `ShardingPolicy`           | String   | Die effektive`*` Sharding-Richtlinie der Tabelle, serialisiert als JSON.6666666666666666666666                     |
| `MergePolicy`              | String   | Die effektive`*` Mergerichtlinie der Tabelle, serialisiert als JSON.                                      |
| `StreamingIngestionPolicy` | String   | Die effektive`*` Streaming-Erfassungsrichtlinie der Tabelle, serialisiert als JSON.                        |
| `MinExtentsCreationTime`   | Datetime | Die minimale Erstellungszeit einer Ausdehnung in der Tabelle (oder NULL, wenn keine Ausdehnungen vorhanden sind).         |
| `MaxExtentsCreationTime`   | Datetime | Die maximale Erstellungszeit einer Ausdehnung in der Tabelle (oder NULL, wenn keine Ausdehnungen vorhanden sind).         |
| `RowOrderPolicy`           | String   | Die effektive Zeilenreihenfolgerichtlinie der Tabelle, serialisiert als JSON.                                     |

`*`*Unter Berücksichtigung von Richtlinien übergeordneter Entitäten (z. B. Datenbank/Cluster).*

**Ausgabebeispiel**

| TableName         | DatabaseName | Ordner | DocString | TotalExtents | TotalExtentSize | TotalOriginalSize | TotalRowCount | HotExtents | HotExtentSize | HotOriginalSize | HotRowCount | AuthorizedPrincipals                                                                                                                                                                               | RetentionPolicy                                                                                                                                       | CachingPolicy                                                                        | Sharding-Richtlinie                                                                    | MergePolicy                                                                                                                                             | StreamingIngestionPolicy | MinExtentsCreationTime      | MaxExtentsCreationTime      |
|-------------------|--------------|--------|-----------|--------------|-----------------|-------------------|---------------|------------|---------------|-----------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------|-----------------------------|-----------------------------|
| Operationen (Operations)        | Operationen (Operations)   |        |           | 1164         | 37687203        | 53451358          | 223325        | 29         | 838752        | 1388213         | 5117        | "Typ": "AAD User", "DisplayName": "Mein Name alias@fabrikam.com(upn: )", "ObjectId": "a7a77777-4c21-4649-95c5-350bf486 087b", "FQN": "aaduser=a7a777777-4c21-4649-95c5-350bf486087b", "Notes": ""]] | "SoftDeletePeriod": "365.00:00:00", "ContainerRecyclingPeriod": "1.00:00:00", "ExtentsDataSizeLimitInBytes": 0, "OriginalDataSizeLimitInBytes": 0  | • "DataHotSpan": "4.00:00:00", "IndexHotSpan": "4.00:00:00", "ColumnOverrides": [] | • "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048 | • "RowCountUpperboundForMerge": 0, "MaxExtentsToMerge": 100, "LoopPeriod": "01:00:00", "MaxRangeInHours": 3, "AllowRebuild": true, "AllowMerge": true | NULL                     |
| ServiceOperations | Operationen (Operations)   |        |           | 1109         | 76588803        | 91553069          | 110125        | 27         | 2635742       | 2929926         | 3162        | "Typ": "AAD User", "DisplayName": "Mein Name alias@fabrikam.com(upn: )", "ObjectId": "a7a77777-4c21-4649-95c5-350bf486 087b", "FQN": "aaduser=a7a777777-4c21-4649-95c5-350bf486087b", "Notes": ""]] | • "SoftDeletePeriod": "365.00:00:00", "ContainerRecyclingPeriod": "1.00:00:00", "ExtentsDataSizeLimitInBytes": 0, "OriginalDataSizeLimitInBytes": 0 | • "DataHotSpan": "4.00:00:00", "IndexHotSpan": "4.00:00:00", "ColumnOverrides": [] | • "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048 | • "RowCountUpperboundForMerge": 0, "MaxExtentsToMerge": 100, "LoopPeriod": "01:00:00", "MaxRangeInHours": 3, "AllowRebuild": true, "AllowMerge": true | NULL                     | 2018-02-08 15:30:38.8489786 | 2018-02-14 07:47:28.7660267 |