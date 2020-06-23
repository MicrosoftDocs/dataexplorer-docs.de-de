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
ms.openlocfilehash: 5248b9d986845ff7f35085cef0100cf3ab4b90da
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264469"
---
# <a name="ingest-from-query-set-append-set-or-append-set-or-replace"></a>Erfassung von Abfragen (. Set,. Append,. Set-oder-Append,. Set-or-Replace)

Diese Befehle führen eine Abfrage oder einen Steuerungs Befehl aus und erfassen die Ergebnisse der Abfrage in einer Tabelle. Der Unterschied zwischen diesen Befehlen besteht darin, wie vorhandene oder nicht vorhandene Tabellen und Daten behandelt werden.

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

* `async`: Wenn dieser Wert angegeben ist, wird der Befehl sofort zurückgegeben und die Erfassung im Hintergrund fortgesetzt. Die Ergebnisse des Befehls enthalten einen Wert, der `OperationId` dann mit dem Befehl verwendet werden kann `.show operations` , um den Erfassungs Status und die Ergebnisse abzurufen.
* *TableName*: der Name der Tabelle, in der Daten erfasst werden sollen.
  Der Tabellenname bezieht sich immer auf die Datenbank im Kontext.
* *PropertyName*, *PropertyValue*: eine beliebige Anzahl von Erfassungs Eigenschaften, die sich auf den Erfassungsprozess auswirken.

 Erfassungs Eigenschaften, die unterstützt werden.

|Eigenschaft        |BESCHREIBUNG|
|----------------|-----------------------------------------------------------------------------------------------------------------------------|
|`creationTime`   | Der als ISO8601-Zeichenfolge formatierte DateTime-Wert, der zum Zeitpunkt der Erstellung der erfassten Datenblöcke verwendet werden soll. Wenn nicht angegeben, wird der aktuelle Wert ( `now()` ) verwendet.|
|`extend_schema`  | Ein boolescher Wert, der den Befehl anweist, das Schema der Tabelle zu erweitern, falls angegeben. Der Standardwert ist "false". Diese Option gilt nur für `.append` `.set-or-append` -,-und- `set-or-replace` Befehle. Die einzigen zulässigen Schema Erweiterungen verfügen über zusätzliche Spalten, die der Tabelle am Ende hinzugefügt werden.|
|`recreate_schema`  | Ein boolescher Wert, der ist. Wenn angegeben, wird beschrieben, ob der Befehl das Schema der Tabelle neu erstellen kann. Der Standardwert ist "false". Diese Option gilt nur für den Befehl *Set-oder-Replace* . Diese Option hat Vorrang vor der extend_schema-Eigenschaft, wenn beide festgelegt sind.|
|`folder`         | Der Ordner, der der Tabelle zugewiesen werden soll. Wenn die Tabelle bereits vorhanden ist, überschreibt diese Eigenschaft den Ordner der Tabelle.|
|`ingestIfNotExists`   | Ein Zeichen folgen Wert, der ist. Wenn angegeben, wird die Erfassung erfolgreich verhindert, wenn die Tabelle bereits Daten mit einem `ingest-by:` Tag mit demselben Wert markiert hat.|
|`policy_ingestiontime`   | Ein boolescher Wert. Wenn angegeben, wird beschrieben, ob die Erfassungs [Zeit Richtlinie](../../management/ingestiontime-policy.md) für eine Tabelle aktiviert werden soll, die mit diesem Befehl erstellt wird. Der Standardwert ist "true".|
|`tags`   | Eine JSON-Zeichenfolge, die angibt, welche Validierungen während der Erfassung ausgeführt werden.|
|`docstring`   | Eine Zeichenfolge, die die Tabelle dokumentiert|

 Eigenschaft, die das Verhalten des Befehls steuert.

|Eigenschaft        |type    |BESCHREIBUNG|
|----------------|--------|-----------------------------------------------------------------------------------------------------------------------------|
|`distributed`   |`bool`  |Gibt an, dass der Befehl von allen Knoten erfasst wird, die die Abfrage parallel ausführen. Der Standardwert ist "false".  Weitere Informationen finden Sie in den Hinweisen weiter unten.|

* *Queryorcommand*: der Text einer Abfrage oder ein Steuerelement Befehl, dessen Ergebnisse als Daten für die Erfassung verwendet werden.

> [!NOTE]
> Nur `.show` Steuerungsbefehle werden unterstützt.

**Anmerkungen**

* `.set-or-replace`ersetzt die Daten der Tabelle, falls vorhanden. Die vorhandenen datenshards werden gelöscht, oder die Ziel Tabelle wird erstellt, sofern nicht bereits vorhanden.
  Das Tabellen Schema wird beibehalten, es sei denn, eine der-oder-Erfassungs `extend_schema` `recreate_schema` Eigenschaften ist auf "true" festgelegt. Wenn das Schema geändert wird, erfolgt dies vor der tatsächlichen Datenerfassung in der eigenen Transaktion. Wenn die Daten nicht erfasst werden, bedeutet das nicht, dass das Schema nicht geändert wurde.
