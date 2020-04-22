---
title: Verwaltung von Ausdehnungen (Datenshards) - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die Verwaltung von Ausdehnungen (Datenshards) in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: e94b830809bf079e612b477292d00d2dbe85e60e
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744728"
---
# <a name="extents-data-shards-management"></a>Extents (Data Shards) Management

Datenshards werden in Kusto **als Ausdehnungen** bezeichnet, und alle Befehle verwenden "Extent" oder "Extents" als Synonym.

## <a name="show-extents"></a>.zeigen Ausdehnungen

**Clusterebene**

`.show` `cluster` `extents` [`hot`]

Zeigt Informationen zu Ausdehnungen (Datenshards) an, die im Cluster vorhanden sind.
Wenn `hot` angegeben ist - zeigt nur Ausdehnungen an, die sich voraussichtlich im heißen Cache befinden.

**Datenbankebene**

`.show``database` *DatabaseName* `extents` `(`[*ExtentId1*`,`... `,` *ExtentIdN*`)`]`hot`[`where` `tags` `has`|`contains`|`!has`|`!contains`] [`and` `tags` (`has`|`contains`|`!has`|`!contains`) *Tag1* [ ( ) *Tag2*...]]

Zeigt Informationen zu Ausdehnungen (Datenshards) an, die in der angegebenen Datenbank vorhanden sind.
Wenn `hot` angegeben ist - zeigt nur Ausdehnungen an, die im heißen Cache erwartet werden.

**Tabellenebene**

`.show``table` *Tabellenname* `extents` `(`[*ExtentId1*`,`... `,` *ExtentIdN*`)`]`hot`[`where` `tags` `has`|`contains`|`!has`|`!contains`] [`and` `tags` (`has`|`contains`|`!has`|`!contains`) *Tag1* [ ( ) *Tag2*...]]

`.show``tables` `,`TableName1 ... *TableName1* `(` `,` *TableNameN* `)` `extents` `(`[*ExtentId1*`,`... `,` *ExtentIdN*`)`]`hot`[`where` `tags` `has`|`contains`|`!has`|`!contains`] [`and` `tags` (`has`|`contains`|`!has`|`!contains`) *Tag1* [ ( ) *Tag2*...]]

Zeigt Informationen zu Ausdehnungen (Datenshards) an, die in den angegebenen Tabellen vorhanden sind (die Datenbank wird aus dem Kontext des Befehls übernommen).
Wenn `hot` angegeben ist - zeigt nur Ausdehnungen an, die im heißen Cache erwartet werden.

**Hinweise**

* Die Verwendung von Befehlen auf Datenbank- und/oder Tabellenebene ist viel schneller als das Filtern (Hinzufügen) `| where DatabaseName == '...' and TableName == '...'`der Ergebnisse eines Befehls auf Clusterebene.
* Wenn die optionale Liste der Ausdehnungs-IDs bereitgestellt wird, ist der zurückgegebene Datensatz nur auf diese Erweiterungen beschränkt.
    * Auch dies ist viel schneller `| where ExtentId in(...)` als das Filtern (Hinzufügen) der Ergebnisse von "bare"-Befehlen.
