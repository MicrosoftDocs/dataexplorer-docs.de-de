---
title: 'Kusto-Update Richtlinien Verwaltung: Azure Daten-Explorer'
description: Erfahren Sie mehr über Befehle zum Aktualisieren von Richtlinien in Azure Daten-Explorer. Weitere Informationen finden Sie unter Vorgehensweise beim Anzeigen, festlegen, ändern und Löschen von Tabellen Update Richtlinien.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/04/2020
ms.openlocfilehash: 5d346e5b7932437322cb8a41210a6f375cd6d6f0
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321113"
---
# <a name="update-policy-commands"></a>Aktualisieren von Richtlinienbefehlen

Die [Aktualisierungs Richtlinie](updatepolicy.md) ist ein Richtlinien Objekt auf Tabellenebene, das automatisch eine Abfrage ausführt und dann die Ergebnisse erfasst, wenn Daten in einer anderen Tabelle erfasst werden.

## <a name="show-update-policy"></a>Update Richtlinie anzeigen

Mit diesem Befehl wird die Aktualisierungs Richtlinie der angegebenen Tabelle oder alle Tabellen in der Standarddatenbank zurückgegeben, wenn `*` als Tabellenname verwendet wird.

### <a name="syntax"></a>Syntax

* `.show` `table` *TableName* `policy` `update`
* `.show``table` *DatabaseName*( `.` *Tabellenname* `policy` )`update`
* `.show` `table` `*` `policy` `update`

### <a name="returns"></a>Rückgabe

Dieser Befehl gibt eine Tabelle zurück, die über einen Datensatz pro Tabelle verfügt.

