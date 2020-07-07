---
title: 'Sharding-Richtlinien Verwaltung: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt die Sharding-Richtlinien Verwaltung in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fb4ff4ade5e3fb0e2f01de0adc74aecd27381a3d
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967467"
---
# <a name="sharding-policy-command"></a>Shardingrichtlinienbefehl

## <a name="show-policy"></a>Richtlinie anzeigen

```kusto
.show table [table_name] policy sharding

.show table * policy sharding

.show database [database_name] policy sharding
```

`Show`Richtlinie zeigt die Sharding-Richtlinie für die Datenbank oder Tabelle an. Alle Richtlinien des angegebenen Entitäts Typs (Datenbank oder Tabelle) werden angezeigt, wenn der angegebene Name "*" lautet.

### <a name="output"></a>Ausgabe

|Richtlinienname | Name der Entität | Policy | Untergeordnete Entitäten | Entitätstyp
|---|---|---|---|---
|Extentsshardingpolicy | Datenbank/Tabellenname | JSON-Format Zeichenfolge zur Darstellung der Richtlinie | Liste der Tabellen (für eine Datenbank)|Datenbank/Tabelle

## <a name="alter-policy"></a>Richtlinie ändern

### <a name="examples"></a>Beispiele

In den folgenden Beispielen wird die aktualisierte Blöcke-Sharding-Richtlinie für die-Entität zurückgegeben, wobei die Datenbank oder die Tabelle als qualifizierter Name als Ausgabe angegeben ist.

#### <a name="setting-all-properties-of-the-policy-explicitly-at-table-level"></a>Explizites Festlegen aller Eigenschaften der Richtlinie auf Tabellenebene

```kusto
.alter table [table_name] policy sharding 
@'{ "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048}'
```

#### <a name="setting-all-properties-of-the-policy-explicitly-at-database-level"></a>Explizites Festlegen aller Eigenschaften der Richtlinie auf Datenbankebene

```kusto
.alter database [database_name] policy sharding
@'{ "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048}'
```

#### <a name="setting-the-default-sharding-policy-at-database-level"></a>Festlegen der *Standard* -Sharding-Richtlinie auf Datenbankebene

```kusto
.alter database [database_name] policy sharding @'{}'
```

#### <a name="altering-a-single-property-of-the-policy-at-database-level"></a>Ändern einer einzelnen Eigenschaft der Richtlinie auf Datenbankebene 

Behalten Sie alle anderen Eigenschaften unverändert bei.

```kusto
.alter-merge database [database_name] policy sharding
@'{ "MaxExtentSizeInMb": 1024}'
```

#### <a name="altering-a-single-property-of-the-policy-at-table-level"></a>Ändern einer einzelnen Eigenschaft der Richtlinie auf Tabellenebene

Alle anderen Eigenschaften unverändert beibehalten

```kusto
.alter-merge table [table_name] policy sharding
@'{ "MaxRowCount": 750000}'
```

## <a name="delete-policy"></a>Richtlinie löschen

```kusto
.delete table [table_name] policy sharding

.delete database [database_name] policy sharding
```

Mit dem Befehl wird die aktuelle Sharding-Richtlinie für die angegebene Entität gelöscht.