* Falls `tags` Filter angegeben werden:
  * Die zurückgegebene Liste ist auf die Ausdehnungen beschränkt, deren Tags-Auflistung *allen* bereitgestellten Tags-Filtern gehorcht.
    * Auch dies ist viel schneller `| where Tags has '...' and Tags contains '...'`als das Filtern (Hinzufügen) der Ergebnisse von "bare" Befehlen.
  * `has`Filter sind Gleichheitsfilter: Ausdehnungen, die nicht mit einem der angegebenen Tags markiert sind, werden herausgefiltert.
  * `!has`Filter sind Gleichheitsnegativfilter: Ausdehnungen, die mit einem der angegebenen Tags markiert sind, werden herausgefiltert.
  * `contains`Bei Filtern handelt es sich um Talsothringfilter ohne Groß-/Kleinschreibung: Ausdehnungen, die nicht über die angegebenen Zeichenfolgen als Teilzeichenfolge eines ihrer Tags verfügen, werden herausgefiltert. 
  * `!contains`Bei Filtern handelt es sich um negativer Filter ohne Groß-/Kleinschreibung: Ausdehnungen, die die angegebenen Zeichenfolgen als Teilzeichenfolge eines ihrer Tags aufweisen, werden herausgefiltert. 
  
  * **Beispiele**
    * Die `E` Ausdehnung in der `aaa` `BBB` Tabelle `ccc` `T` ist mit Tags und gekennzeichnet.
    * Diese Abfrage `E`gibt zurück: 
    
    ```kusto 
    .show table T extents where tags has 'aaa' and tags contains 'bb' 
    ``` 
    
    * Diese Abfrage *not* wird `E` nicht zurückgegeben (da sie nicht mit `aa`einem Tag markiert ist, der gleich ist):
    
    ```kusto 
    .show table T extents where tags has 'aa' and tags contains 'bb' 
    ``` 
    
    * Diese Abfrage `E`gibt zurück: 
    
    ```kusto 
    .show table T extents where tags contains 'aaa' and tags contains 'bb' 
    ``` 

|Ausgabeparameter |type |BESCHREIBUNG 
|---|---|---
|ExtentId |Guid |ID der Ausdehnung 
|DatabaseName |String |Datenbank, zu der die Ausdehnung gehört, 
|TableName |String |Tabelle, zu der Ausdehnungen gehören 
|MaxCreatedOn |Datetime |Datums-/Uhrzeit, zu dem die Ausdehnung erstellt wurde (für eine zusammengeführte Ausdehnung - das Maximum der Erstellungszeiten zwischen Quellausdehnungen) 
|OriginalGröße |Double |Originalgröße in Bytes der Ausdehnungsdaten 
|ExtentSize |Double |Größe der Ausdehnung im Arbeitsspeicher (komprimiert + Index) 
|CompressedSize |Double |Komprimierte Größe der Ausdehnungsdaten im Arbeitsspeicher 
|IndexSize |Double |Indexgröße der Ausdehnungsdaten 
|Blöcke |Long |Menge der Datenblöcke in der Ausdehnung 
|Segmente |Long |Menge des Datensegments in der Ausdehnung 
|AssignedDataNodes |String | Veraltet (eine leere Zeichenfolge)
|LoadedDataNodes |String |Veraltet (eine leere Zeichenfolge)
|ExtentContainerId |String | ID des Ausdehnungsbehälters ist die Ausdehnung in
|RowCount |Long |Anzahl der Zeilen in der Ausdehnung
|MinCreatedOn |Datetime |Datums-/Uhrzeit, zu dem die Ausdehnung erstellt wurde (für eine zusammengeführte Ausdehnung - das Minimum der Erstellungszeiten zwischen Quellausdehnungen) 
|`Tags`|String|Tags, falls vorhanden, für die Ausdehnung definiert 
 
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
 
## <a name="merge-extents"></a>.merge-Ausdehnungen

**Syntax**

`.merge``[async | dryrun]` *TableName* `(` *GUID1* [`,` *GUID2* ...] `)``[with(rebuild=true)]`

Dieser Befehl führt die Ausdehnungen (siehe: [Extents (Data Shards) Overview](extents-overview.md)) zusammen, die durch ihre IDs in der angegebenen Tabelle angegeben sind.

Es gibt 2 Varianten für Mergevorgänge: *Merge* (das Indizes neu erstellt) und *Rebuild* (wodurch die Daten vollständig wieder aufgenommen werden).

* `async`: Der Vorgang wird asynchron ausgeführt - das Ergebnis ist eine Operation `.show operations <operation ID>` ID (GUID), mit der man ausführen kann, um den Status des Befehls & Status anzuzeigen.
* `dryrun`: Der Vorgang listet nur die Ausdehnungen auf, die zusammengeführt werden sollen, führt sie jedoch nicht zusammen. 
* `with(rebuild=true)`: Die Ausdehnungen werden neu erstellt (Daten werden neu verwendet) anstatt zusammengeführt (Indizes werden neu erstellt).

**Return-Ausgabe**

