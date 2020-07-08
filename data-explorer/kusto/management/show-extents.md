---
title: '. Anzeigen von Blöcken: Azure-Daten-Explorer'
description: In diesem Artikel wird der Befehl zum Anzeigen von Blöcken in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: c0e2ffa76becc747b03b907dfe8a356e4c1a49a3
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060629"
---
# <a name="show-extents"></a>. Anzeigen von Blöcken

Zeigt Blöcke aus einer angegebenen Datenbank oder Tabelle an.

> [!NOTE]
> Datenshards werden in Kusto als **Blöcke** bezeichnet, und alle Befehle verwenden "Block" oder "Blöcke" als Synonym.
> Weitere Informationen zu Blöcken finden Sie unter [Übersicht über Blöcke (Daten-Shards)](extents-overview.md).

## <a name="cluster-level"></a>Cluster Ebene

`.show` `cluster` `extents` [`hot`]

Zeigt Informationen zu Blöcken (datenshards) an, die im Cluster vorhanden sind.
Wenn `hot` angegeben wird, werden nur Blöcke angezeigt, die im aktiven Cache erwartet werden.

## <a name="database-level"></a>Datenbankebene

`.show``database` *DatabaseName* `extents` [ `(` *ExtentId1* `,` ... `,` *Extentidn* `)` ] [ `hot` ] [ `where` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag1* [ `and` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag2*...]]

Zeigt Informationen zu Blöcken (datenshards) an, die in der angegebenen Datenbank vorhanden sind.
Wenn `hot` angegeben wird, werden nur Blöcke angezeigt, die im aktiven Cache erwartet werden.

## <a name="table-level"></a>Tabellenebene

`.show``table` *TableName* `extents` [ `(` *ExtentId1* `,` ... `,` *Extentidn* `)` ] [ `hot` ] [ `where` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag1* [ `and` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag2*...]]

`.show``tables` `(` *TableName1* `,` ... `,` *TableNameN* `)` `extents`[ `(` *ExtentId1* `,` . `,` .. *Extentidn* `)` ] [ `hot` ] [ `where` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag1* [ `and` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag2*...]]

Zeigt Informationen zu Blöcken (datenshards) an, die in den angegebenen Tabellen vorhanden sind. Die Datenbank wird aus dem Kontext des Befehls entnommen.
Wenn `hot` angegeben wird, zeigt nur Blöcke an, die im aktiven Cache erwartet werden.

## <a name="notes"></a>Hinweise

* Die Verwendung von Befehlen auf Datenbank-oder Tabellenebene ist wesentlich schneller als das Filtern (hinzufügen `| where DatabaseName == '...' and TableName == '...'` ) der Ergebnisse eines Befehls auf Cluster Ebene.
* Wenn die optionale Liste der Block-IDs angegeben wird, ist das zurückgegebene DataSet nur auf diese Blöcke beschränkt.
    * Diese Methode ist viel schneller als das Filtern (hinzufügen `| where ExtentId in(...)` zu) der Ergebnisse von "Bare"-Befehlen.
* Wenn `tags` Filter angegeben werden:
    * Die zurückgegebene Liste ist auf die Blöcke beschränkt, deren Tags-Auflistung *alle* bereitgestellten Tagfilter befolgt.
    * Diese Methode ist viel schneller als das Filtern (hinzufügen `| where Tags has '...' and Tags contains '...'` zu) der Ergebnisse von "Bare"-Befehlen.
    * `has`Filter sind Gleichheits Filter. Blöcke, die nicht mit einem der angegebenen Tags gekennzeichnet sind, werden herausgefiltert.
    * `!has`Filter sind Gleichheits negative Filter. Blöcke, die mit einem der angegebenen Tags gekennzeichnet sind, werden herausgefiltert.
    * `contains`bei Filtern wird die Groß-/Kleinschreibung nicht beachtet. Blöcke, die nicht über die angegebenen Zeichen folgen als Teil Zeichenfolge ihrer Tags verfügen, werden herausgefiltert.
    * `!contains`bei Filtern wird die Groß-/Kleinschreibung nicht beachtet, negative Filter. Blöcke, die die angegebenen Zeichen folgen als Teil Zeichenfolge ihrer Tags aufweisen, werden herausgefiltert.
  
