---
title: Kusto-Abfrage Erfassung (Set, Append, Replace)-Azure Daten-Explorer
description: Dieser Artikel beschreibt die Erfassung von Abfragen (. Set,. Append,. Set-oder-Append,. Set-or-Replace) in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: bfa44859987d8f3c4f11221fd8370290f08f9a67
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382045"
---
# <a name="ingest-from-query-set-append-set-or-append-set-or-replace"></a>Erfassung von Abfragen (. Set,. Append,. Set-oder-Append,. Set-or-Replace)

Diese Befehle führen eine Abfrage oder einen Steuerungs Befehl aus und erfassen die Ergebnisse der Abfrage in einer Tabelle. Der Unterschied zwischen diesen Befehlen besteht darin, wie vorhandene oder nicht vorhandene Tabellen und Daten behandelt werden:

|Get-Help          |Wenn die Tabelle vorhanden ist                     |Wenn die Tabelle nicht vorhanden ist                    |
|-----------------|------------------------------------|------------------------------------------|
|`.set`           |Der Befehl schlägt fehl.                  |Die Tabelle wird erstellt, und die Daten werden erfasst.|
|`.append`        |Daten werden an die Tabelle angehängt.      |Der Befehl schlägt fehl.                        |
|`.set-or-append` |Daten werden an die Tabelle angehängt.      |Die Tabelle wird erstellt, und die Daten werden erfasst.|
|`.set-or-replace`|Daten ersetzen die Daten in der Tabelle.|Die Tabelle wird erstellt, und die Daten werden erfasst.|

**Syntax**

`.set`[ `async` ] *TableName* [ `with` `(` *propertyName* `=` *PropertyValue* [ `,` ...] `)` ] `<|` *Queryorcommand*

