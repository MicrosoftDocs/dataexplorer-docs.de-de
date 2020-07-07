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
ms.openlocfilehash: 319a71e5db7019ed28001f44a1d4a4bcb21984e9
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967246"
---
# <a name="cache-policy-command"></a>Cacherichtlinienbefehl

In diesem Artikel werden Befehle zum Erstellen und Ändern von [Cache Richtlinien](cachepolicy.md) beschrieben. 

## <a name="displaying-the-cache-policy"></a>Anzeigen der Cache Richtlinie

Die Richtlinie kann für Daten oder Tabellen festgelegt werden und wird mit einem der folgenden Befehle angezeigt:

* `.show` `database` *DatabaseName* `policy` `caching`
* `.show``table` *DatabaseName*( `.` *Tabellenname* `policy` )`caching`

## <a name="altering-the-cache-policy"></a>Ändern der Cache Richtlinie

```kusto
.alter <entity_type> <database_or_table_name> policy caching hot = <timespan>
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

* `entity_type`: Tabelle, Datenbank oder Cluster
* `database_or_table`: Wenn die Entität eine Tabelle oder Datenbank ist, sollte Ihr Name wie folgt im Befehl angegeben werden: 
  - `database_name` oder 
  - `database_name.table_name` oder 
  - `table_name`(bei Ausführung im Kontext einer bestimmten Datenbank)

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
```

Festlegen der Richtlinie für mehrere Tabellen (im Daten Bank Kontext) auf 3 Tage:

```kusto
.alter tables (MyTable1, MyTable2, MyTable3) policy caching hot = 3d
```

Löschen einer für eine Tabelle festgelegten Richtlinie:

```kusto
.delete table MyTable policy caching
```

Löschen einer für eine Datenbank festgelegten Richtlinie:

```kusto
.delete database MyDatabase policy caching
```