## <a name="output-parameters"></a>Ausgabeparameter

|Output-Parameter |Typ |Beschreibung |
|---|---|---|
|Extentid |Guid |ID des Wertebereichs 
|DatabaseName |String |Datenbank, zu der der Block gehört
|TableName |String |Tabelle, zu der die Blöcke gehören
|Maxkreatedon |Datetime |Datum und Uhrzeit, zu der der Block erstellt wurde. Bei einem zusammengeführten Block das Maximum der Erstellungs Zeiten zwischen den Quell Blöcken
|OriginalSize |Double |Ursprüngliche Größe in Byte der Blockdaten
|Extentsize |Double |Größe des Wertebereichs im Arbeitsspeicher (komprimiert + Index)
|CompressedSize |Double |Komprimierte Größe der Blockdaten im Arbeitsspeicher
|IndexSize |Double |Index Größe der Blockdaten
|Blöcke |Long |Anzahl der Datenblöcke im Block
|Segmente |Long |Anzahl der Daten Segmente im Block
|Assigneddatanodes |String | Veraltet (leere Zeichenfolge)
|Loadeddatanodes |String |Veraltet (leere Zeichenfolge)
|Extentcontainerid |String | ID des Block Blocks, in dem sich der Block befindet
|RowCount |Long |Anzahl der Zeilen im Wertebereich
|Minkreatedon |Datetime |Datum und Uhrzeit, zu der der Block erstellt wurde. Bei einem zusammengeführten Block die Mindestanzahl der Erstellungs Zeiten zwischen den Quell Blöcken
|Tags|String|Tags, sofern vorhanden, für den Block definiert
 
## <a name="examples"></a>Beispiele

### <a name="tagged-extent"></a>Markierter Wertebereich

`E`Der Block in der Tabelle `T` wird mit den Tags `aaa` , und markiert `BBB` `ccc` .

* Diese Abfrage gibt Folgendes zurück `E` :
    
   ```kusto
    .show table T extents where tags has 'aaa' and tags contains 'bb'
   ```
   
* Diese Abfrage wird nicht zurückgegeben, `E` da Sie nicht mit gekennzeichnet ist `aa` :
    
   ```kusto
    .show table T extents where tags has 'aa' and tags contains 'bb'
   ```
    
* Diese Abfrage gibt Folgendes zurück `E` :
    
   ```kusto
    .show table T extents where tags contains 'aaa' and tags contains 'bb' 
   ```

### <a name="show-volume-of-extents-created"></a>Menge der erstellten Blöcke anzeigen

Anzeigen des Umfangs der Blöcke, die pro Stunde in einer bestimmten Datenbank erstellt werden

```kusto 
.show database MyDatabase extents | summarize count(ExtentId) by MaxCreatedOn bin=time(1h) | render timechart  
```

### <a name="show-volume-of-data-arriving-by-table-per-hour"></a>Anzeigen der Menge der Daten, die nach Tabelle pro Stunde eintreffen

```kusto 
.show database MyDatabase extents  
| summarize sum(OriginalSize) by TableName, MaxCreatedOn bin=time(1h)  
| render timechart
```

### <a name="show-data-size-distribution-by-table"></a>Verteilung der Datengröße nach Tabelle anzeigen

```kusto 
.show database MyDatabase extents | summarize sum(OriginalSize) by TableName
```

### <a name="show-all-extents-in-the-database-named-gamesdb"></a>Alle Blöcke in der Datenbank mit dem Namen "gamesdb" anzeigen

```kusto 
.show database GamesDB extents
```

### <a name="show-all-extents-in-the-table-named-games"></a>Alle Blöcke in der Tabelle mit dem Namen "Games" anzeigen

```kusto 
.show table Games extents
```

### <a name="show-all-extents-in-specific-tables"></a>Alle Blöcke in bestimmten Tabellen anzeigen

Alle Blöcke in den Tabellen mit den Namen "TaggingGames1" und "TaggingGames2" anzeigen, die mit "Tag1" und "Tag2" gekennzeichnet sind

```kusto 
.show tables (TaggingGames1,TaggingGames2) extents where tags has 'tag1' and tags has 'tag2'
```
