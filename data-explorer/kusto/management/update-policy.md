---
title: Aktualisierungsrichtlinie - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt Update-Richtlinien in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 95952a6f4e7a8c0d1a5b4207742e15873eb44c91
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519532"
---
# <a name="update-policy"></a>Aktualisieren von Richtlinien

Die [Aktualisierungsrichtlinie](updatepolicy.md) ist ein Richtlinienobjekt auf Tabellenebene, das eine Abfrage automatisch ausführt und ihre Ergebnisse aufnimmt, wenn Daten in eine andere Tabelle aufgenommen werden.

## <a name="show-update-policy"></a>Aktualisierungsrichtlinie anzeigen

Dieser Befehl gibt die Aktualisierungsrichtlinie der angegebenen Tabelle oder `*` aller Tabellen in der Standarddatenbank zurück, wenn sie als Tabellenname verwendet wird.

**Syntax**

* `.show``table` *TableName* `policy``update`
* `.show``table` *DatabaseName-Tabellenname*`.`*TableName* `policy``update`
* `.show` `table` `*` `policy` `update`

**Rückgabe**

Dieser Befehl gibt eine Tabelle mit einem Datensatz pro Tabelle mit den folgenden Spalten zurück:

|Column    |type    |BESCHREIBUNG                                                                                                                                                           |
|----------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|EntityName|`string`|Der Name der Entität, auf der die Aktualisierungsrichtlinie definiert ist,                                                                                                                |
|Richtlinien  |`string`|Ein JSON-Array, das alle für die Entität definierten Aktualisierungsrichtlinien angibt, formatiert als [Aktualisierungsrichtlinienobjekt](updatepolicy.md#the-update-policy-object)|

**Beispiel**

```kusto
.show table DerivedTableX policy update 
```

|EntityName        |Richtlinien                                                                                                                                    |
|------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
|[TestDB]. [DerivedTableX]|"IsEnabled": true, "Source": "MyTableX","Query": "MyUpdateFunction()","IsTransactional": false,"PropagateIngestionProperties": false']|

## <a name="alter-update-policy"></a>Änderung der Aktualisierungsrichtlinie

Dieser Befehl legt die Aktualisierungsrichtlinie der angegebenen Tabelle fest.

**Syntax**

* `.alter``table` *TableName* `policy` TableName `update` *ArrayOfUpdatePolicyObjects*
* `.alter``table` *DatabaseName*`.`*TableName* `policy` `update` *ArrayOfUpdatePolicyObjects*

*ArrayOfUpdatePolicyObjects* ist ein JSON-Array, für das null oder mehr Aktualisierungsrichtlinienobjekte definiert sind.

**Hinweise**

1. Es wird empfohlen, eine gespeicherte `Query` Funktion für die Eigenschaft des Aktualisierungsrichtlinienobjekts zu verwenden.
   Dadurch ist es einfach, nur die Funktionsdefinition anstelle des gesamten Richtlinienobjekts zu ändern.

2. Die folgenden Validierungen werden für die Aktualisierungsrichtlinie durchgeführt, wenn sie festgelegt wird (falls `IsEnabled` auf `true`gesetzt):
    1. `Source`: Tabelle sollte in der Zieldatenbank vorhanden sein.
    2. `Query`: 
        * Das vom Schema definierte Schema sollte mit dem schemas der Zieltabelle übereinstimmen. 
        * Die Abfrage muss `source` auf die Tabelle der Aktualisierungsrichtlinie verweisen. Das Definieren einer Aktualisierungsrichtlinienabfrage, die *nicht* auf die Quelle verweist, ist möglich, indem sie in der with-Eigenschaften (siehe Beispiel unten) festgelegt `AllowUnreferencedSourceTable=true` wird, wird jedoch aus Leistungsgründen im Allgemeinen nicht empfohlen (es bedeutet, dass für jede Aufnahme in die Quelltabelle *alle* Datensätze in einer anderen Tabelle für die Ausführung der Aktualisierungsrichtlinie berücksichtigt werden).
    3. Die Richtlinie führt nicht dazu, dass ein Zyklus in der Kette der Aktualisierungsrichtlinien in der Zieldatenbank erstellt wird.
    4. Falls `IsTransactional` auf `true`festgelegt, `TableAdmin` werden Berechtigungen `Source` auch anhand (der Quelltabelle) überprüft.
  
3. Stellen Sie sicher, dass Sie Ihre Updaterichtlinie / -funktion auf Leistung testen, bevor Sie sie anwenden, um sie bei jeder Aufnahme auf die Quelltabelle auszuführen - siehe [hier](updatepolicy.md#testing-an-update-policys-performance-impact).

**Rückgabe**

Der Befehl legt das Aktualisierungsrichtlinienobjekt der Tabelle fest (überschreibt ggf. alle definierten aktuellen Richtlinien) und gibt dann die Ausgabe des entsprechenden [.show-Tabellen-TABELLENaktualisierungsrichtlinienbefehls](#show-update-policy) zurück.

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

- Wenn eine Aufnahme in die Quelltabelle `MyTableX`(in diesem Fall ) auftritt, werden in dieser Tabelle 1 oder mehr Ausdehnungen (Datenshards) erstellt.
- Das, `Query` was im Aktualisierungsrichtlinienobjekt (in diesem Fall `MyUpdateFunction()`) definiert ist, wird nur für diese Ausdehnungen und nicht für die gesamte Tabelle ausgeführt.
  - Dieses "Scoping" erfolgt intern und automatisch, es sollte `Query`nicht beim Definieren der behandelt werden.
  - Bei der Aufnahme in die abgeleitete Tabelle (in diesem Fall) werden nur neu aufgenommene `DerivedTableX`Datensätze berücksichtigt (in diesem Fall unterschiedlich bei jedem Aufnahmevorgang).


```kusto
// The following example will throw an error for not referencing the source table in the update policy query
// The policy's source table is MyTableX, whereas the query only references MyOtherTable. 
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

// Adding the following properties will suppress the error (but is not recommended)
.alter table DerivedTableX policy update with (AllowUnreferencedSourceTable=true)
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

```

## <a name="alter-merge-table-table-policy-update"></a>.alter-merge Tabelle Richtlinienaktualisierung

Dieser Befehl ändert die Aktualisierungsrichtlinie der angegebenen Tabelle.

**Syntax**

* `.alter-merge``table` *TableName* `policy` TableName `update` *ArrayOfUpdatePolicyObjects*
* `.alter-merge``table` *DatabaseName*`.`*TableName* `policy` `update` *ArrayOfUpdatePolicyObjects*

*ArrayOfUpdatePolicyObjects* ist ein JSON-Array, für das null oder mehr Aktualisierungsrichtlinienobjekte definiert sind.

**Hinweise**

1. Es wird empfohlen, gespeicherte Funktionen für die Massenimplementierung der Abfrageeigenschaft des Aktualisierungsrichtlinienobjekts zu verwenden. Dadurch ist es einfach, nur die Funktionsdefinition anstelle des gesamten Richtlinienobjekts zu ändern.

2. Die gleichen Validierungen, die für die `alter` Aktualisierungsrichtlinie `alter-merge` im Falle eines Befehls durchgeführt werden, werden für einen Befehl ausgeführt.

**Rückgabe**

Der Befehl wird an das Aktualisierungsrichtlinienobjekt der Tabelle angehängt (ggf. überschreibt alle aktuellen Richtlinien) und gibt dann die Ausgabe des entsprechenden [.show-Tabellen-TABELLENaktualisierungsrichtlinienbefehls](#show-update-policy) zurück.

**Beispiel**

```kusto
.alter-merge table DerivedTableX policy update 
@'[{"IsEnabled": true, "Source": "MyTableY", "Query": "MyUpdateFunction()", "IsTransactional": false}]'  
``` 

## <a name="delete-table-table-policy-update"></a>.delete Tabelle TABLE-Richtlinienaktualisierung

Löscht die Aktualisierungsrichtlinie der angegebenen Tabelle.

**Syntax**

* `.delete``table` *TableName* `policy``update`
* `.delete``table` *DatabaseName-Tabellenname*`.`*TableName* `policy``update`

**Rückgabe**

Der Befehl löscht das Aktualisierungsrichtlinienobjekt der Tabelle und gibt dann die Ausgabe des entsprechenden [.show-Tabellen-TABELLENaktualisierungsrichtlinienbefehls](#show-update-policy) zurück.

**Beispiel**

```kusto
.delete table DerivedTableX policy update 
```