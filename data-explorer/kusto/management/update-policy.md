---
title: 'Kusto-Update Richtlinien Verwaltung: Azure Daten-Explorer'
description: Dieser Artikel beschreibt die Aktualisierungs Richtlinie in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 320824b4f614ea809141167c1284282b1ef641cf
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616557"
---
# <a name="update-policy"></a>Aktualisieren von Richtlinien

Die [Aktualisierungs Richtlinie](updatepolicy.md) ist ein Richtlinien Objekt auf Tabellenebene, um automatisch eine Abfrage auszuführen und die Ergebnisse zu erfassen, wenn Daten in einer anderen Tabelle erfasst werden.

## <a name="show-update-policy"></a>Update Richtlinie anzeigen

Mit diesem Befehl wird die Aktualisierungs Richtlinie der angegebenen Tabelle oder alle Tabellen in der Standarddatenbank zurück `*` gegeben, wenn als Tabellenname verwendet wird.

**Syntax**

* `.show``table` *TableName* `policy``update`
* `.show``table` *DatabaseName*`.`(*Tabellenname* `policy` )`update`
* `.show` `table` `*` `policy` `update`

**Rückgabe**

Dieser Befehl gibt eine Tabelle mit einem Datensatz pro Tabelle mit den folgenden Spalten zurück:

