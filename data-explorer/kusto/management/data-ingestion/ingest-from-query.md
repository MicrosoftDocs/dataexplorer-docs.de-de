---
title: Von der Abfrage (.set, .append, .set-or-append, .set-or-replace) aufnehmen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird Ingest from query (.set, .append, .set-or-append, .set-or-replace) in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: fb0bbb06bb8d28dd3951a7faedf55b0d84155301
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521453"
---
# <a name="ingest-from-query-set-append-set-or-append-set-or-replace"></a>Von Der Abfrage (.set, .append, .set-or-append, .set-or-replace)

Diese Befehle führen eine Abfrage oder einen Steuerbefehl aus und erfassen die Ergebnisse der Abfrage in einer Tabelle. Der Unterschied zwischen diesen Befehlen besteht darin, wie sie vorhandene oder nicht vorhandene Tabellen und Daten behandeln:

|Get-Help          |Wenn Tabelle vorhanden ist                     |Wenn eine Tabelle nicht vorhanden ist                    |
|-----------------|------------------------------------|------------------------------------------|
|`.set`           |Der Befehl schlägt fehl.                  |Die Tabelle wird erstellt, und die Daten werden aufgenommen.|
|`.append`        |Daten werden an die Tabelle angehängt.      |Der Befehl schlägt fehl.                        |
|`.set-or-append` |Daten werden an die Tabelle angehängt.      |Die Tabelle wird erstellt, und die Daten werden aufgenommen.|
|`.set-or-replace`|Daten ersetzen die Daten in der Tabelle.|Die Tabelle wird erstellt, und die Daten werden aufgenommen.|

**Syntax**

`.set`[`async`] *Tabellenname* [`with` `(` *PropertyName* `=` *PropertyValue* [`,` ...] `)`] `<|` *QueryOrCommand*

