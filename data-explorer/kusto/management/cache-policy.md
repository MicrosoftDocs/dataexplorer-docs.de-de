---
title: Cache-Richtlinie - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Cacherichtlinie in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: ca14703b2548bdb23dc3e6e352aeaacbc17303b4
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744471"
---
# <a name="cache-policy"></a>Cacherichtlinie

In diesem Artikel werden Befehle beschrieben, die zum Erstellen und Ändern von [Cacherichtlinien](cachepolicy.md) verwendet werden. 

## <a name="displaying-the-cache-policy"></a>Anzeigen der Cacherichtlinie

Die Richtlinie kann für eine Daten oder eine Tabelle festgelegt werden und wird mit einem der folgenden Befehle angezeigt:

* `.show``database` *Datenbankname* `policy``caching`
* `.show``table` *DatabaseName-Tabellenname*`.`*TableName* `policy``caching`

## <a name="altering-the-cache-policy"></a>Ändern der Cacherichtlinie

```kusto
.alter <entity_type> <database_or_table_name> policy caching hot = <timespan>
```

Ändern der Cacherichtlinie für mehrere Tabellen (im gleichen Datenbankkontext):

```kusto
.alter tables (table_name [, ...]) policy caching hot = <timespan>
```

Cache-Richtlinie:

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

* `entity_type`: Tabelle, Datenbank oder Cluster
* `database_or_table`: Wenn Entität Tabelle oder Datenbank ist, sollte ihr Name im Befehl wie folgt angegeben werden: 
  - `database_name` oder 
  - `database_name.table_name` oder 
  - `table_name`(bei Ausführung im Kontext der spezifischen Datenbank)

## <a name="deleting-the-cache-policy"></a>Löschen der Cacherichtlinie

```kusto
.delete <entity_type> <database_or_table_name> policy caching
```

**Beispiele**

Cacherichtlinie für `MyTable` Tabelle `MyDatabase`in der Datenbank anzeigen:

```kusto
.show table MyDatabase.MyTable policy caching 
```

Festlegen der Cacherichtlinie der Tabelle `MyTable` (im Datenbankkontext) auf 3 Tage:

```kusto
.alter table MyTable policy caching hot = 3d
```

Festlegen der Richtlinie für mehrere Tabellen (im Datenbankkontext) auf 3 Tage:

```kusto
.alter tables (MyTable1, MyTable2, MyTable3) policy caching hot = 3d
```

Löschen eines Richtliniensatzes für eine Tabelle:

```kusto
.delete table MyTable policy caching
```

Löschen eines Richtliniensatzes für eine Datenbank:

```kusto
.delete database MyDatabase policy caching
```
