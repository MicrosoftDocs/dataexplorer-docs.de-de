---
title: 'Verwaltung von Blöcken (datenshards): Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird die Verwaltung von Blöcken (Data Shards) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: ca66beaad41796dff38a5bd5ce1fad2e0f41fc72
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550502"
---
# <a name="extents-data-shards-management"></a>Verwaltung von Blöcken (datenshards)

Datenshards werden in Kusto als **Blöcke** bezeichnet, und alle Befehle verwenden "Block" oder "Blöcke" als Synonym.

## <a name="show-extents"></a>. Anzeigen von Blöcken

**Cluster Ebene**

`.show` `cluster` `extents` [`hot`]

Zeigt Informationen zu Blöcken (datenshards) an, die im Cluster vorhanden sind.
Wenn `hot` angegeben wird, werden nur Blöcke angezeigt, die im aktiven Cache erwartet werden.

**Datenbankebene**

`.show``database` *DatabaseName* `extents` [ `(` *ExtentId1* `,` ... `,` *Extentidn* `)` ] [ `hot` ] [ `where` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag1* [ `and` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag2*...]]

Zeigt Informationen zu Blöcken (datenshards) an, die in der angegebenen Datenbank vorhanden sind.
Wenn `hot` angegeben wird, werden nur Blöcke angezeigt, die im aktiven Cache erwartet werden.

**Tabellenebene**

`.show``table` *TableName* `extents` [ `(` *ExtentId1* `,` ... `,` *Extentidn* `)` ] [ `hot` ] [ `where` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag1* [ `and` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag2*...]]

`.show``tables` `(` *TableName1* `,` ... `,` *TableNameN* `)` `extents`[ `(` *ExtentId1* `,` . `,` .. *Extentidn* `)` ] [ `hot` ] [ `where` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag1* [ `and` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag2*...]]

Zeigt Informationen zu Blöcken (datenshards) an, die in der angegebenen Tabelle (n) vorhanden sind (die Datenbank wird aus dem Kontext des Befehls entnommen).
Wenn `hot` angegeben wird, werden nur Blöcke angezeigt, die im aktiven Cache erwartet werden.

**Hinweise**

* Die Verwendung von Befehlen auf Datenbank-und/oder Tabellenebene ist wesentlich schneller als das Filtern (hinzufügen `| where DatabaseName == '...' and TableName == '...'` ) der Ergebnisse eines Befehls auf Cluster Ebene.
* Wenn die optionale Liste der Block-IDs angegeben wird, ist das zurückgegebene DataSet nur auf diese Blöcke beschränkt.
    * Dies ist viel schneller als das Filtern (hinzufügen `| where ExtentId in(...)` zu) der Ergebnisse von "Bare"-Befehlen.
* Für den Fall, dass `tags` Filter angegeben werden:
  * Die zurückgegebene Liste ist auf die Blöcke beschränkt, deren Tags-Auflistung *alle* bereitgestellten Tagfilter befolgt.
    * Dies ist viel schneller als das Filtern (hinzufügen `| where Tags has '...' and Tags contains '...'` ) der Ergebnisse von "Bare"-Befehlen.
  * `has`Filter sind Gleichheits Filter: Blöcke, die nicht mit einem der angegebenen Tags gekennzeichnet sind, werden herausgefiltert.
  * `!has`Filter sind negative Gleichheits Filter: Blöcke, die mit einem der angegebenen Tags gekennzeichnet sind, werden herausgefiltert.
  * `contains`bei Filtern werden unter Zeichen folgen Filter ohne Beachtung der Groß-/Kleinschreibung beachtet: Blöcke, die die angegebenen Zeichen folgen nicht als Teil Zeichenfolge ihrer Tags aufweisen, werden herausgefiltert. 
  * `!contains`bei Filtern wird die Groß-/Kleinschreibung von negativen Filtern ohne Berücksichtigung der Groß-/Kleinschreibung beachtet: Blöcke mit den angegebenen Zeichen folgen als Teil Zeichenfolge ihrer Tags werden herausgefiltert. 
  
  * **Beispiele**
    * `E`Der Block in der Tabelle `T` wird mit den Tags `aaa` und markiert `BBB` `ccc` .
    * Diese Abfrage gibt Folgendes zurück `E` : 
    
    ```kusto 
    .show table T extents where tags has 'aaa' and tags contains 'bb' 
    ``` 
    
    * Diese Abfrage gibt *nicht* zurück `E` (da Sie nicht mit einem Tag gekennzeichnet ist, das entspricht `aa` ):
    
    ```kusto 
    .show table T extents where tags has 'aa' and tags contains 'bb' 
    ``` 
    
    * Diese Abfrage gibt Folgendes zurück `E` : 
    
    ```kusto 
    .show table T extents where tags contains 'aaa' and tags contains 'bb' 
    ``` 

