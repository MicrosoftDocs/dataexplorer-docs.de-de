---
title: 'Kusto-Abfrageerfassung (set, append, replace): Azure Data Explorer'
description: In diesem Artikel ist beschrieben, wie Daten aus einer Abfrage (.set, .append, .set-or-append, .set-or-replace) in Azure Data Explorer erfasst werden.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.localizationpriority: high
ms.openlocfilehash: 18959e2387a1a0faf92261dc3c35eca0db44c158
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512892"
---
# <a name="ingest-from-query-set-append-set-or-append-set-or-replace"></a>Erfassen aus Abfrage (.set, .append, .set-or-append, .set-or-replace)

Diese Befehle führen eine Abfrage oder einen Steuerungsbefehl aus und erfassen die Ergebnisse der Abfrage in einer Tabelle. Der Unterschied zwischen diesen Befehlen besteht darin, wie vorhandene oder nicht vorhandene Tabellen und Daten in ihnen verarbeitet werden.

|Befehl          |Wenn die Tabelle vorhanden ist                     |Wenn die Tabelle nicht vorhanden ist                    |
|-----------------|------------------------------------|------------------------------------------|
|`.set`           |Der Befehl schlägt fehl.                  |Die Tabelle wird erstellt, und die Daten werden erfasst.|
|`.append`        |Daten werden an die Tabelle angehängt.      |Der Befehl schlägt fehl.                        |
|`.set-or-append` |Daten werden an die Tabelle angehängt.      |Die Tabelle wird erstellt, und die Daten werden erfasst.|
|`.set-or-replace`|Daten ersetzen die Daten in der Tabelle.|Die Tabelle wird erstellt, und die Daten werden erfasst.|

**Syntax**

`.set` [`async`] *Tabellenname* [`with` `(`*Eigenschaftsname* `=` *Eigenschaftswert* [`,` ...]`)`] `<|` *AbfrageOderBefehl*

