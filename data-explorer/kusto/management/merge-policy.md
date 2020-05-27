---
title: 'Verwaltung von Merge-Richtlinien: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt die mergerichtlinienverwaltung in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9ef6f2cd2359e35e90c3903738adf82728e9da40
ms.sourcegitcommit: a562ce255ac706ca1ca77d272a97b5975235729d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/26/2020
ms.locfileid: "83867068"
---
# <a name="merge-policy-management"></a>Merge-Richtlinien Verwaltung

## <a name="show-policy"></a>Richtlinie anzeigen

```kusto
.show table [table_name] policy merge

.show table * policy merge

.show database [database_name] policy merge

.show database * policy merge
```

Zeigt die aktuelle Merge-Richtlinie für die Datenbank oder Tabelle an.
Zeigt alle Richtlinien des angegebenen Entitäts Typs (Datenbank oder Tabelle) an, wenn der angegebene Name "*" lautet.

### <a name="output"></a>Output

|Richtlinienname | Entitätsname | Policy | Untergeordnete Entitäten | Entitätstyp
|---|---|---|---|---
|Extentsmergepolicy | Datenbank/Tabellenname | eine JSON-formatierte Zeichenfolge, die die Richtlinie darstellt. | Eine Liste von Tabellen (für eine Datenbank)|Daten Bank &#124; Tabelle

## <a name="alter-policy"></a>Richtlinie ändern

### <a name="examples"></a>Beispiele

#### <a name="1-setting-all-properties-of-the-policy-explicitly-at-table-level"></a>1. explizites Festlegen aller Eigenschaften der Richtlinie auf Tabellenebene:

```kusto
.alter table [table_name] policy merge 
@'{'
    '"ExtentSizeTargetInMb": 1024,'
    '"OriginalSizeInMbUpperBoundForRebuild": 2048,'
    '"OriginalSizeInMbUpperBoundForMerge": 4096,'
    '"RowCountUpperBoundForRebuild": 750000,'
    '"RowCountUpperBoundForMerge": 0,'
    '"MaxExtentsToMerge": 100,'
    '"LoopPeriod": "01:00:00",'
    '"MaxRangeInHours": 8,'
    '"AllowRebuild": true,'
    '"AllowMerge": true '
'}'
```

#### <a name="2-setting-all-properties-of-the-policy-explicitly-at-database-level"></a>2. explizites Festlegen aller Eigenschaften der Richtlinie auf Datenbankebene:

```kusto
.alter database [database_name] policy merge 
@'{'
    '"ExtentSizeTargetInMb": 1024,'
    '"OriginalSizeInMbUpperBoundForRebuild": 2048,'
    '"OriginalSizeInMbUpperBoundForMerge": 4096,'
    '"RowCountUpperBoundForRebuild": 750000,'
    '"RowCountUpperBoundForMerge": 0,'
    '"MaxExtentsToMerge": 100,'
    '"LoopPeriod": "01:00:00",'
    '"MaxRangeInHours": 8,'
    '"AllowRebuild": true,'
    '"AllowMerge": true'
'}'
```

#### <a name="3-setting-the-default-merge-policy-at-database-level"></a>3. Festlegen der *Standard* -Merge-Richtlinie auf Datenbankebene:

```kusto
.alter database [database_name] policy merge '{}'
```

#### <a name="4-altering-a-single-property-of-the-policy-at-database-level-keeping-all-other-properties-as-is"></a>4. Ändern einer einzelnen Eigenschaft der Richtlinie auf Datenbankebene, wobei alle anderen Eigenschaften unverändert bleiben:

```kusto
.alter-merge database [database_name] policy merge
@'{'
    '"ExtentSizeTargetInMb": 1024'
'}'
```

#### <a name="5-altering-a-single-property-of-the-policy-at-table-level-keeping-all-other-properties-as-is"></a>5. Ändern einer einzelnen Eigenschaft der Richtlinie auf Tabellenebene, wobei alle anderen Eigenschaften unverändert bleiben:

```kusto
.alter-merge table [table_name] policy merge
@'{'
    '"RowCountUpperBoundForRebuild": 750000'
'}'
```

Alle oben genannten Werte geben die aktualisierte Zusammenschluss Richtlinie für Blöcke für die Entität (Datenbank oder Tabelle, die als qualifizierter Name angegeben ist) als Ausgabe zurück.

Es kann bis zu einer Stunde dauern, bis Änderungen an der Richtlinie wirksam werden.

## <a name="delete-policy-of-merge"></a>Merge-Richtlinie löschen

```kusto
.delete table [table_name] policy merge

.delete database [database_name] policy merge

```

Der-Befehl löscht die aktuelle Merge-Richtlinie für die angegebene Entität.