|Output-Parameter |Typ |BESCHREIBUNG 
|---|---|---
|Extentid |Guid |ID des Wertebereichs 
|DatabaseName |String |Datenbank, zu der der Block gehört 
|TableName |String |Tabelle, zu der Blöcke gehören 
|Maxkreatedon |Datetime |Datum und Uhrzeit, zu der der Block erstellt wurde (für einen zusammengeführten Block: die maximale Erstellungszeit zwischen den Quell Blöcken) 
|OriginalSize |Double |Ursprüngliche Größe in Byte der Blockdaten 
|Extentsize |Double |Größe des Wertebereichs im Arbeitsspeicher (komprimiert + Index) 
|CompressedSize |Double |Komprimierte Größe der Blockdaten im Arbeitsspeicher 
|IndexSize |Double |Index Größe der Blockdaten 
|Blöcke |Long |Umfang der Datenblöcke im Block 
|Segmente |Long |Umfang des Datensegments im Block 
|Assigneddatanodes |String | Veraltet (leere Zeichenfolge)
|Loadeddatanodes |String |Veraltet (leere Zeichenfolge)
|Extentcontainerid |String | ID des Block Blocks, in dem sich der Block befindet
|RowCount |Long |Zeilen Anzahl im Block
|Minkreatedon |Datetime |Datum und Uhrzeit der Erstellung des Wertebereichs (für einen zusammengeführten Block: die minimalen Erstellungs Zeiten zwischen den Quell Blöcken) 
|Tags|String|Tags, sofern vorhanden, für den Block definiert 
 
**Beispiele**

```kusto 
// Show volume of extents being created per hour in a specific database
.show database MyDatabase extents | summarize count(ExtentId) by MaxCreatedOn bin=time(1h) | render timechart  

// Show volume of data arriving by table per hour 
.show database MyDatabase extents  
| summarize sum(OriginalSize) by TableName, MaxCreatedOn bin=time(1h)  
| render timechart

// Show data size distribution by table 
.show database MyDatabase extents | summarize sum(OriginalSize) by TableName

// Show all extents in the database named 'GamesDB' 
.show database GamesDB extents

// Show all extents in the table named 'Games' 
.show table Games extents

// Show all extents in the tables named 'TaggingGames1' and 'TaggingGames2', tagged with both 'tag1' and 'tag2' 
.show tables (TaggingGames1,TaggingGames2) extents where tags has 'tag1' and tags has 'tag2'
``` 
 
## <a name="merge-extents"></a>. Merge-Blöcke

**Syntax**

`.merge``[async | dryrun]` *TableName* `(` *guid1* [ `,` *GUID2* ...] `)``[with(rebuild=true)]`

Mit diesem Befehl werden die Blöcke zusammengeführt (siehe: [Übersicht über die Blöcke (Daten-Shards)](extents-overview.md)), die durch ihre IDs in der angegebenen Tabelle angegeben werden.

Es gibt zwei Arten von Merge-Vorgängen: *Merge* (bei dem Indizes neu erstellt werden) und *neu erstellen* (wodurch die Daten vollständig wieder hergestellt werden).

* `async`: Der Vorgang wird asynchron ausgeführt. das Ergebnis ist eine Vorgangs-ID (GUID), die mit dem Befehl ausgeführt werden kann `.show operations <operation ID>` , um den Status & Status des Befehls anzuzeigen.
* `dryrun`: Mit dem Vorgang werden nur die Blöcke aufgelistet, die zusammengeführt werden sollen, aber Sie werden nicht tatsächlich zusammengeführt. 
* `with(rebuild=true)`: die Blöcke werden neu erstellt (die Daten werden erneut erstellt) anstatt zusammengeführt (Indizes werden neu erstellt).

**Ausgabe zurückgeben**

