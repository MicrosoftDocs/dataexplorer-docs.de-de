---
title: Verwaltung von Datenpartitionierungsrichtlinien - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die Verwaltung von Datenpartitionierungsrichtlinien in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/04/2020
ms.openlocfilehash: 0e1ff783195f26adf7f98e511ca155f43609098c
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663967"
---
# <a name="data-partitioning-policy-management"></a>Datenpartitionierungsrichtlinienverwaltung

Die Datenpartitionierungsrichtlinie wird [hier](../management/partitioningpolicy.md)detailliert beschrieben.

## <a name="show-policy"></a>Show-Politik

```kusto
.show table [table_name] policy partitioning
```

Der `.show` Befehl zeigt die Partitionierungsrichtlinie an, die auf die Tabelle angewendet wird.

### <a name="output"></a>Output

|Richtlinienname | Name der Entität | Richtlinie | Untergeordnete Entitäten | Entitätstyp
|---|---|---|---|---
|DataPartitioning | Tabellenname | JSON-Serialisierung des Richtlinienobjekts | NULL | Tabelle

## <a name="alter-and-alter-merge-policy"></a>Änderungs- und Änderungsfusionsrichtlinie

```kusto
.alter table [table_name] policy partitioning @'policy object, serialized as JSON'

.alter-merge table [table_name] policy partitioning @'partial policy object, serialized as JSON'
```

Der `.alter` Befehl ermöglicht das Ändern der Partitionierungsrichtlinie, die auf die Tabelle angewendet wird.

Der Befehl erfordert [DatabaseAdmin-Berechtigungen.](access-control/role-based-authorization.md)

Änderungen an der Richtlinie können bis zu 1 Stunde in Anspruch nehmen.

### <a name="examples"></a>Beispiele

#### <a name="setting-all-properties-of-the-policy-explicitly-at-table-level"></a>Festlegen aller Eigenschaften der Richtlinie explizit auf Tabellenebene

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
      '}'
    '}'
  ']'
'}'
```

#### <a name="setting-a-specific-property-of-the-policy-explicitly-at-table-level"></a>Festlegen einer bestimmten Eigenschaft der Richtlinie explizit auf Tabellenebene

Um die `EffectiveDateTime` Richtlinie auf einen anderen Wert festzulegen, verwenden Sie den folgenden Befehl:

```kusto
.alter table [table_name] policy partitioning @'{"EffectiveDateTime":"2020-01-01"}'
```

## <a name="delete-policy"></a>Richtlinie löschen

```kusto
.delete table [table_name] policy partitioning
```

Der `.delete` Befehl löscht die Partitionierungsrichtlinie der angegebenen Tabelle.