`.append`[`async`] *TableName* [`with` `(` *PropertyName* `=` *PropertyValue* [`,` ... `])`] `<|` *QueryOrCommand*

`.set-or-append`[`async`] *Tabellenname* [`with` `(` *PropertyName* `=` *PropertyValue* [`,` ...] `)`] `<|` *QueryOrCommand*

`.set-or-replace`[`async`] *Tabellenname* [`with` `(` *PropertyName* `=` *PropertyValue* [`,` ...] `)`] `<|` *QueryOrCommand*

**Argumente**

* `async`: Wenn angegeben, wird der Befehl sofort zurückgegeben und die Aufnahme im Hintergrund fortgesetzt. Die Ergebnisse des Befehls `OperationId` enthalten einen Wert, `.show operation` der dann mit dem Befehl verwendet werden kann, um den Status und die Ergebnisse für den Abschluss der Aufnahme abzurufen.
* *TableName*: Der Name der Tabelle, in die Daten erfasst werden sollen.
  Der Tabellenname ist immer relativ zur Datenbank im Kontext.
* *PropertyName*, *PropertyValue*: Eine beliebige Anzahl von Aufnahmeeigenschaften, die sich auf den Aufnahmeprozess auswirken.

 Unterstützte Aufnahmeeigenschaften:

|Eigenschaft        |Beschreibung|
|----------------|-----------------------------------------------------------------------------------------------------------------------------|
|`creationTime`   | Der Datetime-Wert (formatiert als ISO8601-Zeichenfolge), der zur Erstellungszeit der aufgenommenen Datenausdehnungen verwendet werden soll. Wenn nicht angegeben, wird der aktuelle Wert (now()) verwendet.|
|`extend_schema`  | Ein boolescher Wert, der, falls angegeben, den Befehl anweist, das Schema der Tabelle zu erweitern (Standardwert auf false). Diese Option gilt nur für .append .set-or-append und set-or-replace-Befehle. Es sind nur Schemaerweiterungen zulässig, bei denen zusätzliche Spalten am Ende der Tabelle hinzugefügt werden.|
|`recreate_schema`  | Ein boolescher Wert, der, falls angegeben, beschreibt, ob der Befehl das Schema der Tabelle neu erstellen kann (Standardwert für false). Diese Option gilt nur für den Befehl set-or-replace. Diese Option hat Vorrang vor der extend_schema-Eigenschaft, wenn beide festgelegt sind.|
|`folder`         | Der Ordner, der der Tabelle zugewiesen werden soll. Wenn die Tabelle bereits vorhanden ist, überschreibt diese Eigenschaft den Ordner der Tabelle.|
|`ingestIfNotExists`   | Ein Zeichenfolgenwert, der, falls angegeben, verhindert, dass die Aufnahme erfolgreich ist, wenn die Tabelle bereits Daten enthält, die mit einem ingest-by:-Tag mit demselben Wert markiert sind.|
|`policy_ingestiontime`   | Ein boolescher Wert, der angibt, ob die [IngestionTime-Richtlinie](../../management/ingestiontime-policy.md) für eine Tabelle aktiviert werden soll, die durch diesen Befehl erstellt wird. Der Standardwert ist „true“.|
|`tags`   | Eine JSON-Zeichenfolge, die angibt, welche Überprüfungen im Rahmen der Erfassung ausgeführt werden sollen.|
|`docstring`   | Eine Zeichenfolge, die die Tabelle dokumentiert.|

  Darüber hinaus gibt es eine Eigenschaft, die das Verhalten des Befehls selbst steuert:

|Eigenschaft        |type    |Beschreibung|
|----------------|--------|-----------------------------------------------------------------------------------------------------------------------------|
|`distributed`   |`bool`  |Gibt an, dass der Befehl von allen Knoten erfasst wird, die die Abfrage parallel ausführen. (Standardwerte `false`auf .)  Siehe Anmerkungen unten.|

* *QueryOrCommand*: Der Text einer Abfrage oder eines Steuerelementbefehls, dessen Ergebnisse als Daten zum Aufnehmen verwendet werden.

> [!NOTE]
> Es `.show` werden nur Steuerbefehle unterstützt.

**Anmerkungen**

* `.set-or-replace`ersetzt die Daten der Tabelle, sofern sie vorhanden ist (löscht die vorhandenen Datenshards), oder erstellt die Zieltabelle, falls noch nicht vorhanden ist.
  Das Tabellenschema bleibt erhalten, `extend_schema` es `recreate_schema` sei denn, eine `true`der oder die Aufnahmeeigenschaft ist auf festgelegt. Wenn das Schema geändert wird, geschieht dies vor der tatsächlichen Datenerfassung in seiner eigenen Transaktion, sodass ein Fehlschalgen der Erfassung der Daten nicht bedeutet, dass das Schema nicht geändert wurde.
* `.set-or-append`und `.append` Befehle behalten das `extend_schema` Schema bei, es `true`sei denn, die Aufnahmeeigenschaft ist auf festgelegt. Wenn das Schema geändert wird, geschieht dies vor der tatsächlichen Datenerfassung in seiner eigenen Transaktion, sodass ein Fehlschalgen der Erfassung der Daten nicht bedeutet, dass das Schema nicht geändert wurde.
* Es wird **dringend empfohlen,** die Daten für die Aufnahme auf weniger als 1 GB pro Aufnahmevorgang zu beschränken. Bei Bedarf können mehrere Aufnahmebefehle verwendet werden.
* Die Datenerfassung ist ein ressourcenintensiver Vorgang, der sich auf gleichzeitige Aktivitäten im Cluster auswirken kann, einschließlich ausführender Abfragen. Vermeiden Sie es, "zu viele" solcher Befehle gleichzeitig auszuführen.
* Beim Abgleichen des Resultsetschemas mit dem der Zieltabelle basiert der Vergleich auf den Spaltentypen. Es gibt keine Übereinstimmung von Spaltennamen, also stellen Sie sicher, dass die Spalten des Abfrageergebnisschemas in der gleichen Reihenfolge wie die Tabelle sind, andernfalls werden Daten in die falsche Spalte aufgenommen.
* Das `distributed` Festlegen `true` des Flags auf ,, wenn die von der Abfrage erzeugte Datenmenge groß ist (über 1 GB Daten) **und** die Abfrage keine Serialisierung erfordert (damit mehrere Knoten parallel eine Ausgabe erzeugen können).
  Wenn die Abfrageergebnisse klein sind, wird es nicht empfohlen, dieses Flag zu verwenden, da es unnötig viele kleine Daten-Shards generieren kann.

**Beispiele** 

Erstellen Sie eine neue Tabelle mit dem Namen "RecentErrors" in der aktuellen Datenbank, die dasselbe Schema wie "LogsTable" enthält und alle Fehlerdatensätze der letzten Stunde enthält:

```kusto
.set RecentErrors <|
   LogsTable
   | where Level == "Error" and Timestamp > now() - time(1h)
```

Erstellen Sie eine neue Tabelle mit dem Namen "OldExtents" in der aktuellen Datenbank, die eine einzelne Spalte ("ExtentId") enthält und die Ausdehnungs-IDs aller Ausdehnungen in der Datenbank enthält, die vor mehr als 30 Tagen erstellt wurden, basierend auf einer vorhandenen Tabelle mit dem Namen "MyExtents":

```kusto
.set async OldExtents <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

Fügen Sie Daten an eine vorhandene Tabelle mit dem Namen "OldExtents" in der aktuellen Datenbank an, die eine einzelne Spalte ("ExtentId") enthält und die Ausdehnungs-IDs aller Ausdehnungen in der Datenbank enthält, die vor mehr als 30 Tagen erstellt wurden, während die neue Ausdehnung mit Tags `tagA` und `tagB`, basierend auf einer vorhandenen Tabelle namens "MyExtents" markiert wird:

```kusto
.append OldExtents with(tags='["TagA","TagB"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```
 
Fügen Sie Daten an die Tabelle "OldExtents" in der aktuellen Datenbank an (oder erstellen Sie `ingest-by:myTag`die Tabelle, wenn sie noch nicht vorhanden ist), während Sie die neue Ausdehnung mit markieren. Tun Sie dies nur, wenn die Tabelle `ingest-by:myTag`nicht bereits eine mit , basierend auf einer vorhandenen Tabelle mit dem Namen "MyExtents" markierte Ausdehnung enthält:

```kusto
.set-or-append async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

Ersetzen Sie die Daten in der Tabelle "OldExtents" in der aktuellen Datenbank (oder erstellen Sie `ingest-by:myTag`die Tabelle, wenn sie noch nicht vorhanden ist), während Sie die neue Ausdehnung mit markieren.

```kusto
.set-or-replace async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

Fügen Sie Daten an die Tabelle "OldExtents" in der aktuellen Datenbank an, während Sie die erstellte Erstellungszeit der Ausdehnung auf eine bestimmte Datumszeit in der Vergangenheit festlegen:

```kusto
.append async OldExtents with(creationTime='2017-02-13T11:09:36.7992775Z') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

**Return-Ausgabe**
 
Gibt Informationen zu den Ausdehnungen `.set` zurück, die als Ergebnis des Befehls or `.append` erstellt wurden.

**Beispielausgabe**

|ExtentId |OriginalGröße |ExtentSize |CompressedSize |IndexSize |RowCount | 
|--|--|--|--|--|--|
|23a05ed6-376d-4119-b1fc-6493bcb05563 |1.291 |5882 |1568 |4314 |10 |