Output-Parameter |Typ |BESCHREIBUNG 
---|---|---
Originalextentid |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für den ursprünglichen Block in der Quell Tabelle, der in den Zielblock zusammengeführt wurde. 
Resultextentid |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für den Ergebnis Block, der aus den Quell Blöcken erstellt wurde. Bei Fehler: "failed" oder "abgebrochen".
Duration |Zeitraum |Der Zeitraum, der für den Abschluss des MERGE-Vorgangs benötigt wird.

**Beispiele**

Erstellt zwei spezifische Blöcke in neu `MyTable` , die asynchron ausgeführt werden.
```kusto
.merge async MyTable (e133f050-a1e2-4dad-8552-1f5cf47cab69, 0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687) with(rebuild=true)
```

Führt zwei spezifische Blöcke in zusammen `MyTable` , die synchron ausgeführt werden.
```kusto
.merge MyTable (12345050-a1e2-4dad-8552-1f5cf47cab69, 98765b2d-9dd2-4d2c-a45e-b24c65aa6687)
```

**Hinweise**
- Im allgemeinen `.merge` sollten Befehle nur selten manuell ausgeführt werden, und Sie werden automatisch im Hintergrund des Kusto-Clusters ausgeführt (entsprechend den für Tabellen und Datenbanken in der Datenbank definierten [Zusammenstellungs Richtlinien](mergepolicy.md) ).  
  - Allgemeine Überlegungen zu den Kriterien für das Zusammenführen mehrerer Blöcke zu einer einzigen Erweiterung sind unter [mergerichtlinie](mergepolicy.md)dokumentiert.
- `.merge`Vorgänge haben einen möglichen Endzustand von `Abandoned` (was bei Ausführung `.show operations` mit der Vorgangs-ID angezeigt werden kann): dieser Status weist darauf hin, dass die Quell Blöcke nicht zusammengeführt wurden, da einige der Quell Tabellen nicht mehr in der Quell Tabelle vorhanden sind.
  - Ein solcher Status sollte in folgenden Fällen auftreten:
     - Die Quell Blöcke wurden im Rahmen der Beibehaltungs Dauer der Tabelle gelöscht.
     - Die Quell Blöcke wurden in eine andere Tabelle verschoben.
     - Die Quell Tabelle wurde vollständig gelöscht oder umbenannt.

## <a name="move-extents"></a>Verschiebungs Blöcke verschieben

**Syntax**

`.move`[ `async` ] `extents` `all` `from` `table` *SourceTableName* `to` `table` *DestinationTableName*

`.move`[ `async` ] `extents` `(` *Guid1* [ `,` *GUID2* ...] `)` `from` `table` *SourceTableName* `to` `table` *DestinationTableName* 

`.move`[ `async` ] `extents` `to` `table` *DestinationTableName*-  <|  *Abfrage*

Dieser Befehl wird im Kontext einer bestimmten Datenbank ausgeführt und verschiebt die angegebenen Werte Blöcke aus der Quell Tabelle in die Ziel Tabelle, die transaktional ist.
Erfordert die [Table admin-Berechtigung](../management/access-control/role-based-authorization.md) für die Quell-und Ziel Tabellen.

* `async`(optional) gibt an, ob der Befehl asynchron ausgeführt wird (in diesem Fall wird eine Vorgangs-ID (GUID) zurückgegeben, und der Status des Vorgangs kann mithilfe des Befehls " [. Show Operations](operations.md#show-operations) " überwacht werden.)
    * Wenn diese Option verwendet wird, können die Ergebnisse einer erfolgreichen Ausführung mit dem Befehl " [Vorgangs Details anzeigen](operations.md#show-operation-details) " abgerufen werden.

Es gibt drei Möglichkeiten, um anzugeben, welche Blöcke verschoben werden sollen:
1. Alle Blöcke einer bestimmten Tabelle müssen verschoben werden.
2. Durch explizites angeben der Block-IDs in der Quell Tabelle.
3. Durch Bereitstellen einer Abfrage, deren Ergebnisse die Block-IDs in den Quell Tabellen angeben.

**Einschränkungen**
- Sowohl die Quell-als auch die Ziel Tabelle müssen sich in der Kontext Datenbank befinden. 
- Es wird erwartet, dass alle Spalten in der Quell Tabelle in der Ziel Tabelle mit demselben Namen und demselben Datentyp vorhanden sind.