Ausgabeparameter |type |BESCHREIBUNG 
---|---|---
OriginalExtentId |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für die ursprüngliche Ausdehnung in der Quelltabelle, der mit der Zielausdehnung zusammengeführt wurde. 
ResultExtentId |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für die Ergebnisausdehnung, die aus den Quellausdehnungen erstellt wurde. Nach einem Fehler - "Fehlgeschlagen" oder "Verlassen".
Duration |Zeitraum |Der Zeitraum, der zum Abschließen des Zusammenführungsvorgangs benötigte.

**Beispiele**

Erstellt zwei spezifische Ausdehnungen in `MyTable`, die asynchron ausgeführt werden
```kusto
.merge async MyTable (e133f050-a1e2-4dad-8552-1f5cf47cab69, 0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687) with(rebuild=true)
```

Führt zwei spezifische Ausdehnungen in `MyTable`zusammen, die synchron ausgeführt werden
```kusto
.merge MyTable (12345050-a1e2-4dad-8552-1f5cf47cab69, 98765b2d-9dd2-4d2c-a45e-b24c65aa6687)
```

**Hinweise**
- Im Allgemeinen `.merge` sollten Befehle selten manuell ausgeführt werden, und sie werden kontinuierlich automatisch im Hintergrund des Kusto-Clusters ausgeführt (gemäß den [Mergerichtlinien,](mergepolicy.md) die für Tabellen und Datenbanken darin definiert sind).  
  - Allgemeine Überlegungen zu den Kriterien für die Zusammenführung mehrerer Ausdehnungen zu einer einzigen werden unter [Merge-Richtlinie](mergepolicy.md)dokumentiert.
- `.merge`Vorgänge haben einen möglichen `Abandoned` Endzustand von (der bei der Ausführung `.show operations` mit der Vorgangs-ID angezeigt werden kann) - dieser Zustand deutet darauf hin, dass die Quellausdehnungen nicht zusammengeführt wurden, da einige der Quellausdehnungen in der Quelltabelle nicht mehr vorhanden sind.
  - Ein solcher Zustand wird voraussichtlich in Fällen wie:
     - Die Quellausdehnungen wurden als Teil der Aufbewahrung der Tabelle gelöscht.
     - Die Quellausdehnungen wurden in eine andere Tabelle verschoben.
     - Die Quelltabelle wurde vollständig gelöscht oder umbenannt.

## <a name="move-extents"></a>.move-Ausdehnungen

**Syntax**

`.move`[`async` `extents` `all` `from` ] `table` *SourceTableName* `to` `table` *DestinationTableName*

`.move`[`async` `extents` `(` ] *GUID1* [`,` *GUID2* ...] `)` `table` *DestinationTableName* *SourceTableName* SourceTableName `to` DestinationTableName `from` `table` 

`.move`[`async` `extents` `to` `table` ] *DestinationTableName-Abfrage* <| *query*

Dieser Befehl wird im Kontext einer bestimmten Datenbank ausgeführt und verschiebt die angegebenen Ausdehnungen aus der Quelltabelle in die Zieltabelle transaktional.
Erfordert [Die Berechtigung "Tabellenadministrator"](../management/access-control/role-based-authorization.md) für die Quell- und Zieltabellen.