|Column    |type    |BESCHREIBUNG                                                                                                                                                           |
|----------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|EntityName|`string`|Der Name der Entität, auf der die Update Richtlinie definiert ist.                                                                                                                |
|Richtlinien  |`string`|Ein JSON-Array, das alle für die Entität definierten Update Richtlinien angibt, formatiert als [Update-Richtlinien Objekt](updatepolicy.md#the-update-policy-object) .|

**Beispiel**

```kusto
.show table DerivedTableX policy update 
```

|EntityName        |Richtlinien                                                                                                                                    |
|------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
|[TestDB]. [Derivedtablex]|[{"Isaktivierte": true, "Source": "mytablex", "Query": "myupdatefunction ()", "IsTransactional": false, "propagatverstionproperties": false}]|

## <a name="alter-update-policy"></a>Update Richtlinie ändern

Mit diesem Befehl wird die Aktualisierungs Richtlinie der angegebenen Tabelle festgelegt.

**Syntax**

* `.alter``table` *TableName* `policy` `update` *arrayofupdatepolicyobjects*
* `.alter``table` *DatabaseName*`.`*TableName* `policy` `update` *arrayofupdatepolicyobjects*

*Arrayofupdatepolicyobjects* ist ein JSON-Array, für das NULL oder mehr Update-Richtlinien Objekte definiert sind.

**Hinweise**

1. Es wird empfohlen, eine gespeicherte Funktion für die- `Query` Eigenschaft des Update Policy-Objekts zu verwenden.
   Dadurch ist es einfach, nur die Funktionsdefinition anstelle des gesamten Richtlinien Objekts zu ändern.

2. Die folgenden Überprüfungen werden für die Update Richtlinie ausgeführt, wenn Sie festgelegt wird (falls `IsEnabled` auf `true`festgelegt ist):
    1. `Source`: Die Tabelle sollte in der Zieldatenbank vorhanden sein.
    2. `Query`: 
        * Das durch das Schema definierte Schema muss mit dem Schema der Ziel Tabelle identisch sein. 
        * Die Abfrage muss auf die `source` Tabelle der Update Richtlinie verweisen. Die Definition einer Update-Richtlinien Abfrage, die *nicht* auf die Quelle verweist, `AllowUnreferencedSourceTable=true` ist möglich, indem in den with-Eigenschaften festgelegt wird (siehe Beispiel unten). Dies wird jedoch aus Leistungsgründen nicht empfohlen (Dies impliziert, dass bei jeder Erfassung in der Quell Tabelle *alle* Datensätze in einer anderen Tabelle für die Ausführung der Update Richtlinie berücksichtigt werden).
    3. Die Richtlinie führt nicht dazu, dass ein Cycle in der Kette der Update Richtlinien in der Zieldatenbank erstellt wird.
    4. Falls `IsTransactional` auf festgelegt ist `true`, `TableAdmin` werden Berechtigungen auch mit `Source` (der Quell Tabelle) überprüft.
  
3. Stellen Sie sicher, dass Sie die Update Richtlinie/-Funktion für die Leistung testen, bevor Sie Sie für die Ausführung bei jeder Erfassung in der Quell Tabelle anwenden. Informationen hierzu finden Sie [hier](updatepolicy.md#testing-an-update-policys-performance-impact).

**Rückgabe**

Mit dem Befehl wird das Update der-Richtlinie der Tabelle festgelegt (sofern vorhanden), wobei alle aktuellen Richtlinien außer Kraft gesetzt werden. Anschließend wird die Ausgabe des entsprechenden Befehls der [Tabellen Tabelle-Aktualisierungs Richtlinie angezeigt](#show-update-policy) .

**Beispiel**

```kusto
// Creating function that will be used for update
.create function 
MyUpdateFunction()
{
    MyTableX
    | where ColumnA == 'some-string'
    | summarize MyCount=count() by ColumnB, Key=ColumnC
    | join (OtherTable | project OtherColumnZ, Key=OtherColumnC) on Key
    | project ColumnB, ColumnZ=OtherColumnZ, Key, MyCount
}

// Creating the target table (in case it doesn't already exist)
.set-or-append DerivedTableX <| MyUpdateFunction() | limit 0

// Use update policy on table DerivedTableX
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyUpdateFunction()", "IsTransactional": false, "PropagateIngestionProperties": false}]'
```

- Wenn eine Erfassung der Quell Tabelle (in diesem Fall `MyTableX`) erfolgt, werden mindestens 1 Blöcke (datenshards) in dieser Tabelle erstellt.
- Der `Query` , der im Update-Richtlinien Objekt definiert ist (in diesem `MyUpdateFunction()`Fall), wird nur in diesen Blöcken ausgeführt und kann nicht für die gesamte Tabelle ausgeführt werden.
  - Diese "Bereichs Definition" erfolgt intern und automatisch, sollte beim Definieren von nicht behandelt werden `Query`.
  - Nur neu erfasste Datensätze (in den einzelnen Erfassungs Vorgängen) werden beim erfassen in der abgeleiteten Tabelle berücksichtigt (in diesem Fall `DerivedTableX`).


```kusto
// The following example will throw an error for not referencing the source table in the update policy query
// The policy's source table is MyTableX, whereas the query only references MyOtherTable. 
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

// Adding the following properties will suppress the error (but is not recommended)
.alter table DerivedTableX policy update with (AllowUnreferencedSourceTable=true)
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

```

## <a name="alter-merge-table-table-policy-update"></a>. Alter-Merge table table Policy Update

Dieser Befehl ändert die Update Richtlinie der angegebenen Tabelle.

**Syntax**

* `.alter-merge``table` *TableName* `policy` `update` *arrayofupdatepolicyobjects*
* `.alter-merge``table` *DatabaseName*`.`*TableName* `policy` `update` *arrayofupdatepolicyobjects*

*Arrayofupdatepolicyobjects* ist ein JSON-Array, für das NULL oder mehr Update-Richtlinien Objekte definiert sind.

**Hinweise**

1. Es wird empfohlen, dass eine gespeicherte Funktion für die Massen Implementierung der Query-Eigenschaft des Update Policy-Objekts verwendet wird. Dadurch ist es einfach, nur die Funktionsdefinition anstelle des gesamten Richtlinien Objekts zu ändern.

2. Die gleichen Überprüfungen, die für die Update Richtlinie ausgeführt werden, `alter` bei einem Befehl werden für `alter-merge` einen Befehl ausgeführt.

**Rückgabe**

Der Befehl fügt an das Update-Richtlinien Objekt der Tabelle an (wobei ggf. alle definierten aktuellen Richtlinien überschrieben werden) und gibt dann die Ausgabe des entsprechenden Befehls der [Tabellen Tabelle Update-Richtlinie anzeigen](#show-update-policy) zurück.

**Beispiel**

```kusto
.alter-merge table DerivedTableX policy update 
@'[{"IsEnabled": true, "Source": "MyTableY", "Query": "MyUpdateFunction()", "IsTransactional": false}]'  
``` 

## <a name="delete-table-table-policy-update"></a>. Löschen der Richtlinien Aktualisierung für Tabellen Tabellen

Löscht die Update Richtlinie der angegebenen Tabelle.

**Syntax**

* `.delete``table` *TableName* `policy``update`
* `.delete``table` *DatabaseName*`.`(*Tabellenname* `policy` )`update`

**Rückgabe**

Mit dem Befehl wird das Update-Richtlinien Objekt der Tabelle gelöscht, und anschließend wird die Ausgabe des entsprechenden Befehls der [Tabelle "Update](#show-update-policy) -Tabelle anzeigen" zurückgegeben.

**Beispiel**

```kusto
.delete table DerivedTableX policy update 
```