**Angeben von Blöcken mit einer Abfrage**

```kusto 
.move extents to table TableName <| ...query... 
```

Die Blöcke werden mithilfe einer Kusto-Abfrage angegeben, die ein Recordset mit einer Spalte mit dem Namen "extentid" zurückgibt. 

**Rückgabe Ausgabe** (für die Synchronisierungs Ausführung)

Output-Parameter |Typ |BESCHREIBUNG 
---|---|---
Originalextentid |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für den ursprünglichen Block in der Quell Tabelle, der in die Ziel Tabelle verschoben wurde. 
Resultextentid |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für den Ergebnis Block, der aus der Quell Tabelle in die Ziel Tabelle verschoben wurde. Bei Fehler: "failed".
Details |Zeichenfolge |Schließt die Fehlerdetails ein, falls der Vorgang fehlschlägt.

**Beispiele**

Verschiebt alle Blöcke in der Tabelle in `MyTable` die Tabelle `MyOtherTable` .
```kusto
.move extents all from table MyTable to table MyOtherTable
```

Verschiebt 2 bestimmte Blöcke (durch ihre Block-IDs) von der Tabelle `MyTable` in die Tabelle `MyOtherTable` .
```kusto
.move extents (AE6CD250-BE62-4978-90F2-5CB7A10D16D7,399F9254-4751-49E3-8192-C1CA78020706) from table MyTable to table MyOtherTable
```

Verschiebt alle Blöcke von 2 bestimmten Tabellen ( `MyTable1` , `MyTable2` ) in die-Tabelle `MyOtherTable` .
```kusto
.move extents to table MyOtherTable <| .show tables (MyTable1,MyTable2) extents
```

**Beispielausgabe** 

|Originalextentid |Resultextentid| Details
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08F 42187872f| 
|2dfdef64-62a3-4950-A130-96b5b1083b5a |0F-7F -5e28-4b09-A000-e62eb41592df| 

## <a name="drop-extents"></a>. Drop-Blöcke

Löscht Blöcke aus der angegebenen Datenbank/Tabelle. Dieser Befehl weist mehrere Varianten auf: in einer Variante werden die Blöcke, die gelöscht werden sollen, durch eine Kusto-Abfrage angegeben, während in den anderen Varianten Blöcke mithilfe einer unten beschriebenen Mini Sprache angegeben werden. 
 
### <a name="specifying-extents-with-a-query"></a>Angeben von Blöcken mit einer Abfrage

Erfordert die [Table admin-Berechtigung](../management/access-control/role-based-authorization.md) für jede der Tabellen, die von der bereitgestellten Abfrage zurückgegebene Blöcke enthalten.

Löscht Blöcke (oder meldet Sie nur, ohne tatsächlich zu löschen, wenn `whatif` verwendet wird):

**Syntax**

`.drop``extents`[ `whatif` ] <| *Abfrage*

Die Blöcke werden mithilfe einer Kusto-Abfrage angegeben, die ein Recordset mit einer Spalte mit dem Namen "extentid" zurückgibt. 
 
### <a name="dropping-a-specific-extent"></a>Löschen eines bestimmten Wertebereichs

Erfordert die [Table admin-Berechtigung](../management/access-control/role-based-authorization.md) , wenn der Tabellenname angegeben ist.

Erfordert die [Administrator Berechtigung](../management/access-control/role-based-authorization.md) für den Fall, dass der Tabellenname nicht angegeben wird.

**Syntax**

`.drop``extent` *Extentid* [ `from` *TableName*]

### <a name="dropping-specific-multiple-extents"></a>Löschen bestimmter mehrerer Blöcke

Erfordert die [Table admin-Berechtigung](../management/access-control/role-based-authorization.md) , wenn der Tabellenname angegeben ist.

Erfordert die [Administrator Berechtigung](../management/access-control/role-based-authorization.md) für den Fall, dass der Tabellenname nicht angegeben wird.

**Syntax**

`.drop``extents` `(` *ExtentId1* `,` ... *Erweitertidn* `)` [ `from` *TableName*]

### <a name="specifying-extents-by-properties"></a>Angeben von Blöcken nach Eigenschaften

Erfordert die [Table admin-Berechtigung](../management/access-control/role-based-authorization.md) , wenn der Tabellenname angegeben ist.

