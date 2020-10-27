---
title: 'Cache Richtlinie: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt die Cache Richtlinie in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 31d051bf104778ecc4c2a743db6bda5ccf8ed89f
ms.sourcegitcommit: ef3d919dee27c030842abf7c45c9e82e6e8350ee
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92630125"
---
# <a name="cache-policy-command"></a>Cacherichtlinienbefehl

In diesem Artikel werden Befehle zum Erstellen und Ändern von [Cache Richtlinien](cachepolicy.md) beschrieben. 

## <a name="displaying-the-cache-policy"></a>Anzeigen der Cache Richtlinie

Die Richtlinie kann für eine Datenbank, eine Tabelle oder eine [materialisierte Sicht](materialized-views/materialized-view-overview.md)festgelegt werden. Sie wird mit einem der folgenden Befehle angezeigt:

* `.show``database` *DatabaseName* `policy``caching`
* `.show``table` *TableName* `policy``caching`
* `.show``materialized-view` *Materializedviewname* `policy``caching`

## <a name="altering-the-cache-policy"></a>Ändern der Cache Richtlinie

```kusto
.alter <entity_type> <database_or_table_or_materialized-view_name> policy caching hot = <timespan>
```

Ändern der Cache Richtlinie für mehrere Tabellen (im selben Daten Bank Kontext):

```kusto
.alter tables (table_name [, ...]) policy caching hot = <timespan>
```

Cache Richtlinie:

```kusto
{
  "DataHotSpan": {
    "Value": "3.00:00:00"
  },
  "IndexHotSpan": {
    "Value": "3.00:00:00"
  }
}
```

* `entity_type` : Tabelle, materialisierte Sicht, Datenbank oder Cluster
* `database_or_table_or_materialized-view`: Wenn die Entität eine Tabelle oder Datenbank ist, sollte Ihr Name wie folgt im Befehl angegeben werden: 
  - `database_name` oder 
  - `database_name.table_name` oder 
  - `table_name` (bei Ausführung im Kontext einer bestimmten Datenbank)

## <a name="deleting-the-cache-policy"></a>Löschen der Cache Richtlinie

```kusto
.delete <entity_type> <database_or_table_name> policy caching
```

**Beispiele**

Cache Richtlinie für Tabelle `MyTable` in Datenbank anzeigen `MyDatabase` :

```kusto
.show table MyDatabase.MyTable policy caching 
```

Cache Richtlinie der Tabelle `MyTable` (im Daten Bank Kontext) wird auf 3 Tage festgelegt:

```kusto
.alter table MyTable policy caching hot = 3d
.alter materialized-view MyMaterializedView policy caching hot = 3d
```

Festlegen der Richtlinie für mehrere Tabellen (im Daten Bank Kontext) auf 3 Tage:

```kusto
.alter tables (MyTable1, MyTable2, MyTable3) policy caching hot = 3d
```

Löschen einer für eine Tabelle festgelegten Richtlinie:

```kusto
.delete table MyTable policy caching
```

Löschen einer Richtlinie, die für eine materialisierte Sicht festgelegt wurde:

```kusto
.delete materialized-view MyMaterializedView policy caching
```

Löschen einer für eine Datenbank festgelegten Richtlinie:

```kusto
.delete database MyDatabase policy caching
```