|Spalte    |Typ    |BESCHREIBUNG                                                                                                                                                           |
|----------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|EntityName|`string`|Der Name der Entität, auf der die Update Richtlinie definiert ist.                                                                                                                |
|Richtlinien  |`string`|Ein JSON-Array, das alle für die Entität definierten Update Richtlinien angibt, formatiert als [Update-Richtlinien Objekt](updatepolicy.md#the-update-policy-object) .|

### <a name="example"></a>Beispiel

```kusto
.show table DerivedTableX policy update 
```

|EntityName        |Richtlinien                                                                                                                                    |
|------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
|[TestDB]. [Derivedtablex]|[{"Isaktivierte": true, "Source": "mytablex", "Query": "myupdatefunction ()", "IsTransactional": false, "propagatverstionproperties": false}]|

## <a name="alter-update-policy"></a>Update Richtlinie ändern

Mit diesem Befehl wird die Aktualisierungs Richtlinie der angegebenen Tabelle festgelegt.

### <a name="syntax"></a>Syntax

* `.alter``table` *TableName* `policy` `update` *Arrayofupdatepolicyobjects*
* `.alter``table` *DatabaseName* `.` *TableName* `policy` `update` *arrayofupdatepolicyobjects*

*Arrayofupdatepolicyobjects* ist ein JSON-Array, für das NULL oder mehr Update-Richtlinien Objekte definiert sind.

> [!NOTE]
> * Verwenden Sie eine gespeicherte Funktion für die- `Query` Eigenschaft des Update Policy-Objekts.
   Anstelle des gesamten Richtlinien Objekts müssen Sie nur die Funktionsdefinition ändern.
> * Wenn `IsEnabled` auf festgelegt ist `true` , werden die folgenden Überprüfungen für die Update Richtlinie ausgeführt, während Sie festgelegt wird:
>    * `Source` -Überprüft, ob die Tabelle in der Zieldatenbank vorhanden ist.
>    * `Query` 
>        * Überprüft, ob das durch das Schema definierte Schema mit der einer Ziel Tabelle übereinstimmt.
>        * Überprüft, ob die Abfrage `source` auf die Tabelle der Update Richtlinie verweist. 
        Die Definition einer Update-Richtlinien Abfrage, die nicht auf die Quelle verweist, ist möglich `AllowUnreferencedSourceTable=true` , indem in den *with* -Eigenschaften festgelegt wird (siehe Beispiel unten), aber aufgrund von Leistungsproblemen nicht empfohlen wird. Bei jeder Erfassung in der Quell Tabelle werden alle Datensätze in einer anderen Tabelle für die Ausführung der Update Richtlinie berücksichtigt.
 >       * Hier wird überprüft, ob die Richtlinie dazu führt, dass in der Kette der Update Richtlinien in der Zieldatenbank keine Zyklen erstellt werden.
 > * Wenn `IsTransactional` auf festgelegt ist `true` , prüft, ob die `TableAdmin` Berechtigungen auch anhand von `Source` (der Quell Tabelle) überprüft werden.
 > * Testen Sie die Aktualisierungs Richtlinie oder-Funktion, bevor Sie Sie für die Ausführung bei jeder Erfassung in der Quell Tabelle anwenden. Weitere Informationen finden Sie unter [Testen der Auswirkungen der Update Richtlinie auf die Leistung](updatepolicy.md#performance-impact).

### <a name="returns"></a>Rückgabe

Der Befehl legt das Aktualisierungs Richtlinien Objekt der Tabelle fest, wobei jede aktuelle Richtlinie außer Kraft gesetzt wird, und gibt dann die Ausgabe des entsprechenden [`.show table update policy`](#show-update-policy) Befehls zurück.

### <a name="example"></a>Beispiel

```kusto
// Create a function that will be used for update
.create function 
MyUpdateFunction()
{
    MyTableX
    | where ColumnA == 'some-string'
    | summarize MyCount=count() by ColumnB, Key=ColumnC
    | join (OtherTable | project OtherColumnZ, Key=OtherColumnC) on Key
    | project ColumnB, ColumnZ=OtherColumnZ, Key, MyCount
}

// Create the target table (if it doesn't already exist)
.set-or-append DerivedTableX <| MyUpdateFunction() | limit 0

// Use update policy on table DerivedTableX
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyUpdateFunction()", "IsTransactional": false, "PropagateIngestionProperties": false}]'
```

* Wenn eine Erfassung der Quell Tabelle stattfindet, in diesem Fall, `MyTableX` werden ein oder mehrere Blöcke (datenshards) in dieser Tabelle erstellt.
* Der, der `Query` im Update Policy-Objekt definiert ist, wird in diesem Fall `MyUpdateFunction()` nur in diesen Blöcken ausgeführt und nicht für die gesamte Tabelle ausgeführt.
  * "Bereichs Definition" erfolgt intern und automatisch und sollte beim Definieren von nicht behandelt werden `Query` .
  * Nur neu erfasste Datensätze, die bei jedem Erfassungs Vorgang unterschiedlich sind, werden beim erfassen in der `DerivedTableX` abgeleiteten Tabelle berücksichtigt.

```kusto
// The following example will throw an error for not referencing the source table in the update policy query
// The policy's source table is MyTableX, whereas the query only references MyOtherTable. 
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

// Adding the following properties will suppress the error (but is not recommended)
.alter table DerivedTableX policy update with (AllowUnreferencedSourceTable=true)
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

```

## <a name="alter-merge-table-tablename-policy-update"></a>. Alter-Merge Table *TableName* Policy Update

Dieser Befehl ändert die Update Richtlinie der angegebenen Tabelle.

**Syntax**

* `.alter-merge``table` *TableName* `policy` `update` *Arrayofupdatepolicyobjects*
* `.alter-merge``table` *DatabaseName* `.` *TableName* `policy` `update` *arrayofupdatepolicyobjects*

*Arrayofupdatepolicyobjects* ist ein JSON-Array, für das NULL oder mehr Update-Richtlinien Objekte definiert sind.

> [!NOTE]
> * Verwenden Sie gespeicherte Funktionen für die Massen Implementierung der Query-Eigenschaft des Update Policy-Objekts. 
     Sie müssen nur die Funktionsdefinition anstelle des gesamten Richtlinien Objekts ändern.
> * Die Überprüfungen sind identisch mit denen, die für einen Befehl durchgeführt wurden `alter` .

**Rückgabe**

Der Befehl fügt dem Update-Richtlinien Objekt der Tabelle an, wobei jede aktuelle Richtlinie überschrieben wird, und gibt dann die Ausgabe des entsprechenden [`.show table *TableName* update policy`](#show-update-policy) Befehls zurück.

**Beispiel**

```kusto
.alter-merge table DerivedTableX policy update 
@'[{"IsEnabled": true, "Source": "MyTableY", "Query": "MyUpdateFunction()", "IsTransactional": false}]'  
``` 

## <a name="delete-table-tablename-policy-update"></a>. Löschen der *TableName* -Richtlinien Aktualisierung für Tabellen

Löscht die Update Richtlinie der angegebenen Tabelle.

**Syntax**

* `.delete` `table` *TableName* `policy` `update`
* `.delete``table` *DatabaseName*( `.` *Tabellenname* `policy` )`update`

**Rückgabe**

Mit dem Befehl wird das Update-Richtlinien Objekt der Tabelle gelöscht, und anschließend wird die Ausgabe des entsprechenden Befehls zurückgegeben [`.show table *TableName* update policy`](#show-update-policy) .

**Beispiel**

```kusto
.delete table DerivedTableX policy update 
```