Erfordert die [Administrator Berechtigung](../management/access-control/role-based-authorization.md) für den Fall, dass der Tabellenname nicht angegeben wird.

`.drop``extents`[ `older` *N* ( `days`  |  `hours` )] `from` (*TableName*  |  `all` `tables` ) [ `trim` `by` ( `extentsize`  |  `datasize` ) *N* ( `MB`  |  `GB`  |  `bytes` )] [ `limit` *limitanzahl*]

* `older`: Nur Blöcke, die älter als *N* Tage/Stunden sind, werden gelöscht. 
* `trim`: Der Vorgang schneidet die Daten in der Datenbank ab, bis die Summe der Blöcke der gewünschten Größe entspricht (MaxSize). 
* `limit`: Der Vorgang wird auf die Werte für die erste *limitanzahl* angewendet. 

Der-Befehl unterstützt den Emulations `.drop-pretend` Modus (anstelle von `.drop` ), der eine Ausgabe erzeugt, als ob der Befehl ausgeführt würde, ohne dass er tatsächlich ausgeführt würde.

**Beispiele**

Entfernen Sie alle Blöcke, die vor mehr als 10 Tagen erstellt wurden, aus allen Tabellen in der Datenbank.`MyDatabase`

```kusto
.drop extents <| .show database MyDatabase extents | where CreatedOn < now() - time(10d)
```

Entfernt alle Blöcke in Tabellen `Table1` und `Table2` , deren Erstellungszeit vor mehr als 10 Tagen betrug:

```kusto
.drop extents older 10 days from tables (Table1, Table2)
```

Emulations Modus: zeigt an, welche Blöcke vom Befehl entfernt werden (Parameter für die Block-ID sind für diesen Befehl nicht zutreffend):

```kusto
.drop-pretend extents older 10 days from all tables
```

Entfernt alle Blöcke aus "testtable":

```kusto
.drop extents from TestTable
```
 
**Ausgabe zurückgeben**

|Output-Parameter |Typ |BESCHREIBUNG 
|---|---|---
|Extentid |String |Extentid, die als Ergebnis des Befehls gelöscht wurde 
|TableName |String |Tabellenname, wobei Block zu gehört  
|CreatedOn |Datetime |Zeitstempel, der Informationen darüber enthält, wann der Block anfänglich erstellt wurde 
 
**Beispielausgabe** 

|Block-ID |Tabellenname |Erstellt am 
|---|---|---
|43c6e03f -1713-4ca7-a52a-5db8a4e8b87d |TestTable |2015-01-12 12:48:49.4298178 

## <a name="replace-extents"></a>. Replace Blöcke

**Syntax**

`.replace`[ `async` ] `extents` `in` `table` *DestinationTableName* - `<| 
{` *Abfrage für Blöcke, die aus der Tabellen*Abfrage für Blöcke gelöscht werden sollen, die `},{` *in die Tabelle verschoben werden* sollen`}`

Dieser Befehl wird im Kontext einer bestimmten Datenbank ausgeführt, verschiebt die angegebenen Blöcke aus den Quell Tabellen in die Ziel Tabelle und löscht die angegebenen Blöcke aus der Ziel Tabelle.
Alle Drop-und Move-Vorgänge werden in einer einzelnen Transaktion durchgeführt.

Erfordert die [Table admin-Berechtigung](../management/access-control/role-based-authorization.md) für die Quell-und Ziel Tabellen.

* `async`(optional) gibt an, ob der Befehl asynchron ausgeführt wird (in diesem Fall wird eine Vorgangs-ID (GUID) zurückgegeben, und der Status des Vorgangs kann mithilfe des Befehls " [. Show Operations](operations.md#show-operations) " überwacht werden.)
    * Wenn diese Option verwendet wird, können die Ergebnisse einer erfolgreichen Ausführung mit dem Befehl " [Vorgangs Details anzeigen](operations.md#show-operation-details) " abgerufen werden.

Die Angabe, welche Blöcke gelöscht oder verschoben werden sollen, erfolgt durch das Bereitstellen von 2 Abfragen.
- *Abfrage für Blöcke, die aus der Tabelle gelöscht werden sollen* : die Ergebnisse dieser Abfrage geben die Block-IDs an.  
, der aus der Ziel Tabelle gelöscht werden soll.
- *Abfrage für Blöcke, die in die Tabelle verschoben werden* sollen: die Ergebnisse dieser Abfrage geben die Block-IDs in den Quell Tabellen an, die in die Ziel Tabelle verschoben werden sollen.