* `async`(optional) gibt an, ob der Befehl asynchron ausgeführt wird (in diesem Fall wird eine Operation ID (Guid) zurückgegeben, und der Status des Vorgangs kann mit dem Befehl [.show operations](operations.md#show-operations) überwacht werden).
    * Falls diese Option verwendet wird, können die Ergebnisse einer erfolgreichen Ausführung mit dem Befehl [.show operation details](operations.md#show-operation-details) abgerufen werden).

Es gibt drei Möglichkeiten, anzugeben, welche Ausdehnungen verschoben werden sollen:
1. Alle Ausdehnungen einer bestimmten Tabelle sind zu verschieben.
2. Durch explizite Angabe der Ausdehnungs-IDs in der Quelltabelle.
3. Durch Bereitstellen einer Abfrage, deren Ergebnisse die Ausdehnungs-IDs in der Quelltabelle(n) angeben.

**Einschränkungen**
- Sowohl Quell- als auch Zieltabellen müssen sich in der Kontextdatenbank befinden. 
- Es wird erwartet, dass alle Spalten in der Quelltabelle in der Zieltabelle mit demselben Namen und Datentyp vorhanden sind.

**Angeben von Ausdehnungen mit einer Abfrage**

```kusto 
.move extents to table TableName <| ...query... 
```

Die Ausdehnungen werden mithilfe einer Kusto-Abfrage angegeben, die ein Recordset mit einer Spalte namens "ExtentId" zurückgibt. 

**Return-Ausgabe** (für Sync-Ausführung)

Ausgabeparameter |type |BESCHREIBUNG 
---|---|---
OriginalExtentId |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für die ursprüngliche Ausdehnung in der Quelltabelle, die in die Zieltabelle verschoben wurde. 
ResultExtentId |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für die Ergebnisausdehnung, die von der Quelltabelle in die Zieltabelle verschoben wurde. Nach einem Fehler - "Fehlgeschlagen".
Details |Zeichenfolge |Schließt die Fehlerdetails ein, falls der Vorgang fehlschlägt.

**Beispiele**

Verschiebt alle Ausdehnungen in der Tabelle `MyTable` in die Tabelle `MyOtherTable`.
```kusto
.move extents all from table MyTable to table MyOtherTable
```

Verschiebt 2 spezifische Ausdehnungen (nach ihrer `MyTable` Ausdehnungs-IDs) von Tabelle zu Tabelle `MyOtherTable`.
```kusto
.move extents (AE6CD250-BE62-4978-90F2-5CB7A10D16D7,399F9254-4751-49E3-8192-C1CA78020706) from table MyTable to table MyOtherTable
```

Verschiebt alle Ausdehnungen`MyTable1`von `MyTable2`2 `MyOtherTable`spezifischen Tabellen ( , ) in die Tabelle .
```kusto
.move extents to table MyOtherTable <| .show tables (MyTable1,MyTable2) extents
```

**Beispielausgabe** 

|OriginalExtentId |ResultExtentId| Details
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df| 

## <a name="drop-extents"></a>.drop Ausdehnungen

Löscht Ausdehnungen aus der angegebenen Datenbank / Tabelle. Dieser Befehl hat mehrere Varianten: In einer Variante werden die zu fallende Ausdehnung durch eine Kusto-Abfrage angegeben, während in den anderen Varianten Ausdehnungen mit einer unten beschriebenen Minisprache angegeben werden. 
 
### <a name="specifying-extents-with-a-query"></a>Angeben von Ausdehnungen mit einer Abfrage

Erfordert [Die Berechtigung "Tabellenadministrator"](../management/access-control/role-based-authorization.md) für jede der Tabellen, deren Ausdehnungen von der bereitgestellten Abfrage zurückgegeben werden.

Drops Ausdehnungen (oder meldet sie `whatif` einfach, ohne tatsächlich fallen zu lassen, wenn verwendet wird):

**Syntax**

`.drop``extents` [`whatif`] <| *Abfrage*

Die Ausdehnungen werden mithilfe einer Kusto-Abfrage angegeben, die ein Recordset mit einer Spalte namens "ExtentId" zurückgibt. 
 
### <a name="dropping-a-specific-extent"></a>Löschen einer bestimmten Ausdehnung

Erfordert [Table admin-Berechtigung,](../management/access-control/role-based-authorization.md) falls der Tabellenname angegeben ist.

Erfordert [Datenbankadministratorberechtigung,](../management/access-control/role-based-authorization.md) falls der Tabellenname nicht angegeben ist.

**Syntax**

`.drop``extent` *ExtentId* `from` [ *Tabellenname*]

### <a name="dropping-specific-multiple-extents"></a>Löschen bestimmter mehrfacher Ausdehnungen

Erfordert [Table admin-Berechtigung,](../management/access-control/role-based-authorization.md) falls der Tabellenname angegeben ist.

Erfordert [Datenbankadministratorberechtigung,](../management/access-control/role-based-authorization.md) falls der Tabellenname nicht angegeben ist.

**Syntax**

`.drop``extents` `,`ExtentId1 ... *ExtentId1* `(` *ExtentIdN* `)` `from` [ *Tabellenname*]

### <a name="specifying-extents-by-properties"></a>Angeben von Ausdehnungen nach Eigenschaften

Erfordert [Table admin-Berechtigung,](../management/access-control/role-based-authorization.md) falls der Tabellenname angegeben ist.

Erfordert [Datenbankadministratorberechtigung,](../management/access-control/role-based-authorization.md) falls der Tabellenname nicht angegeben ist.

`.drop``extents` [`older` *N* N`days` | `hours`( `from` )] (`trim` `by` `extentsize` | *TableName*  |  `all` `tables`) [`limit` (`datasize`) *N* (`MB` | `GB` | `bytes`)] [ *LimitCount*]

* `older`: Nur Ausdehnungen, die älter als *N* Tage/Stunden sind, werden verworfen. 
* `trim`: Der Vorgang schneidet die Daten in der Datenbank, bis die Summe der Ausdehnungen mit der gewünschten Größe übereinstimmt (MaxSize) 
* `limit`: Der Vorgang wird *LimitCount* auf die ersten LimitCount-Ausdehnungen angewendet 

Der Befehl unterstützt`.drop-pretend` den `.drop`Emulationsmodus (anstelle von ) , der eine Ausgabe erzeugt, als ob der Befehl ausgeführt hätte, ohne ihn tatsächlich auszuführen.

**Beispiele**

Entfernen aller Vor mehr als 10 Tage erstellten Erweiterungen aus allen Tabellen in der Datenbank`MyDatabase`

```kusto
.drop extents <| .show database MyDatabase extents | where CreatedOn < now() - time(10d)
```

Entfernt alle Ausdehnungen `Table1` `Table2`in Tabellen und , deren Erstellungszeit vor über 10 Tagen liegt:

```kusto
.drop extents older 10 days from tables (Table1, Table2)
```

Emulationsmodus: zeigt an, welche Ausdehnungen durch den Befehl entfernt würden (der Parameter "Ausdehnungs-ID" ist für diesen Befehl nicht anwendbar):

```kusto
.drop-pretend extents older 10 days from all tables
```

Entfernt alle Ausdehnungen aus 'TestTable':

```kusto
.drop extents from TestTable
```
 
**Return-Ausgabe**

|Ausgabeparameter |type |BESCHREIBUNG 
|---|---|---
|ExtentId |String |ExtentId, die als Ergebnis des Befehls gelöscht wurde 
|TableName |String |Tabellenname, wo die Ausdehnung gehörte  
|CreatedOn |Datetime |Zeitstempel, der Informationen darüber enthält, wann die Ausdehnung ursprünglich erstellt wurde 
 
**Beispielausgabe** 

|Extent-ID |Tabellenname |Erstellt am 
|---|---|---
|43c6e03f-1713-4ca7-a52a-5db8a4e8b87d |TestTable |2015-01-12 12:48:49.4298178 

## <a name="replace-extents"></a>.ersetzen Ausdehnungen

**Syntax**

`.replace`[`async` `extents` `in` `table` ] *DestinationTableName-Abfrage* `<| 
{` *für Ausdehnungen,*`},{`die aus der Tabellenabfrage für*Erweiterungen gelöscht werden sollen,* die in eine Tabelle verschoben werden sollen`}`

Dieser Befehl wird im Kontext einer bestimmten Datenbank ausgeführt, verschiebt die angegebenen Ausdehnungen aus den Quelltabellen in die Zieltabelle und löscht die angegebenen Ausdehnungen aus der Zieltabelle.
Alle Ablage- und Verschiebungsvorgänge werden in einer einzigen Transaktion ausgeführt.

Erfordert [Die Berechtigung "Tabellenadministrator"](../management/access-control/role-based-authorization.md) für die Quell- und Zieltabellen.

* `async`(optional) gibt an, ob der Befehl asynchron ausgeführt wird (in diesem Fall wird eine Operation ID (Guid) zurückgegeben, und der Status des Vorgangs kann mit dem Befehl [.show operations](operations.md#show-operations) überwacht werden).
    * Falls diese Option verwendet wird, können die Ergebnisse einer erfolgreichen Ausführung mit dem Befehl [.show operation details](operations.md#show-operation-details) abgerufen werden).

Angeben, welche Ausdehnungen gelöscht oder verschoben werden sollen, wird durch Bereitstellen von 2 Abfragen ausgeführt.
- *Abfrage für Ausdehnungen,* die aus der Tabelle gelöscht werden sollen - die Ergebnisse dieser Abfrage geben die Ausdehnungs-IDs an  
die aus der Zieltabelle gelöscht werden sollten.
- *Abfrage für Erweiterungen,* die in die Tabelle verschoben werden sollen – die Ergebnisse dieser Abfrage geben die Ausdehnungs-IDs in der Quelltabelle(n) an, die in die Zieltabelle verschoben werden sollen.

Beide Abfragen sollten ein Recordset mit einer Spalte namens "ExtentId" zurückgeben.

**Einschränkungen**
- Sowohl Quell- als auch Zieltabellen müssen sich in der Kontextdatenbank befinden. 
- Es wird erwartet, dass alle aus der Abfrage angegebenen Ausdehnungen *für Ausdehnungen, die aus der Tabelle gelöscht werden sollen,* zur Zieltabelle gehören.
- Es wird erwartet, dass alle Spalten in den Quelltabellen in der Zieltabelle mit demselben Namen und Datentyp vorhanden sind.

**Return-Ausgabe** (für Sync-Ausführung)

Ausgabeparameter |type |BESCHREIBUNG 
---|---|---
OriginalExtentId |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für die ursprüngliche Ausdehnung in der Quelltabelle, die in die Zieltabelle verschoben wurde, oder - die Ausdehnung in der Zieltabelle, die gelöscht wurde.
ResultExtentId |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für die Ergebnisausdehnung, die von der Quelltabelle in die Zieltabelle verschoben wurde, oder - leer, falls die Ausdehnung aus der Zieltabelle gelöscht wurde. Nach einem Fehler - "Fehlgeschlagen".
Details |Zeichenfolge |Schließt die Fehlerdetails ein, falls der Vorgang fehlschlägt.

**Beispiele**

Verschiebt alle Ausdehnungen`MyTable1`von `MyTable2`2 `MyOtherTable`spezifischen Tabellen ( , `MyOtherTable` ) `drop-by:MyTag`in die Tabelle und löscht alle Ausdehnungen mit dem Tag:

```kusto
.replace extents in table MyOtherTable <|
    {.show table MyOtherTable extents where tags has 'drop-by:MyTag'},
    {.show tables (MyTable1,MyTable2) extents}
```

**Beispielausgabe** 

|OriginalExtentId |ResultExtentId |Details
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df| 

*HINWEIS*: 
> [!NOTE]
> Der Befehl schlägt fehl, wenn Ausdehnungen, die von den Ausdehnungen zurückgegeben werden, die *aus der Tabellenabfrage gelöscht werden sollen,* in der Zieltabelle nicht vorhanden sind. Dies kann passieren, wenn die Ausdehnungen zusammengeführt wurden, bevor der Befehl replace ausgeführt wurde. Um sicherzustellen, dass der Befehl bei fehlenden Ausdehnungen fehlschlägt, überprüfen Sie, ob die Abfrage die erwarteten ExtentIds zurückgibt. Beispiel #1 unten schlägt fehl, wenn die zu löschende Ausdehnung in der Tabelle MyOtherTable nicht vorhanden ist. Beispiel #2 wird jedoch erfolgreich sein, obwohl die Zufallausdehnung nicht vorhanden ist, da die zu löschende Abfrage keine Ausdehnungs-IDs zurückgegeben hat. 

Beispiel 1: 

```kusto
.replace extents in table MyOtherTable <|
     { datatable(ExtentId:guid)[ "2cca5844-8f0d-454e-bdad-299e978be5df"] }, { .show table MyTable1 extents }
```

Beispiel 2:

```kusto
.replace extents in table MyOtherTable  <|
     { .show table MyOtherTable extents | where ExtentId == guid(2cca5844-8f0d-454e-bdad-299e978be5df) }, { .show table MyTable1 extents }
```



## <a name="drop-extent-tags"></a>.drop-Ausdehnungs-Tags

**Syntax**

`.drop`[`async` `extent` `tags` `from` ] `table` *Tabellenname* `(`'`,`*Tag1*'[ '*Tag2*'`,`... `,`'*TagN*']`)`

`.drop`[`async` `extent` `tags`  <| ] *Abfrage*

* `async`(optional) gibt an, ob der Befehl asynchron ausgeführt wird (in diesem Fall wird eine Operation ID (Guid) zurückgegeben, und der Status des Vorgangs kann mit dem Befehl [.show operations](operations.md#show-operations) überwacht werden).
    * Falls diese Option verwendet wird, können die Ergebnisse einer erfolgreichen Ausführung mit dem Befehl [.show operation details](operations.md#show-operation-details) abgerufen werden).

Der Befehl wird im Kontext einer bestimmten Datenbank ausgeführt und löscht die [bereitgestellten Ausdehnungs-Tags](extents-overview.md#extent-tagging) aus einer beliebigen Ausdehnung in der bereitgestellten Datenbank und Tabelle, die eines der Tags enthält.  

Es gibt zwei Möglichkeiten, anzugeben, welche Tags aus welchen Ausdehnungen entfernt werden sollen:

1. Durch explizite Angabe der Tags, die aus allen Ausdehnungen in der angegebenen Tabelle entfernt werden sollen.
2. Durch Bereitstellen einer Abfrage, deren Ergebnisse die Ausdehnungs-IDs in der Tabelle und für jede Ausdehnung angeben - die Tags, die entfernt werden sollen.

**Einschränkungen**
- Alle Ausdehnungen müssen sich in der Kontextdatenbank befinden und zur gleichen Tabelle gehören. 

**Angeben von Ausdehnungen mit einer Abfrage**

Erfordert [Die Berechtigung "Tabellenadministrator"](../management/access-control/role-based-authorization.md) für alle beteiligten Quell- und Zieltabellen.

```kusto 
.drop extent tags <| ...query... 
```

Die Ausdehnungen und die absendenden Tags werden mithilfe einer Kusto-Abfrage angegeben, die ein Recordset mit einer Spalte namens "ExtentId" und einer Spalte namens "Tags" zurückgibt. 

*HINWEIS*: Wenn Sie die [Kusto .NET-Clientbibliothek](../api/netfx/about-kusto-data.md)verwenden, können Sie die folgenden Methoden verwenden, um den gewünschten Befehl zu generieren:
- `CslCommandGenerator.GenerateExtentTagsDropByRegexCommand(string tableName, string regex)`
- `CslCommandGenerator.GenerateExtentTagsDropBySubstringCommand(string tableName, string substring)`

**Return-Ausgabe**

Ausgabeparameter |type |BESCHREIBUNG 
---|---|---
OriginalExtentId |Zeichenfolge |Eine eindeutige Bezeichnerin (GUID) für die ursprüngliche Ausdehnung, deren Tags geändert wurden (und als Teil des Vorgangs gelöscht werden) 
ResultExtentId |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für die Ergebnisausdehnung, die Tags geändert hat (und als Teil des Vorgangs erstellt und hinzugefügt wird). Nach einem Fehler - "Fehlgeschlagen".
ResultExtentTags |Zeichenfolge |Die Auflistung von Tags, mit denen die Ergebnisausdehnung markiert ist (falls vorhanden), oder "null", falls der Vorgang fehlschlägt.
Details |Zeichenfolge |Schließt die Fehlerdetails ein, falls der Vorgang fehlschlägt.

**Beispiele**

Löscht `drop-by:Partition000` das Tag aus `MyOtherTable` einer beliebigen Ausdehnung in der Tabelle, die mit ihm markiert ist.

```kusto
.drop extent tags from table MyOtherTable ('drop-by:Partition000')
```

Löscht die `drop-by:20160810104500` `a random tag`Tags , `drop-by:20160810` , und/oder `My Table` aus einer beliebigen Ausdehnung in der Tabelle, die mit einem der beiden Tags markiert ist.

```kusto
.drop extent tags from table [My Table] ('drop-by:20160810104500','a random tag','drop-by:20160810')
```

Löscht `drop-by` alle Tags aus `MyTable`Ausdehnungen in der Tabelle .

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n') 
  | mv-expand Tags to typeof(string)
  | where Tags startswith 'drop-by'
```

Löscht alle Tags, `drop-by:StreamCreationTime_20160915(\d{6})` die regex `MyTable`entsprechen, aus Ausdehnungen in der Tabelle .

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n')
  | mv-expand Tags to typeof(string)
  | where Tags matches regex @"drop-by:StreamCreationTime_20160915(\d{6})"
```

**Beispielausgabe** 

|OriginalExtentId |ResultExtentId | ResultExtentTags | Details
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | Partition001 |
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | |
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f | Partition001 Partition002 |
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df | |

## <a name="alter-extent-tags"></a>.alter Ausdehnungs-Tags

**Syntax**

`.alter`[`async` `extent` `tags` `(`] '*Tag1*`,`'[`,`'*Tag2*' ... `,`'*TagN*`)` <| *']-Abfrage*

Der Befehl wird im Kontext einer bestimmten Datenbank ausgeführt und ändert die [Ausdehnungs-Tag(s)](extents-overview.md#extent-tagging) aller von der angegebenen Abfrage zurückgegebenen Ausdehnungen in den bereitgestellten Satz von Tags.

Die Zu ändernden Ausdehnungen und Tags werden mithilfe einer Kusto-Abfrage angegeben, die ein Recordset mit einer Spalte namens "ExtentId" zurückgibt.

* `async`(optional) gibt an, ob der Befehl asynchron ausgeführt wird (in diesem Fall wird eine Operation ID (Guid) zurückgegeben, und der Status des Vorgangs kann mit dem Befehl [.show operations](operations.md#show-operations) überwacht werden).
    * Falls diese Option verwendet wird, können die Ergebnisse einer erfolgreichen Ausführung mit dem Befehl [.show operation details](operations.md#show-operation-details) abgerufen werden).

Erfordert [die Berechtigung "Tabellenadministrator"](../management/access-control/role-based-authorization.md) für alle beteiligten Tabellen.

**Einschränkungen**
- Alle Ausdehnungen müssen sich in der Kontextdatenbank befinden und zur gleichen Tabelle gehören. 

**Return-Ausgabe**

Ausgabeparameter |type |BESCHREIBUNG 
---|---|---
OriginalExtentId |Zeichenfolge |Eine eindeutige Bezeichnerin (GUID) für die ursprüngliche Ausdehnung, deren Tags geändert wurden (und als Teil des Vorgangs gelöscht werden) 
ResultExtentId |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für die Ergebnisausdehnung, die Tags geändert hat (und als Teil des Vorgangs erstellt und hinzugefügt wird). Nach einem Fehler - "Fehlgeschlagen".
ResultExtentTags |Zeichenfolge |Die Auflistung von Tags, mit denen die Ergebnisausdehnung markiert ist, oder "null", falls der Vorgang fehlschlägt.
Details |Zeichenfolge |Schließt die Fehlerdetails ein, falls der Vorgang fehlschlägt.

**Beispiele**

Ändert Tags aller Ausdehnungen `MyTable` `MyTag`in der Tabelle in .

```kusto
.alter extent tags ('MyTag') <| .show table MyTable extents
```

Ändert Tags aller Ausdehnungen `MyTable`in Tabelle `drop-by:MyTag` `drop-by:MyNewTag` , `MyOtherNewTag`mit dem mit und markiert.

```kusto
.alter extent tags ('drop-by:MyNewTag','MyOtherNewTag') <| .show table MyTable extents where tags has 'drop-by:MyTag'
```

**Beispielausgabe** 

|OriginalExtentId |ResultExtentId | ResultExtentTags | Details
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | Drop-by:MyNewTag MyOtherNewTag| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | Drop-by:MyNewTag MyOtherNewTag| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f | Drop-by:MyNewTag MyOtherNewTag| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df | Drop-by:MyNewTag MyOtherNewTag| 