* `.set-or-append``.append`die Befehle und behalten das Schema bei, es sei denn, die Erfassungs `extend_schema` Eigenschaft ist auf "true" festgelegt. Wenn das Schema geändert wird, erfolgt dies vor der tatsächlichen Datenerfassung in der eigenen Transaktion. Wenn die Daten nicht erfasst werden, bedeutet das nicht, dass das Schema nicht geändert wurde.
* Es wird empfohlen, die Daten für die Erfassung auf weniger als 1 GB pro Erfassungs Vorgang zu beschränken. Ggf. können mehrere Erfassungs Befehle verwendet werden.
* Die Datenerfassung ist ein ressourcenintensiver Vorgang, der sich auf gleichzeitige Aktivitäten im Cluster auswirken kann, einschließlich der Ausführung von Abfragen. Vermeiden Sie, zu viele derartige Befehle gleichzeitig auszuführen.
* Wenn das ResultSetSchema mit dem der Ziel Tabelle übereinstimmt, basiert der Vergleich auf den Spaltentypen. Es gibt keine Übereinstimmung von Spaltennamen. Stellen Sie sicher, dass sich die Schema Spalten der Abfrageergebnisse in derselben Reihenfolge wie die Tabelle befinden. Andernfalls werden die Daten in die falsche Spalte aufgenommen.
* Das `distributed` Flag auf "true" festzulegen, ist nützlich, wenn die von der Abfrage erzeugte Datenmenge groß ist, 1 GB überschreitet und die Abfrage keine Serialisierung erfordert, sodass mehrere Knoten eine parallele Ausgabe erzeugen können.
  Wenn die Abfrageergebnisse klein sind, verwenden Sie dieses Flag nicht, da es unnötig viele kleine datenshards generieren kann.

**Beispiele** 

Erstellen Sie eine neue Tabelle namens "recenterrors" in der Datenbank, die das gleiche Schema wie "logstable" aufweist und alle Fehler Datensätze der letzten Stunde enthält.

```kusto
.set RecentErrors <|
   LogsTable
   | where Level == "Error" and Timestamp > now() - time(1h)
```

Erstellen Sie eine neue Tabelle mit dem Namen "oldextents" in der Datenbank, die eine einzelne Spalte mit dem Namen "extentid" aufweist und die Block-IDs aller Blöcke in der Datenbank enthält, die seit mehr als 30 Tagen erstellt wurden. Die Datenbank enthält eine vorhandene Tabelle mit dem Namen "myextents".

```kusto
.set async OldExtents <|
   MyExtents 
   | where CreatedOn < now() - time(30d)
   | project ExtentId
```

Fügen Sie Daten an eine vorhandene Tabelle mit dem Namen "oldextents" in der aktuellen Datenbank mit der einzelnen Spalte "extentid" an, und halten Sie die Block-IDs aller Blöcke in der Datenbank, die seit mehr als 30 Tagen erstellt wurden.
Markieren Sie den neuen Block mit Tags `tagA` und `tagB` , basierend auf einer vorhandenen Tabelle mit dem Namen "myextents".

```kusto
.append OldExtents with(tags='["TagA","TagB"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId
```

Fügen Sie Daten an die Tabelle "oldextents" in der aktuellen Datenbank an, oder erstellen Sie die Tabelle, wenn Sie nicht bereits vorhanden ist. Markieren Sie den neuen Wertebereich mit `ingest-by:myTag` . Dies ist nur der Fall, wenn die Tabelle nicht bereits einen Block enthält `ingest-by:myTag` , der auf der Grundlage einer vorhandenen Tabelle mit dem Namen "myextents" gekennzeichnet ist.

```kusto
.set-or-append async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <|
   MyExtents
   | where CreatedOn < now() - time(30d)
   | project ExtentId
```

Ersetzen Sie die Daten in der Tabelle "oldextents" in der aktuellen Datenbank, oder erstellen Sie die Tabelle, wenn Sie nicht bereits vorhanden ist. Markieren Sie den neuen Wertebereich mit `ingest-by:myTag` .

```kusto
.set-or-replace async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId
```

Fügen Sie Daten an die Tabelle "oldextents" in der aktuellen Datenbank an, während Sie die Erstellungszeit der erstellten Blöcke auf einen bestimmten DateTime-Wert in der Vergangenheit festlegen.

```kusto
.append async OldExtents with(creationTime='2017-02-13T11:09:36.7992775Z') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

**Ausgabe zurückgeben**
 
Gibt Informationen zu den Blöcken zurück, die aufgrund des-oder-Befehls erstellt wurden `.set` `.append` .

**Beispielausgabe**

|Extentid |OriginalSize |Extentsize |CompressedSize |IndexSize |RowCount | 
|--|--|--|--|--|--|
|23a05ed6-376d-4119-b1fc-6493bcb05563 |1.291 |5882 |1568 |4314 |10 |