Beide Abfragen sollten ein Recordset mit einer Spalte mit dem Namen "extentid" zurückgeben.

**Einschränkungen**
- Sowohl die Quell-als auch die Ziel Tabelle müssen sich in der Kontext Datenbank befinden. 
- Alle Blöcke, die von der *Abfrage für Blöcke angegeben werden, die aus der Tabelle gelöscht werden* sollen, werden in der Ziel Tabelle erwartet.
- Es wird erwartet, dass alle Spalten in den Quell Tabellen in der Ziel Tabelle mit demselben Namen und demselben Datentyp vorhanden sind.

**Rückgabe Ausgabe** (für die Synchronisierungs Ausführung)

Output-Parameter |Typ |BESCHREIBUNG 
---|---|---
Originalextentid |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für den ursprünglichen Block in der Quell Tabelle, der in die Ziel Tabelle verschoben wurde, oder-der Block in der Ziel Tabelle, der gelöscht wurde.
Resultextentid |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für den Ergebnis Block, der aus der Quell Tabelle in die Ziel Tabelle verschoben wurde, oder-leer, wenn der Wertebereich aus der Ziel Tabelle gelöscht wurde. Bei Fehler: "failed".
Details |Zeichenfolge |Schließt die Fehlerdetails ein, falls der Vorgang fehlschlägt.

> [!NOTE]
> Der Befehl schlägt fehl, wenn die Blöcke, die von den Blöcken zurückgegeben *werden, die aus der Tabellen Abfrage gelöscht werden,* in der Ziel Tabelle nicht vorhanden sind. Dies kann vorkommen, wenn die Blöcke zusammengeführt wurden, bevor der Replace-Befehl ausgeführt wurde. Um sicherzustellen, dass der Befehl für fehlende Blöcke fehlschlägt, überprüfen Sie, ob die Abfrage die erwarteten extentids zurückgibt. Der folgende Beispiel #1 schlägt fehl, wenn der zu löschende Block nicht in der Tabelle "myothertable" vorhanden ist. Beispiel #2 ist jedoch auch dann erfolgreich, wenn der zu löschte Block nicht vorhanden ist, da die abzurufte Abfrage keine Block-IDs zurückgegeben hat. 

**Beispiele**

Der folgende Befehl verschiebt alle Blöcke aus 2 bestimmten Tabellen ( `MyTable1` , `MyTable2` ) in eine Tabelle `MyOtherTable` und löscht alle Blöcke, die `MyOtherTable` mit markiert sind `drop-by:MyTag` :

```kusto
.replace extents in table MyOtherTable <|
    {
        .show table MyOtherTable extents where tags has 'drop-by:MyTag'
    },
    {
        .show tables (MyTable1,MyTable2) extents
    }
```

**Beispielausgabe** 

|Originalextentid |Resultextentid |Details
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08F 42187872f| 
|2dfdef64-62a3-4950-A130-96b5b1083b5a |0F-7F -5e28-4b09-A000-e62eb41592df| 


Mit den folgenden Befehlen werden alle Blöcke von einer bestimmten Tabelle ( `MyTable1` ) in eine Tabelle verschoben `MyOtherTable` und ein bestimmter Block in `MyOtherTable` mit der ID gelöscht:


```kusto
.replace extents in table MyOtherTable <|
    {
        print ExtentId = "2cca5844-8f0d-454e-bdad-299e978be5df"
    },
    {
        .show table MyTable1 extents 
    }
```

```kusto
.replace extents in table MyOtherTable  <|
    {
        .show table MyOtherTable extents
        | where ExtentId == guid(2cca5844-8f0d-454e-bdad-299e978be5df) 
    },
    {
        .show table MyTable1 extents 
    }
```

Der folgende Befehl implementiert eine idempotente Logik, sodass Werte Blöcke nur dann aus der Tabelle gelöscht `t_dest` werden, wenn Bereiche zum Verschieben von der Tabelle `t_source` in die Tabelle vorhanden sind `t_dest` :

