---
title: . Replace Blöcke-Azure Daten-Explorer
description: Dieser Artikel beschreibt den Befehl Blöcke ersetzen in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: cd193cb370136fd7f14a8892f157a895a1d7ad50
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060666"
---
# <a name="replace-extents"></a>. Replace Blöcke

Dieser Befehl wird im Kontext einer bestimmten Datenbank ausgeführt.
Die angegebenen Blöcke werden aus den Quell Tabellen in die Ziel Tabelle verschoben und dann die angegebenen Blöcke aus der Ziel Tabelle gelöscht.
Alle Drop-und Move-Vorgänge werden in einer einzelnen Transaktion durchgeführt.

Erfordert die [Table admin-Berechtigung](../management/access-control/role-based-authorization.md) für die Quell-und Ziel Tabellen.

> [!NOTE]
> Datenshards werden in Kusto als **Blöcke** bezeichnet, und alle Befehle verwenden "Block" oder "Blöcke" als Synonym.
> Weitere Informationen zu Blöcken finden Sie unter [Übersicht über Blöcke (Daten-Shards)](extents-overview.md).

## <a name="syntax"></a>Syntax

`.replace`[ `async` ] `extents` `in` `table` *DestinationTableName* - `<| 
{` *Abfrage für Blöcke, die aus der Tabellen*Abfrage für Blöcke gelöscht werden sollen, die `},{` *in die Tabelle verschoben werden* sollen`}`

* `async`(optional): führen Sie den Befehl asynchron aus.
    * Eine Vorgangs-ID (GUID) wird zurückgegeben.
    * Der Status des Vorgangs kann überwacht werden. Verwenden Sie den Befehl [. Show Operations](operations.md#show-operations) .
    * Die Ergebnisse einer erfolgreichen Ausführung können abgerufen werden. Verwenden Sie den Befehl [Vorgangs Details anzeigen](operations.md#show-operation-details) .

Um anzugeben, welche Blöcke gelöscht oder verschoben werden sollen, verwenden Sie eine von zwei Abfragen.
* *Abfrage für Blöcke, die aus der Tabelle gelöscht werden*sollen: die Ergebnisse dieser Abfrage geben die Block-IDs an, die aus der Ziel Tabelle gelöscht werden sollen.
* *Abfrage für Blöcke, die in die Tabelle verschoben werden*sollen: die Ergebnisse dieser Abfrage geben die Block-IDs in den Quell Tabellen an, die in die Ziel Tabelle verschoben werden sollen.

Beide Abfragen sollten ein Recordset mit einer Spalte mit dem Namen "extentid" zurückgeben.

## <a name="restrictions"></a>Beschränkungen

* Sowohl die Quell-als auch die Ziel Tabelle müssen sich in der Kontext Datenbank befinden.
* Alle Blöcke, die von der *Abfrage für Blöcke angegeben werden, die aus der Tabelle gelöscht werden* sollen, werden in der Ziel Tabelle erwartet.
* Es wird erwartet, dass alle Spalten in den Quell Tabellen in der Ziel Tabelle mit demselben Namen und demselben Datentyp vorhanden sind.

## <a name="return-output-for-sync-execution"></a>Rückgabe Ausgabe (für die Synchronisierungs Ausführung)

Output-Parameter |Typ |Beschreibung
---|---|---
Originalextentid |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für den ursprünglichen Block in der Quell Tabelle, der in die Ziel Tabelle verschoben wurde, oder den Block in der Ziel Tabelle, der gelöscht wurde.
Resultextentid |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für den Ergebnis Block, der aus der Quell Tabelle in die Ziel Tabelle verschoben wurde. Leer, wenn der Wertebereich aus der Ziel Tabelle gelöscht wurde. Bei Fehler: "failed".
Details |Zeichenfolge |Schließt die Fehlerdetails ein, wenn der Vorgang fehlschlägt.

> [!NOTE]
> Der Befehl schlägt fehl, wenn die Blöcke, die von den Blöcken zurückgegeben *werden, die aus der Tabellen Abfrage gelöscht werden,* in der Ziel Tabelle nicht vorhanden sind. Dies kann vorkommen, wenn die Blöcke zusammengeführt wurden, bevor der Replace-Befehl ausgeführt wurde.
> Um sicherzustellen, dass der Befehl für fehlende Blöcke fehlschlägt, überprüfen Sie, ob die Abfrage die erwarteten extentids zurückgibt. Der folgende Beispiel #1 schlägt fehl, wenn der zu löschende Block nicht in der Tabelle " *myothertable*" vorhanden ist. Beispiel #2 ist jedoch auch dann erfolgreich, wenn der zu löschte Block nicht vorhanden ist, da die abzurufte Abfrage keine Block-IDs zurückgegeben hat.

## <a name="examples"></a>Beispiele

### <a name="move-all-extents-from-two-tables"></a>Alle Blöcke aus zwei Tabellen verschieben 

Verschieben Sie alle Blöcke aus zwei bestimmten Tabellen ( `MyTable1` , `MyTable2` ) in eine Tabelle `MyOtherTable` , und löschen Sie alle Blöcke, die `MyOtherTable` mit markiert sind mit `drop-by:MyTag` :

```kusto
.replace extents in table MyOtherTable <|
    {
        .show table MyOtherTable extents where tags has 'drop-by:MyTag'
    },
    {
        .show tables (MyTable1,MyTable2) extents
    }
```

#### <a name="sample-output"></a>Beispielausgabe

|Originalextentid |Resultextentid |Details
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08F 42187872f| 
|2dfdef64-62a3-4950-A130-96b5b1083b5a |0F-7F -5e28-4b09-A000-e62eb41592df| 

### <a name="move-all-extents-from-one-table-to-another-drop-specific-extent"></a>Alle Blöcke von einer Tabelle in eine andere verschieben, bestimmte Wertebereiche löschen

Verschieben Sie alle Blöcke von einem bestimmten tavle ( `MyTable1` ) in eine Tabelle `MyOtherTable` , und löschen Sie einen bestimmten Block in `MyOtherTable` mit der ID:

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

### <a name="implement-an-idempotent-logic"></a>Implementieren einer idempotenten Logik

Implementieren Sie eine idempotente Logik, sodass Kusto Blöcke nur dann aus der Tabelle löscht, `t_dest` Wenn es Blöcke gibt, die von der Tabelle in die Tabelle verschoben werden können `t_source` `t_dest` :

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
