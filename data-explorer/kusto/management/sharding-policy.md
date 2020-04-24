---
title: Sharding-Richtlinienverwaltung - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Verwaltung von Sharding-Richtlinien in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7770e0834e4b00f42158732e667d41eb636cec5b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520042"
---
# <a name="sharding-policy-management"></a>Sharding-Richtlinienverwaltung

## <a name="show-policy"></a>Show-Politik

```kusto
.show table [table_name] policy sharding

.show table * policy sharding

.show database [database_name] policy sharding
```

`Show`Richtlinie zeigt die Sharding-Richtlinie für die Datenbank oder Tabelle an. Es zeigt alle Richtlinien des angegebenen Entitätstyps (Datenbank oder Tabelle) an, wenn der angegebene Name '*' ist.

### <a name="output"></a>Output

|Richtlinienname | Name der Entität | Richtlinie | Untergeordnete Entitäten | Entitätstyp
|---|---|---|---|---
|ExtentsShardingPolicy | Datenbank - Tabellenname | json-Formatzeichenfolge, die die Richtlinie darstellt | Liste der Tabellen (für eine Datenbank)|Datenbank / Tabelle

## <a name="alter-policy"></a>Änderungspolitik

### <a name="examples"></a>Beispiele

In den folgenden Beispielen wird die aktualisierte Shardingrichtlinie für die Entität zurückgegeben, wobei die Datenbank oder Tabelle als qualifizierter Name als Ausgabe angegeben ist.

#### <a name="setting-all-properties-of-the-policy-explicitly-at-table-level"></a>Festlegen aller Eigenschaften der Richtlinie explizit auf Tabellenebene

```kusto
.alter table [table_name] policy sharding 
@'{ "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048}'
```

#### <a name="setting-all-properties-of-the-policy-explicitly-at-database-level"></a>Festlegen aller Eigenschaften der Richtlinie explizit auf Datenbankebene

```kusto
.alter database [database_name] policy sharding
@'{ "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048}'
```

#### <a name="setting-the-default-sharding-policy-at-database-level"></a>Festlegen der *Standard-Shardrichtlinie* auf Datenbankebene

```kusto
.alter database [database_name] policy sharding @'{}'
```

#### <a name="altering-a-single-property-of-the-policy-at-database-level"></a>Ändern einer einzelnen Eigenschaft der Richtlinie auf Datenbankebene 

Behalten Sie alle anderen Eigenschaften bei.

```kusto
.alter-merge database [database_name] policy sharding
@'{ "MaxExtentSizeInMb": 1024}'
```

#### <a name="altering-a-single-property-of-the-policy-at-table-level"></a>Ändern einer einzelnen Eigenschaft der Richtlinie auf Tabellenebene

Halten Sie alle anderen Eigenschaften so, wie sie sind

```kusto
.alter-merge table [table_name] policy sharding
@'{ "MaxRowCount": 750000}'
```

## <a name="delete-policy"></a>Richtlinie löschen

```kusto
.delete table [table_name] policy sharding

.delete database [database_name] policy sharding
```

Der Befehl löscht die aktuelle Sharding-Richtlinie für die angegebene Entität.