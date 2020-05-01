---
title: 'Kusto-Beibehaltungs Richtlinien Verwaltung: Azure Daten-Explorer'
description: Dieser Artikel beschreibt die Aufbewahrungs Richtlinie in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: e03e529e0c802f0d424deb4048c5809bbe845ddd
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617407"
---
# <a name="retention-policy"></a>Aufbewahrungsrichtlinie

In diesem Artikel werden Steuerungsbefehle beschrieben, die zum Erstellen und Ändern der [Aufbewahrungs Richtlinie](retentionpolicy.md)verwendet werden.

## <a name="show-retention-policy"></a>Aufbewahrungs Richtlinie anzeigen

```kusto
.show <entity_type> <database_or_table> policy retention

.show <entity_type> *  policy retention
```

* `entity_type`: Tabelle oder Datenbank
* `database_or_table`: `database_name` oder `database_name.table_name` oder `table_name` (im Daten Bank Kontext)

**Beispiel**

Die Beibehaltungs Richtlinie für die Datenbank mit `MyDatabase`dem Namen anzeigen:

```kusto
.show database MyDatabase policy retention
```

## <a name="delete-retention-policy"></a>Aufbewahrungs Richtlinie löschen

Durch das Löschen der Daten Aufbewahrungs Richtlinie wird eine unbegrenzte Daten Aufbewahrung festgelegt.

Das Löschen der Daten Aufbewahrungs Richtlinie der Tabelle führt dazu, dass die Tabelle die Aufbewahrungs Richtlinie von der Datenbankebene ableitet.

```kusto
.delete <entity_type> <database_or_table> policy retention
```

* `entity_type`: Tabelle oder Datenbank
* `database_or_table`: `database_name` oder `database_name.table_name` oder `table_name` (im Daten Bank Kontext)

**Beispiel**

Löschen Sie die Beibehaltungs Richtlinie für die `MyTable1`Tabelle mit dem Namen:

```kusto
.delete table MyTable policy retention
```


## <a name="alter-retention-policy"></a>Aufbewahrungs Richtlinie ändern

```kusto
.alter <entity_type> <database_or_table> policy retention <retention_policy>

.alter tables (<table_name> [, ...]) policy retention <retention_policy>

.alter-merge <entity_type> <database_or_table> policy retention <retention_policy>

.alter-merge <entity_type> <database_or_table_name> policy retention [softdelete = <timespan>] [recoverability = disabled|enabled]
```

* `entity_type`: Tabelle oder Datenbank
* `database_or_table`: `database_name` oder `database_name.table_name` oder `table_name` (im Daten Bank Kontext)
* `table_name`: Name einer Tabelle in einem Daten Bank Kontext.  Ein Platzhalter`*` (ist hier zulässig).
* `retention_policy` :

```kusto
    "{ 
        \"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Disabled\"
    }" 
```

**Beispiele**

Die Beibehaltungs Richtlinie für die Datenbank mit `MyDatabase`dem Namen anzeigen:

```kusto
.show database MyDatabase policy retention
```

Legt eine Beibehaltungs Richtlinie mit einem vorläufigen Lösch Zeitraum von 10 Tagen fest und deaktiviert die Wiederherstellbarkeit von Daten:

```kusto
.alter-merge table Table1 policy retention softdelete = 10d recoverability = disabled
```

Legt eine Beibehaltungs Richtlinie mit einem vorläufigen Lösch Zeitraum von 10 Tagen fest und ermöglicht die Wiederherstellbarkeit von Daten:

```kusto
.alter table Table1 policy retention "{\"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Enabled\"}"
```

Legt die gleiche Beibehaltungs Richtlinie wie oben fest, aber dieses Mal für mehrere Tabellen (Table1, Table2 und "table3"):

```kusto
.alter tables (Table1, Table2, Table3) policy retention "{\"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Enabled\"}"
```

Legt eine Beibehaltungs Richtlinie mit den Standardwerten fest: 100 Jahre als Zeitraum für vorläufiges löschen und wieder Herstellbarkeit aktiviert:

```kusto
.alter table Table1 policy retention "{}"
```