`.append` [`async`] *Tabellenname* [`with` `(`*Eigenschaftsname* `=` *Eigenschaftswert* [`,` ...`])`] `<|` *AbfrageOderBefehl*

`.set-or-append` [`async`] *Tabellenname* [`with` `(`*Eigenschaftsname* `=` *Eigenschaftswert* [`,` ...]`)`] `<|` *AbfrageOderBefehl*

`.set-or-replace` [`async`] *Tabellenname* [`with` `(`*Eigenschaftsname* `=` *Eigenschaftswert* [`,` ...]`)`] `<|` *AbfrageOderBefehl*

**Argumente**

* `async`: Ist dieses Argument angegeben, gibt der Befehl die Kontrolle sofort zurück, und die Erfassung erfolgt im Hintergrund. Die Ergebnisse des Befehls enthalten einen `OperationId`-Wert, der dann mit dem `.show operations`-Befehl verwendet werden kann, um den Erfassungsstatus und die Ergebnisse abzurufen.
* *Tabellenname*: Der Name der Tabelle, in der die Daten erfasst werden sollen.
  Der Tabellenname bezieht sich immer auf die Datenbank im Kontext.
* *Eigenschaftsname*, *Eigenschaftswert*: Eine beliebige Anzahl von Erfassungseigenschaften, die sich auf den Erfassungsvorgang auswirken.

 Die folgenden Erfassungseigenschaften werden unterstützt.

|Eigenschaft        |BESCHREIBUNG|
|----------------|-----------------------------------------------------------------------------------------------------------------------------|
|`creationTime`   | Der als ISO8601-Zeichenfolge formatierte datetime-Wert, der zum Zeitpunkt der Erstellung der erfassten Datenerweiterungen verwendet werden soll. Ohne Angabe wird der aktuelle Wert (`now()`) verwendet.|
|`extend_schema`  | Ein boolescher Wert, der den Befehl anweist, das Schema der Tabelle zu erweitern. Der Standardwert ist „false“. Diese Option betrifft nur `.append`-, `.set-or-append`- und `set-or-replace`-Befehle. Es sind nur Schemaerweiterungen zulässig, bei denen zusätzliche Spalten am Ende der Tabelle hinzugefügt werden.|
|`recreate_schema`  | Ein boolescher Wert, der angibt, ob der Befehl das Schema der Tabelle neu erstellen darf. Der Standardwert ist „false“. Diese Option betrifft nur den *set-or-replace*-Befehl. Diese Option hat Vorrang vor der „extend_schema“-Eigenschaft, wenn beide festgelegt sind.|
|`folder`         | Der Ordner, der der Tabelle zugewiesen werden soll. Ist die Tabelle bereits vorhanden, überschreibt diese Eigenschaft den Ordner der Tabelle.|
|`ingestIfNotExists`   | Ein Zeichenfolgenwert, der eine erfolgreiche Erfassung verhindert, wenn die Tabelle bereits Daten enthält, die mit einem `ingest-by:`-Tag mit identischem Wert gekennzeichnet sind.|
|`policy_ingestiontime`   | Ein boolescher Wert. Dieser Wert gibt an, ob die [IngestionTime-Richtlinie](../../management/ingestiontime-policy.md) für eine Tabelle aktiviert werden soll, die durch diesen Befehl erstellt wird. Der Standardwert ist „true“.|
|`tags`   | Eine JSON-Zeichenfolge, die angibt, welche Überprüfungen im Rahmen der Erfassung ausgeführt werden sollen.|
|`docstring`   | Eine Zeichenfolge, die die Tabelle dokumentiert.|

 Eigenschaft, die das Verhalten des Befehls steuert.

|Eigenschaft        |type    |BESCHREIBUNG|
|----------------|--------|-----------------------------------------------------------------------------------------------------------------------------|
|`distributed`   |`bool`  |Gibt an, dass der Befehl Daten von allen Knoten, die die Abfrage ausführen, parallel erfasst. Der Standardwert ist „false“.  Weitere Informationen finden Sie weiter unten im Abschnitt "Hinweise".|

* *AbfrageOderBefehl*: Der Text einer Abfrage oder ein Steuerungsbefehl, deren oder dessen Ergebnisse als zu erfassende Daten verwendet werden.

> [!NOTE]
> Es werden nur `.show`-Steuerungsbefehle unterstützt.

**Anmerkungen**

* `.set-or-replace` ersetzt die Daten in der Tabelle, sofern diese vorhanden ist. Der Befehl löscht die vorhandenen Datenshards oder erstellt die Zieltabelle, sofern diese noch nicht vorhanden ist.
  Das Tabellenschema wird beibehalten, es sei denn, eine der Erfassungseigenschaften `extend_schema` oder `recreate_schema` ist auf „true“ festgelegt. Wird das Schema geändert, erfolgt dies vor der tatsächlichen Datenerfassung in seiner eigenen Transaktion. Ist ein Erfassen der Daten nicht möglich, bedeutet das nicht, dass das Schema nicht geändert wurde.
* `.set-or-append`- und `.append`-Befehle behalten das Schema bei, es sei denn, die `extend_schema`-Erfassungseigenschaft ist auf „true“ festgelegt. Wird das Schema geändert, erfolgt dies vor der tatsächlichen Datenerfassung in seiner eigenen Transaktion. Ist ein Erfassen der Daten nicht möglich, bedeutet das nicht, dass das Schema nicht geändert wurde.
* Es wird empfohlen, die zu erfassenden Daten auf weniger als 1 GB pro Erfassungsvorgang beschränken. Ggf. sollten mehrere Erfassungsbefehle verwendet werden.
* Eine Datenerfassung ist ein ressourcenintensiver Vorgang, der sich auf gleichzeitige Aktivitäten im Cluster auswirken kann, so auch auf das Ausführen von Abfragen. Sie sollten vermeiden, zu viele derartige Befehle gleichzeitig auszuführen.
* Wenn das Resultsetschema mit dem der Zieltabelle abgeglichen wird, basiert der Vergleich auf den Spaltentypen. Es erfolgt kein Abgleichen der Spaltennamen. Stellen Sie sicher, dass die Schemaspalten der Abfrageergebnisse in derselben Reihenfolge wie die Tabelle vorliegen. Andernfalls werden Daten in der falschen Spalte erfasst.
* Ein Festlegen des `distributed`-Flags auf „true“ ist nützlich, wenn die von der Abfrage erzeugte Datenmenge 1 GB überschreitet und die Abfrage keine Serialisierung erfordert, sodass mehrere Knoten Ausgabe parallel erzeugen können.
  Sind die Abfrageergebnisse klein, sollten Sie dieses Flag nicht verwenden, da es dazu führen kann, dass unnötig viele kleine Datenshards generiert werden.

**Beispiele** 

Erstellen Sie eine neue Tabelle namens :::no-loc text="RecentErrors"::: in der Datenbank, die dasselbe Schema wie :::no-loc text="LogsTable"::: hat und alle Fehlerdatensätze der letzten Stunde enthält.

```kusto
.set RecentErrors <|
   LogsTable
   | where Level == "Error" and Timestamp > now() - time(1h)
```

Erstellen Sie eine neue Tabelle namens „OldExtents“ in der Datenbank, die eine einzige Spalte namens „ExtentId“ hat und die Block-IDs aller Blöcke (Extents) in der Datenbank enthält, die vor mehr als 30 Tagen erstellt wurden. Die Datenbank enthält eine Tabelle namens „MyExtents“.

```kusto
.set async OldExtents <|
   MyExtents 
   | where CreatedOn < now() - time(30d)
   | project ExtentId
```

Fügen Sie Daten an die vorhandene Tabelle „OldExtents“ in der aktuellen Datenbank an, die eine einzige Spalte namens „ExtentId“ hat und die Block-IDs aller Blöcke in der Datenbank enthält, die vor mehr als 30 Tagen erstellt wurden.
Markieren Sie den neue Block mit den Tags `tagA` und `tagB`, basierend auf der vorhandenen Tabelle „MyExtents“.

```kusto
.append OldExtents with(tags='["TagA","TagB"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId
```

Fügen Sie Daten an die Tabelle „OldExtents“ in der aktuellen Datenbank an, oder erstellen Sie die Tabelle, wenn sie noch nicht vorhanden ist. Kennzeichnen Sie den neuen Block mit `ingest-by:myTag`. Führen Sie diesen Schritt nur aus, wenn die Tabelle noch keinen mit `ingest-by:myTag` gekennzeichneten Block enthält, basierend auf der vorhandenen Tabelle „MyExtents“.

```kusto
.set-or-append async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <|
   MyExtents
   | where CreatedOn < now() - time(30d)
   | project ExtentId
```

Ersetzen Sie die Daten in der Tabelle „OldExtents“ in der aktuellen Datenbank an, oder erstellen Sie die Tabelle, wenn sie noch nicht vorhanden ist. Kennzeichnen Sie den neuen Block mit `ingest-by:myTag`.

```kusto
.set-or-replace async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId
```

Fügen Sie Daten an die Tabelle „OldExtents“ in der aktuellen Datenbank an, wozu Sie die Erstellungszeit für die erstellten Blöcke auf einen bestimmten Zeitpunkt in der Vergangenheit festlegen.

```kusto
.append async OldExtents with(creationTime='2017-02-13T11:09:36.7992775Z') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

**Zurückgegebene Ausgabe**
 
Gibt Informationen zu den Erweiterungen zurück, die aufgrund des `.set`- oder `.append`-Befehls erstellt wurden.

**Beispielausgabe**

|ExtentId |OriginalSize |ExtentSize |CompressedSize |IndexSize |RowCount | 
|--|--|--|--|--|--|
|23a05ed6-376d-4119-b1fc-6493bcb05563 |1.291 |5882 |1568 |4314 |10 |