`.append`[ `async` ] *TableName* [ `with` `(` *propertyName* `=` *PropertyValue* [ `,` ... `])` ] `<|` *Queryorcommand*

`.set-or-append`[ `async` ] *TableName* [ `with` `(` *propertyName* `=` *PropertyValue* [ `,` ...] `)` ] `<|` *Queryorcommand*

`.set-or-replace`[ `async` ] *TableName* [ `with` `(` *propertyName* `=` *PropertyValue* [ `,` ...] `)` ] `<|` *Queryorcommand*

**Argumente**

* `async`: Wenn dieser Wert angegeben wird, wird der Befehl sofort zurückgegeben, und die Erfassung im Hintergrund wird fortgesetzt. Die Ergebnisse des Befehls enthalten einen `OperationId` Wert, der dann mit dem Befehl verwendet werden kann `.show operation` , um den Erfassungs Status und die Ergebnisse abzurufen.
* *TableName*: der Name der Tabelle, in der Daten erfasst werden sollen.
  Der Tabellenname ist immer relativ zur Datenbank im Kontext.
* *PropertyName*, *PropertyValue*: eine beliebige Anzahl von Erfassungs Eigenschaften, die sich auf den Erfassungsprozess auswirken.

 Unterstützte Erfassungs Eigenschaften:

|Eigenschaft        |BESCHREIBUNG|
|----------------|-----------------------------------------------------------------------------------------------------------------------------|
|`creationTime`   | Der DateTime-Wert (formatiert als ISO8601-Zeichenfolge), der zum Zeitpunkt der Erstellung der erfassten Datenblöcke verwendet werden soll. Wenn kein Wert angegeben wird, wird der aktuelle Wert (Now ()) verwendet.|
|`extend_schema`  | Ein boolescher Wert, der den Befehl anweist, das Schema der Tabelle zu erweitern (Standardwert: false), wenn angegeben. Diese Option gilt nur für die Befehle. Append. Set-oder-Append und Set-or-Replace. Es sind nur Schemaerweiterungen zulässig, bei denen zusätzliche Spalten am Ende der Tabelle hinzugefügt werden.|
|`recreate_schema`  | Ein boolescher Wert, der, falls angegeben, beschreibt, ob der Befehl das Schema der Tabelle neu erstellen kann (Standardwert: false). Diese Option gilt nur für den Befehl Set-or-Replace. Diese Option hat Vorrang vor der extend_schema-Eigenschaft, wenn beide festgelegt sind.|
|`folder`         | Der Ordner, der der Tabelle zugewiesen werden soll. Wenn die Tabelle bereits vorhanden ist, überschreibt diese Eigenschaft den Ordner der Tabelle.|
|`ingestIfNotExists`   | Ein Zeichen folgen Wert, der, sofern angegeben, verhindert, dass die Erfassung erfolgreich ist, wenn die Tabelle bereits Daten aufweist, die mit einem Erfassungs-by:-Tag mit dem gleichen Wert markiert sind.|
|`policy_ingestiontime`   | Ein boolescher Wert, der angibt, ob die [IngestionTime-Richtlinie](../../management/ingestiontime-policy.md) für eine Tabelle aktiviert werden soll, die durch diesen Befehl erstellt wird. Der Standardwert ist "True".|
|`tags`   | Eine JSON-Zeichenfolge, die angibt, welche Überprüfungen im Rahmen der Erfassung ausgeführt werden sollen.|
|`docstring`   | Eine Zeichenfolge, die die Tabelle dokumentiert.|

  Außerdem gibt es eine Eigenschaft, die das Verhalten des Befehls selbst steuert:

|Eigenschaft        |Typ    |Beschreibung|
|----------------|--------|-----------------------------------------------------------------------------------------------------------------------------|
|`distributed`   |`bool`  |Gibt an, dass der Befehl von allen Knoten erfasst wird, die die Abfrage parallel ausführen. (Der Standardwert ist `false` .)  Siehe folgende Hinweise.|

* *Queryorcommand*: der Text einer Abfrage oder ein Steuerelement Befehl, dessen Ergebnisse als Daten für die Erfassung verwendet werden.

> [!NOTE]
> Nur `.show` Steuerungsbefehle werden unterstützt.

**Anmerkungen**

* `.set-or-replace`ersetzt die Daten der Tabelle, wenn Sie vorhanden ist (löscht die vorhandenen datenshards), oder erstellt die Ziel Tabelle, wenn Sie nicht bereits vorhanden ist.
  Das Tabellen Schema wird beibehalten, es sei denn, eine der-oder-Erfassungs `extend_schema` `recreate_schema` Eigenschaften ist auf festgelegt `true` . Wenn das Schema geändert wird, geschieht dies vor der tatsächlichen Datenerfassung in der eigenen Transaktion, sodass ein Fehler beim Erfassen der Daten nicht bedeutet, dass das Schema nicht geändert wurde.
* `.set-or-append`-und- `.append` Befehle behalten das Schema bei, sofern die Erfassungs `extend_schema` Eigenschaft nicht auf festgelegt ist `true` . Wenn das Schema geändert wird, geschieht dies vor der tatsächlichen Datenerfassung in der eigenen Transaktion, sodass ein Fehler beim Erfassen der Daten nicht bedeutet, dass das Schema nicht geändert wurde.
* Es wird **dringend empfohlen** , die Daten für die Erfassung auf weniger als 1 GB pro Erfassungs Vorgang zu beschränken. Ggf. können mehrere Erfassungs Befehle verwendet werden.
* Die Datenerfassung ist ein ressourcenintensiver Vorgang, der sich auf gleichzeitige Aktivitäten im Cluster auswirken kann, einschließlich der Ausführung von Abfragen. Vermeiden Sie die Ausführung von "zu viele"-Befehle gleichzeitig.
* Wenn das ResultSetSchema mit dem der Ziel Tabelle übereinstimmt, basiert der Vergleich auf den Spaltentypen. Es sind keine übereinstimmenden Spaltennamen vorhanden. Stellen Sie daher sicher, dass sich die Schema Spalten der Abfrageergebnisse in derselben Reihenfolge wie die Tabelle befinden. andernfalls werden die Daten in die falsche Spalte aufgenommen.
* Wenn das-Flag auf festgelegt wird `distributed` , `true` ist die Menge der Daten, die von der Abfrage erzeugt werden, sehr groß (überschreitet 1 GB an Daten), **und** für die Abfrage ist keine Serialisierung erforderlich (sodass mehrere Knoten eine parallele Ausgabe erzeugen können).
  Wenn die Abfrageergebnisse klein sind, empfiehlt es sich nicht, dieses Flag zu verwenden, da es möglicherweise unnötig viele kleine datenshards generiert.

**Beispiele** 

Erstellen Sie eine neue Tabelle namens "recenterrors" in der aktuellen Datenbank, die das gleiche Schema wie "logstable" aufweist und alle Fehler Datensätze der letzten Stunde enthält:

```kusto
.set RecentErrors <|
   LogsTable
   | where Level == "Error" and Timestamp > now() - time(1h)
```

Erstellen Sie eine neue Tabelle mit dem Namen "oldextents" in der aktuellen Datenbank, die eine einzelne Spalte ("extentid") aufweist und die Block-IDs aller Blöcke in der Datenbank enthält, die vor mehr als 30 Tagen erstellt wurden, basierend auf einer vorhandenen Tabelle mit dem Namen "myextents":

```kusto
.set async OldExtents <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

Fügen Sie Daten an eine vorhandene Tabelle mit dem Namen "oldextents" in der aktuellen Datenbank an, die eine einzelne Spalte ("extentid") aufweist und die Block-IDs aller Blöcke in der Datenbank enthält, die vor mehr als 30 Tagen erstellt wurden, während Sie den neuen Block mit Tags `tagA` und `tagB` basierend auf einer vorhandenen Tabelle mit dem Namen "myextents" markieren:

```kusto
.append OldExtents with(tags='["TagA","TagB"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```
 
Fügen Sie Daten an die Tabelle "oldextents" in der aktuellen Datenbank an (oder erstellen Sie die Tabelle, falls Sie noch nicht vorhanden ist), während Sie den neuen Wertebereich mit markieren `ingest-by:myTag` . Dies ist nur der Fall, wenn die Tabelle nicht bereits einen Block enthält `ingest-by:myTag` , der auf der Grundlage einer vorhandenen Tabelle mit dem Namen "myextents" gekennzeichnet ist:

```kusto
.set-or-append async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

Ersetzen Sie die Daten in der Tabelle "oldextents" in der aktuellen Datenbank (oder erstellen Sie die Tabelle, falls Sie noch nicht vorhanden ist), während Sie den neuen Wertebereich mit markieren `ingest-by:myTag` .

```kusto
.set-or-replace async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

Anfügen von Daten an die Tabelle "oldextents" in der aktuellen Datenbank, während die Erstellungszeit für den erstellten Block (n) auf einen bestimmten DateTime-Wert in der Vergangenheit festgelegt wurde:

```kusto
.append async OldExtents with(creationTime='2017-02-13T11:09:36.7992775Z') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

**Ausgabe zurückgeben**
 
Gibt Informationen zu den Blöcken zurück, die als Ergebnis des- `.set` oder-Befehls erstellt wurden `.append` .

**Beispielausgabe**

|Extentid |OriginalSize |Extentsize |CompressedSize |IndexSize |RowCount | 
|--|--|--|--|--|--|
|23a05ed6-376d-4119-b1fc-6493bcb05563 |1.291 |5882 |1568 |4314 |10 |