```kusto
.replace async extents in table t_dest <|
{
    let any_extents_to_move = toscalar( 
        t_source
        | where extent_tags() has 'drop-by:blue'
        | summarize count() > 0
    );
    let extents_to_drop =
        t_dest
        | where any_extents_to_move and extent_tags() has 'drop-by:blue'
        | summarize by ExtentId = extent_id()
    ;
    extents_to_drop
},
{
    let extents_to_move = 
        t_source
        | where extent_tags() has 'drop-by:blue'
        | summarize by ExtentId = extent_id()
    ;
    extents_to_move
}
```

## <a name="drop-extent-tags"></a>. Drop Block-Tags

**Syntax**

`.drop`[ `async` ] `extent` `tags` `from` `table` *TableName* `(` '*Tag1*' [ `,` '*Tag2*' `,` ... `,` ' *TAGN*']`)`

`.drop`[ `async` ] `extent` `tags`  <|  *Abfrage*

* `async`(optional) gibt an, ob der Befehl asynchron ausgeführt wird (in diesem Fall wird eine Vorgangs-ID (GUID) zurückgegeben, und der Status des Vorgangs kann mithilfe des Befehls " [. Show Operations](operations.md#show-operations) " überwacht werden.)
    * Wenn diese Option verwendet wird, können die Ergebnisse einer erfolgreichen Ausführung mit dem Befehl " [Vorgangs Details anzeigen](operations.md#show-operation-details) " abgerufen werden.

