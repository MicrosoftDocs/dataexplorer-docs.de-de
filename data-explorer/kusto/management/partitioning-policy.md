---
title: 'Richtlinien Verwaltung für die Daten Partitionierung: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt die Richtlinien Verwaltung für die Daten Partitionierung in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/04/2020
ms.openlocfilehash: f34042af4578643f8b1b4120345cb37f66b71830
ms.sourcegitcommit: a36981785765b85a961f275be24d297d38e498fd
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96310016"
---
# <a name="partitioning-policy-command"></a>Partitionierungsrichtlinienbefehl

Die Richtlinie zur Daten Partitionierung wird [hier](../management/partitioningpolicy.md)ausführlich erläutert.

## <a name="show-policy"></a>Richtlinie anzeigen

```kusto
.show table [table_name] policy partitioning
```

Der `.show` Befehl zeigt die Partitionierungs Richtlinie an, die auf die Tabelle angewendet wird.

### <a name="output"></a>Output

|Richtlinienname | Name der Entität | Richtlinie | Untergeordnete Entitäten | Entitätstyp
|---|---|---|---|---
|Datapartitionierung | Tabellenname | JSON-Serialisierung des Policy-Objekts | NULL | Tabelle

## <a name="alter-and-alter-merge-policy"></a>Alter-und Alter-Merge-Richtlinie

```kusto
.alter table [table_name] policy partitioning @'policy object, serialized as JSON'

.alter-merge table [table_name] policy partitioning @'partial policy object, serialized as JSON'
```

Der- `.alter` Befehl ermöglicht das Ändern der Partitionierungs Richtlinie, die auf die Tabelle angewendet wird.

Für den Befehl sind [databaseadmin](access-control/role-based-authorization.md) -Berechtigungen erforderlich.

Es kann bis zu einer Stunde dauern, bis Änderungen an der Richtlinie wirksam werden.

### <a name="examples"></a>Beispiele

#### <a name="setting-a-policy-with-a-hash-partition-key"></a>Festlegen einer Richtlinie mit einem Hash Partitions Schlüssel

```kusto
.alter table [table_name] policy partitioning @'{'
  '"PartitionKeys": ['
    '{'
      '"ColumnName": "my_string_column",'
      '"Kind": "Hash",'
      '"Properties": {'
        '"Function": "XxHash64",'
        '"MaxPartitionCount": 256,'
      '}'
    '}'
  ']'
'}'
```

#### <a name="setting-a-policy-with-a-uniform-range-datetime-partition-key"></a>Festlegen einer Richtlinie mit einem Uniform Range-DateTime-Partitions Schlüssel

```kusto
.alter table [table_name] policy partitioning @'{'
  '"PartitionKeys": ['
    '{'
      '"ColumnName": "my_datetime_column",'
      '"Kind": "UniformRange",'
      '"Properties": {'
        '"Reference": "1970-01-01T00:00:00",'
        '"RangeSize": "1.00:00:00"'
        '"OverrideCreationTime": false'
      '}'
    '}'
  ']'
'}'
```

#### <a name="setting-a-policy-with-both-kinds-of-partition-keys"></a>Festlegen einer Richtlinie mit beiden Arten von Partitions Schlüsseln

```kusto
.alter table [table_name] policy partitioning @'{'
  '"PartitionKeys": ['
    '{'
      '"ColumnName": "my_string_column",'
      '"Kind": "Hash",'
      '"Properties": {'
        '"Function": "XxHash64",'
        '"MaxPartitionCount": 256,'
      '}'
    '},'
    '{'
      '"ColumnName": "my_datetime_column",'
      '"Kind": "UniformRange",'
      '"Properties": {'
        '"Reference": "1970-01-01T00:00:00",'
        '"RangeSize": "1.00:00:00"'
        '"OverrideCreationTime": false'
      '}'
    '}'
  ']'
'}'
```

#### <a name="setting-a-specific-property-of-the-policy-explicitly-at-table-level"></a>Explizites Festlegen einer bestimmten Eigenschaft der Richtlinie auf Tabellenebene

`EffectiveDateTime`Verwenden Sie den folgenden Befehl, um die der Richtlinie auf einen anderen Wert festzulegen:

```kusto
.alter-merge table [table_name] policy partitioning @'{"EffectiveDateTime":"2020-01-01"}'
```

## <a name="delete-policy"></a>Richtlinie löschen

```kusto
.delete table [table_name] policy partitioning
```

Der- `.delete` Befehl löscht die Partitionierungs Richtlinie der angegebenen Tabelle.
