---
title: '. Move-Blöcke: Azure Daten-Explorer'
description: Dieser Artikel beschreibt den Befehl Move Blöcke in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: 994f7077b1583317320cc561b2d5a5ae1cbe829f
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060632"
---
# <a name="move-extents"></a>Verschiebungs Blöcke verschieben

Dieser Befehl wird im Kontext einer bestimmten Datenbank ausgeführt. Die angegebenen Blöcke werden aus der Quell Tabelle in die Ziel Tabelle verschoben.

Der-Befehl erfordert die [Table admin-Berechtigung](../management/access-control/role-based-authorization.md) für die Quell-und Ziel Tabellen.

> [!NOTE]
> Datenshards werden in Kusto als **Blöcke** bezeichnet, und alle Befehle verwenden "Block" oder "Blöcke" als Synonym.
> Weitere Informationen zu Blöcken finden Sie unter [Übersicht über Blöcke (Daten-Shards)](extents-overview.md).

## <a name="syntax"></a>Syntax

`.move`[ `async` ] `extents` `all` `from` `table` *SourceTableName* `to` `table` *DestinationTableName*

`.move`[ `async` ] `extents` `(` *Guid1* [ `,` *GUID2* ...] `)` `from` `table` *SourceTableName* `to` `table` *DestinationTableName* 

`.move`[ `async` ] `extents` `to` `table` *DestinationTableName*-  <|  *Abfrage*

`async`(optional). Führen Sie den Befehl asynchron aus. 
   * Eine Vorgangs-ID (GUID) wird zurückgegeben.
   * Der Status des Vorgangs kann überwacht werden. Verwenden Sie den Befehl [. Show Operations](operations.md#show-operations) .
   * Die Ergebnisse einer erfolgreichen Ausführung können abgerufen werden. Verwenden Sie den Befehl [Vorgangs Details anzeigen](operations.md#show-operation-details) .

Es gibt drei Möglichkeiten, um anzugeben, welche Blöcke verschoben werden sollen:
* Verschiebt alle Blöcke einer bestimmten Tabelle.
* Geben Sie explizit die Block-IDs in der Quell Tabelle an.
* Stellen Sie eine Abfrage bereit, deren Ergebnisse den Block-IDs in den Quell Tabellen angeben.

## <a name="restrictions"></a>Beschränkungen

* Sowohl die Quell-als auch die Ziel Tabelle müssen sich in der Kontext Datenbank befinden.
* Es wird erwartet, dass alle Spalten in der Quell Tabelle in der Ziel Tabelle mit demselben Namen und demselben Datentyp vorhanden sind.

## <a name="specify-extents-with-a-query"></a>Angeben von Blöcken mit einer Abfrage

```kusto
.move extents to table TableName <| ...query...
```

Die Blöcke werden mithilfe einer Kusto-Abfrage angegeben, die ein Recordset mit einer Spalte mit dem Namen " *extentid*" zurückgibt.

## <a name="return-output-for-sync-execution"></a>Rückgabe Ausgabe (für die Synchronisierungs Ausführung)

Output-Parameter |Typ |Beschreibung
---|---|---
Originalextentid |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für den ursprünglichen Block in der Quell Tabelle, der in die Ziel Tabelle verschoben wurde.
Resultextentid |Zeichenfolge |Ein eindeutiger Bezeichner (GUID) für den Ergebnis Block, der aus der Quell Tabelle in die Ziel Tabelle verschoben wurde. Bei Fehler: "failed".
Details |Zeichenfolge |Schließt die Fehlerdetails ein, falls der Vorgang fehlschlägt.

## <a name="examples"></a>Beispiele

### <a name="move-all-extents"></a>Alle Blöcke verschieben 

Verschieben Sie alle Blöcke in der Tabelle in `MyTable` die Tabelle `MyOtherTable` :

```kusto
.move extents all from table MyTable to table MyOtherTable
```

### <a name="move-two-specific-extents"></a>Verschieben zweier spezifischer Blöcke 

Verschieben Sie zwei spezifische Blöcke (nach Ihren Block-IDs) von der Tabelle `MyTable` in die Tabelle `MyOtherTable` :

```kusto
.move extents (AE6CD250-BE62-4978-90F2-5CB7A10D16D7,399F9254-4751-49E3-8192-C1CA78020706) from table MyTable to table MyOtherTable
```

### <a name="move-all-extents-from-specific-tables"></a>Alle Blöcke aus bestimmten Tabellen verschieben 

Verschieben Sie alle Blöcke aus bestimmten tavles ( `MyTable1` , `MyTable2` ) in eine Tabelle `MyOtherTable` :

```kusto
.move extents to table MyOtherTable <| .show tables (MyTable1,MyTable2) extents
```

## <a name="sample-output"></a>Beispielausgabe

|Originalextentid |Resultextentid| Details
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08F 42187872f| 
|2dfdef64-62a3-4950-A130-96b5b1083b5a |0F-7F -5e28-4b09-A000-e62eb41592df| 
