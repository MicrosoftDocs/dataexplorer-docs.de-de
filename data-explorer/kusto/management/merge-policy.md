---
title: Richtlinienverwaltung zusammenführen - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die Richtlinienverwaltung in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4093c9c09e4e268bc38cabdc6da27f0ac2ee17ab
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81664023"
---
# <a name="merge-policy-management"></a>Mergerichtlinienverwaltung

## <a name="show-policy"></a>Show-Politik

```kusto
.show table [table_name] policy merge

.show table * policy merge

.show database [database_name] policy merge

.show database * policy merge
```

Zeigt die aktuelle Mergerichtlinie für die Datenbank oder Tabelle an.
Zeigt alle Richtlinien des angegebenen Entitätstyps (Datenbank oder Tabelle) an, wenn der angegebene Name '*' lautet.

### <a name="output"></a>Output

|Richtlinienname | Entitätsname | Richtlinie | Untergeordnete Entitäten | Entitätstyp
|---|---|---|---|---
|ExtentsMergePolicy | Datenbank - Tabellenname | eine JSON-formatierte Zeichenfolge, die die Richtlinie darstellt | Eine Liste von Tabellen (für eine Datenbank)|Datenbank&#124; Tabelle

## <a name="alter-policy"></a>Änderungspolitik

### <a name="examples"></a>Beispiele

#### <a name="1-setting-all-properties-of-the-policy-explicitly-at-table-level"></a>1. Festlegen aller Eigenschaften der Richtlinie explizit auf Tabellenebene:

```kusto
.alter table [table_name] policy merge 
@'{'
    '"ExtentSizeTargetInMb": 1024,'
    '"OriginalSizeInMbUpperBoundForRebuild": 2048,'
    '"RowCountUpperBoundForRebuild": 750000, '
    '"RowCountUpperBoundForMerge": 0, '
    '"MaxExtentsToMerge": 100, '
    '"LoopPeriod": "01:00:00", '
    '"MaxRangeInHours": 8, '
    '"AllowRebuild": true,'
    '"AllowMerge": true '
'}'
```

#### <a name="2-setting-all-properties-of-the-policy-explicitly-at-database-level"></a>2. Festlegen aller Eigenschaften der Richtlinie explizit auf Datenbankebene:

```kusto
.alter database [database_name] policy merge 
@'{'
    '"ExtentSizeTargetInMb": 1024,'
    '"OriginalSizeInMbUpperBoundForRebuild": 2048,'
    '"RowCountUpperBoundForRebuild": 750000,'
    '"RowCountUpperBoundForMerge": 0,'
    '"MaxExtentsToMerge": 100,'
    '"LoopPeriod": "01:00:00",'
    '"MaxRangeInHours": 8,'
    '"AllowRebuild": true,'
    '"AllowMerge": true'
'}'
```

#### <a name="3-setting-the-default-merge-policy-at-database-level"></a>3. Festlegen *default* der Standard-Mergerichtlinie auf Datenbankebene:

```kusto
.alter database [database_name] policy merge '{}'
```

#### <a name="4-altering-a-single-property-of-the-policy-at-database-level-keeping-all-other-properties-as-is"></a>4. Ändern einer einzelnen Eigenschaft der Richtlinie auf Datenbankebene, wobei alle anderen Eigenschaften beibehalten werden:

```kusto
.alter-merge database [database_name] policy merge
@'{'
    '"ExtentSizeTargetInMb": 1024'
'}'
```

#### <a name="5-altering-a-single-property-of-the-policy-at-table-level-keeping-all-other-properties-as-is"></a>5. Ändern einer einzelnen Eigenschaft der Richtlinie auf Tabellenebene, wobei alle anderen Eigenschaften beibehalten werden:

```kusto
.alter-merge table [table_name] policy merge
@'{'
    '"RowCountUpperBoundForRebuild": 750000'
'}'
```

Alle oben genannten Zurückgaben der aktualisierten Ausdehnungszusammenführungsrichtlinie für die Entität (Datenbank oder Tabelle, die als qualifizierter Name angegeben ist) als Ausgabe zurück.

Änderungen an der Richtlinie können bis zu 1 Stunde in Anspruch nehmen.

## <a name="delete-policy-of-merge"></a>Richtlinien für das Löschen der Zusammenführung

```kusto
.delete table [table_name] policy merge

.delete database [database_name] policy merge

```

Der Befehl löscht die aktuelle Mergerichtlinie für die angegebene Entität.