Der Befehl wird im Kontext einer bestimmten Datenbank ausgeführt und löscht die angegebenen Block- [Tag (e)](extents-overview.md#extent-tagging) aus einem beliebigen Block in der bereitgestellten Datenbank und Tabelle, der alle Tags enthält.  

Es gibt zwei Möglichkeiten, um anzugeben, welche Tags aus welchen Blöcken entfernt werden sollen:

1. Durch explizites angeben der Tags, die aus allen Blöcken in der angegebenen Tabelle entfernt werden sollen.
2. Durch Bereitstellen einer Abfrage, deren Ergebnisse den Block-IDs in der Tabelle und den Foreach-Block angeben: die Tags, die entfernt werden sollen.

**Einschränkungen**
- Alle Blöcke müssen sich in der Kontext Datenbank befinden und müssen derselben Tabelle angehören. 

**Angeben von Blöcken mit einer Abfrage**

Erfordert die [Table admin-Berechtigung](../management/access-control/role-based-authorization.md) für alle Beteiligten Quell-und Ziel Tabellen.

```kusto 
.drop extent tags <| ...query... 
```

Die Blöcke und die zu löschenden Tags werden mithilfe einer Kusto-Abfrage angegeben, die ein Recordset mit einer Spalte mit dem Namen "extentid" und einer Spalte mit dem Namen "Tags" zurückgibt. 

*Hinweis*: Wenn Sie die [Kusto .NET-Client Bibliothek](../api/netfx/about-kusto-data.md)verwenden, können Sie die folgenden Methoden verwenden, um den gewünschten Befehl zu generieren:
- `CslCommandGenerator.GenerateExtentTagsDropByRegexCommand(string tableName, string regex)`
- `CslCommandGenerator.GenerateExtentTagsDropBySubstringCommand(string tableName, string substring)`

**Ausgabe zurückgeben**

Output-Parameter |Typ |BESCHREIBUNG 
---|---|---
Originalextentid |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für den ursprünglichen Block, dessen Tags geändert wurden (und im Rahmen des Vorgangs gelöscht werden). 
Resultextentid |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für den Ergebnis Block, der Tags geändert hat (und als Teil des Vorgangs erstellt und hinzugefügt wird). Bei Fehler: "failed".
Resultextenttags |Zeichenfolge |Die Auflistung von Tags, mit denen der Ergebnis Block markiert ist (falls vorhanden), oder "Null", wenn der Vorgang fehlschlägt.
Details |Zeichenfolge |Schließt die Fehlerdetails ein, falls der Vorgang fehlschlägt.

**Beispiele**

Löscht das `drop-by:Partition000` Tag aus einem beliebigen Block in einer Tabelle `MyOtherTable` , die mit diesem versehen ist.

```kusto
.drop extent tags from table MyOtherTable ('drop-by:Partition000')
```

Löscht die Tags `drop-by:20160810104500` , `a random tag` und/oder `drop-by:20160810` aus einem beliebigen Block in einer Tabelle, `My Table` die mit einem der beiden Tags markiert ist.

```kusto
.drop extent tags from table [My Table] ('drop-by:20160810104500','a random tag','drop-by:20160810')
```

Löscht alle `drop-by` Tags aus den Blöcken in der Tabelle `MyTable` .

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n') 
  | mv-expand Tags to typeof(string)
  | where Tags startswith 'drop-by'
```

Löscht alle Tags, die Regex entsprechen, `drop-by:StreamCreationTime_20160915(\d{6})` aus Blöcken in der Tabelle `MyTable` .

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n')
  | mv-expand Tags to typeof(string)
  | where Tags matches regex @"drop-by:StreamCreationTime_20160915(\d{6})"
```

**Beispielausgabe** 

|Originalextentid |Resultextentid | Resultextenttags | Details
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | Partition001 |
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | |
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08F 42187872f | Partition001 Partition002 |
|2dfdef64-62a3-4950-A130-96b5b1083b5a |0F-7F -5e28-4b09-A000-e62eb41592df | |

## <a name="alter-extent-tags"></a>. Alter Block-Tags

**Syntax**

`.alter`[ `async` ] `extent` `tags` `(` '*Tag1*' [ `,` '*Tag2*' `,` ... `,` ' *TAGN*'] `)`  <|  *Abfrage*

Der Befehl wird im Kontext einer bestimmten Datenbank ausgeführt und ändert die Block- [Tag (n)](extents-overview.md#extent-tagging) aller Blöcke, die von der angegebenen Abfrage zurückgegeben werden, in den bereitgestellten Satz von Tags.

Die Blöcke und die zu ändernden Tags werden mithilfe einer Kusto-Abfrage angegeben, die ein Recordset mit einer Spalte mit dem Namen "extentid" zurückgibt.

* `async`(optional) gibt an, ob der Befehl asynchron ausgeführt wird (in diesem Fall wird eine Vorgangs-ID (GUID) zurückgegeben, und der Status des Vorgangs kann mithilfe des Befehls " [. Show Operations](operations.md#show-operations) " überwacht werden.)
    * Wenn diese Option verwendet wird, können die Ergebnisse einer erfolgreichen Ausführung mit dem Befehl " [Vorgangs Details anzeigen](operations.md#show-operation-details) " abgerufen werden.

Erfordert die [Table admin-Berechtigung](../management/access-control/role-based-authorization.md) für alle beteiligten Tabellen.

**Einschränkungen**
- Alle Blöcke müssen sich in der Kontext Datenbank befinden und müssen derselben Tabelle angehören. 

**Ausgabe zurückgeben**

Output-Parameter |Typ |BESCHREIBUNG 
---|---|---
Originalextentid |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für den ursprünglichen Block, dessen Tags geändert wurden (und im Rahmen des Vorgangs gelöscht werden). 
Resultextentid |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für den Ergebnis Block, der Tags geändert hat (und als Teil des Vorgangs erstellt und hinzugefügt wird). Bei Fehler: "failed".
Resultextenttags |Zeichenfolge |Die Auflistung von Tags, mit denen der Ergebnis Block gekennzeichnet ist, oder "Null" für den Fall, dass der Vorgang fehlschlägt.
Details |Zeichenfolge |Schließt die Fehlerdetails ein, falls der Vorgang fehlschlägt.

**Beispiele**

Ändert die Tags aller Blöcke in der Tabelle in `MyTable` `MyTag` .

```kusto
.alter extent tags ('MyTag') <| .show table MyTable extents
```

Ändert die Tags aller Blöcke in der Tabelle `MyTable` , die mit gekennzeichnet sind, in `drop-by:MyTag` `drop-by:MyNewTag` und `MyOtherNewTag` .

```kusto
.alter extent tags ('drop-by:MyNewTag','MyOtherNewTag') <| .show table MyTable extents where tags has 'drop-by:MyTag'
```

**Beispielausgabe** 

|Originalextentid |Resultextentid | Resultextenttags | Details
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | Drop-by: mynewtag myothernewtag| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | Drop-by: mynewtag myothernewtag| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08F 42187872f | Drop-by: mynewtag myothernewtag| 
|2dfdef64-62a3-4950-A130-96b5b1083b5a |0F-7F -5e28-4b09-A000-e62eb41592df | Drop-by: mynewtag myothernewtag| 

