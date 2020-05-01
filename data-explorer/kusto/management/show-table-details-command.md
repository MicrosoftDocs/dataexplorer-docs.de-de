---
title: '. Anzeigen von Tabellen Details: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird beschrieben, wie Sie Tabellen Details in Azure Daten-Explorer anzeigen.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 3182c059a3f56b94950009672759388bbff8058d
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616914"
---
# <a name="show-table-details"></a>.show table details
Gibt eine Menge zurück, die die angegebene Tabelle oder alle Tabellen in der Datenbank enthält und eine ausführliche Zusammenfassung der Eigenschaften der Tabelle enthält.

Erfordert die [Database Viewer-Berechtigung](../management/access-control/role-based-authorization.md).

```kusto
.show table T1 details
.show tables (T1, ..., Tn) details
.show tables details
```

**Ausgabe**

| Output-Parameter           | type     | BESCHREIBUNG                                                                                     |
|----------------------------|----------|-------------------------------------------------------------------------------------------------|
| `TableName`                | String   | Der Name der Tabelle.                                                                          |
| `DatabaseName`             | String   | Die Datenbank, zu der die Tabelle gehört.                                                         |
| `Folder`                   | String   | Der Ordner der Tabelle.                                                                             |
| `DocString`                | String   | Eine Zeichenfolge, die die Tabelle dokumentiert.                                                                 |
| `TotalExtents`             | Int64    | Die Gesamtanzahl der Blöcke in der Tabelle.                                                       |
| `TotalExtentSize`          | Double   | Die Gesamtgröße der Blöcke (komprimierte Größe + Indexgröße) in der Tabelle.                          |
| `TotalOriginalSize`        | Double   | Die Gesamtgröße der Daten in der Tabelle.                                                   |
| `TotalRowCount`            | Int64    | Die Gesamtanzahl der Zeilen in der Tabelle.                                                          |
| `HotExtents`               | Int64    | Die Gesamtanzahl der Blöcke in der Tabelle, die im aktiven Cache gespeichert werden.                              |
| `HotExtentSize`            | Double   | Die Gesamtgröße der Blöcke (komprimierte Größe + Indexgröße) in der Tabelle, die im aktiven Cache gespeichert ist. |
| `HotOriginalSize`          | Double   | Die Gesamtgröße der Daten in der Tabelle, die im aktiven Cache gespeichert ist.                          |
| `HotRowCount`              | Int64    | Die Gesamtanzahl der Zeilen in der Tabelle, die im aktiven Cache gespeichert ist.                                 |
| `AuthorizedPrincipals`     | String   | Die autorisierten Prinzipale der Tabelle, die als JSON serialisiert werden.                                          |
| `RetentionPolicy`          | String   | Die effektive`*` Beibehaltungs Richtlinie der Tabelle, die als JSON serialisiert ist.                                  |
| `CachingPolicy`            | String   | Die effektive`*` Cachingrichtlinie der Tabelle, die als JSON serialisiert ist.                                    |
| `ShardingPolicy`           | String   | Die effektive`*` Sharding-Richtlinie der Tabelle, die als JSON. 66666666666666 serialisiert wurde.                     |
| `MergePolicy`              | String   | Die effektive`*` Merge-Richtlinie der Tabelle, die als JSON serialisiert ist.                                      |
| `StreamingIngestionPolicy` | String   | Die effektive`*` streamingingrichtlinie der Tabelle, die als JSON serialisiert ist.                        |
| `MinExtentsCreationTime`   | Datetime | Die minimale Erstellungszeit eines Wertebereichs in der Tabelle (oder NULL, wenn keine Blöcke vorhanden sind).         |
| `MaxExtentsCreationTime`   | Datetime | Die maximale Erstellungszeit eines Wertebereichs in der Tabelle (oder NULL, wenn keine Blöcke vorhanden sind).         |
| `RowOrderPolicy`           | String   | Die effektive Zeilen Reihen folgen Richtlinie der Tabelle, die als JSON serialisiert ist.                                     |

`*`Berücksichtigen *von Richtlinien für übergeordnete Entitäten (z. b. Datenbank/Cluster).*

**Ausgabe Beispiel**

| TableName         | DatabaseName | Ordner | DocString | Totalextents | Totalextentsize | Totaloriginalsize | TotalRowCount | Hortenzelte | "Hutextentsize" | Hotoriginalsize | "Hatrowcount" | Authorizedprincipals                                                                                                                                                                               | RetentionPolicy                                                                                                                                       | Cachingpolicy                                                                        | Shardingpolicy                                                                    | Mergepolicy                                                                                                                                             | Streamingingestionpolicy | Minextentscreationtime      | Maxextentscreationtime      |
|-------------------|--------------|--------|-----------|--------------|-----------------|-------------------|---------------|------------|---------------|-----------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------|-----------------------------|-----------------------------|
| Operationen (Operations)        | Operationen (Operations)   |        |           | 1164         | 37687203        | 53451358          | 223325        | 29         | 838752        | 1388213         | 5117        | [{"Type": "Aad User", "Display Name": "My Name (UPN: alias@fabrikam.com)", "ObjectID": "a7a77777-4c21-4649-95c5-350bf486087b", "vollständig verfügbar": "aaduser = a7a77777-4c21-4649-95c5-350bf486087b", "Notes": ""}] | {"Software Update Period": "365.00:00:00", "containerrecyclingperiod": "1,00:00:00", "extentsdatasizelimitinbytes": 0, "originaldatasizelimitinbytes": 0}  | {"Datahotspan": "4.00:00:00", "indexhotspan": "4.00:00:00", "columnoverrides": []} | {"Maxrowcount": 750000, "maxextentsizeinmb": 1024, "maxoriginalsizeinmb": 2048} | {"Rowmenttupperboundformerge": 0, "maxextentstomerge": 100, "loopperiod": "01:00:00", "maxrangeinhours": 3, "Zustellungs Build": true, "AllowMerge": true} | NULL                     |
| ServiceOperations | Operationen (Operations)   |        |           | 1109         | 76588803        | 91553069          | 110125        | 27         | 2635742       | 2929926         | 3162        | [{"Type": "Aad User", "Display Name": "My Name (UPN: alias@fabrikam.com)", "ObjectID": "a7a77777-4c21-4649-95c5-350bf486087b", "vollständig verfügbar": "aaduser = a7a77777-4c21-4649-95c5-350bf486087b", "Notes": ""}] | {"Software Update Period": "365.00:00:00", "containerrecyclingperiod": "1,00:00:00", "extentsdatasizelimitinbytes": 0, "originaldatasizelimitinbytes": 0} | {"Datahotspan": "4.00:00:00", "indexhotspan": "4.00:00:00", "columnoverrides": []} | {"Maxrowcount": 750000, "maxextentsizeinmb": 1024, "maxoriginalsizeinmb": 2048} | {"Rowmenttupperboundformerge": 0, "maxextentstomerge": 100, "loopperiod": "01:00:00", "maxrangeinhours": 3, "Zustellungs Build": true, "AllowMerge": true} | NULL                     | 2018-02-08 15:30:38.8489786 | 2018-02-14 07:47:28.7660267 |
