---
title: . Drop Blöcke-Azure Daten-Explorer
description: Dieser Artikel beschreibt den Drop Blöcke-Befehl in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: dfa462ca82cd5e94adff77b3893b3b02d60c6cdc
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060637"
---
# <a name="drop-extents"></a>. Drop-Blöcke

Löscht Blöcke aus einer angegebenen Datenbank oder Tabelle.

Dieser Befehl weist mehrere Varianten auf: in einem der Blöcke werden die Blöcke, die gelöscht werden sollen, durch eine Kusto-Abfrage angegeben. In den anderen Varianten werden Blöcke mithilfe einer unten beschriebenen Mini Sprache angegeben.

Erfordert die [Table admin-Berechtigung](../management/access-control/role-based-authorization.md) für jede Tabelle, die die von der bereitgestellten Abfrage zurückgegebenen Blöcke enthält.

> [!NOTE]
> Datenshards werden in Kusto als **Blöcke** bezeichnet, und alle Befehle verwenden "Block" oder "Blöcke" als Synonym.
> Weitere Informationen zu Blöcken finden Sie unter [Übersicht über Blöcke (Daten-Shards)](extents-overview.md).

## <a name="drop-extents-with-a-query"></a>Löschen von Blöcken mit einer Abfrage

Löschen Sie Blöcke, die mit einer Kusto-Abfrage angegeben werden.
Ein Recordset mit einer Spalte mit dem Namen "extentid" wird zurückgegeben.

Wenn `whatif` verwendet wird, meldet Sie nur, ohne tatsächlich zu löschen.

### <a name="syntax"></a>Syntax

`.drop``extents`[ `whatif` ] <| *Abfrage*

## <a name="drop-a-specific-extent"></a>Löschen eines bestimmten Wertebereichs

Erfordert die [Table admin-Berechtigung](../management/access-control/role-based-authorization.md) , wenn der Tabellenname angegeben ist.

Erfordert eine [Datenbankadministrator Berechtigung](../management/access-control/role-based-authorization.md) , wenn kein Tabellenname angegeben ist.

### <a name="syntax"></a>Syntax

`.drop``extent` *Extentid* [ `from` *TableName*]

## <a name="drop-specific-multiple-extents"></a>Bestimmte mehrere Blöcke löschen

Erfordert die [Table admin-Berechtigung](../management/access-control/role-based-authorization.md) , wenn der Tabellenname angegeben ist.

Erfordert eine [Datenbankadministrator Berechtigung](../management/access-control/role-based-authorization.md) , wenn kein Tabellenname angegeben ist.

### <a name="syntax"></a>Syntax

`.drop``extents` `(` *ExtentId1* `,` ... *Erweitertidn* `)` [ `from` *TableName*]

## <a name="drop-extents-by-specified-properties"></a>Blöcke durch angegebene Eigenschaften löschen

Der-Befehl unterstützt den Emulations Modus, der eine Ausgabe erzeugt, als ob der Befehl ausgeführt würde, ohne ihn tatsächlich auszuführen. Verwenden Sie `.drop-pretend` anstelle von `.drop`.

Erfordert die [Table admin-Berechtigung](../management/access-control/role-based-authorization.md) , wenn der Tabellenname angegeben ist.

Erfordert eine [Datenbankadministrator Berechtigung](../management/access-control/role-based-authorization.md) , wenn kein Tabellenname angegeben ist.

### <a name="syntax"></a>Syntax

`.drop``extents`[ `older` *N* ( `days`  |  `hours` )] `from` (*TableName*  |  `all` `tables` ) [ `trim` `by` ( `extentsize`  |  `datasize` ) *N* ( `MB`  |  `GB`  |  `bytes` )] [ `limit` *limitanzahl*]

* `older`: Nur Blöcke, die älter als *N* Tage/Stunden sind, werden gelöscht.
* `trim`: Der Vorgang schneidet die Daten in der Datenbank ab, bis die Summe der Blöcke mit der erforderlichen Größe (MaxSize) übereinstimmt.
* `limit`: Der Vorgang wird auf die Werte für die erste *limitanzahl* angewendet.

## <a name="examples"></a>Beispiele

### <a name="remove-all-extents-by-time-created"></a>Alle Blöcke nach der erstellten Zeit entfernen

Entfernen Sie alle Blöcke, die vor mehr als 10 Tagen erstellt wurden, aus allen Tabellen in der Datenbank.`MyDatabase`

```kusto
.drop extents <| .show database MyDatabase extents | where CreatedOn < now() - time(10d)
```

### <a name="remove-some-extents-by-time-created"></a>Entfernen einiger Blöcke nach Zeit Erstellung

Entfernen Sie alle Blöcke in Tabellen `Table1` , `Table2` deren Erstellungszeit vor mehr als 10 Tagen lag.

```kusto
.drop extents older 10 days from tables (Table1, Table2)
```

### <a name="emulation-mode-show-which-extents-would-be-removed-by-the-command"></a>Emulations Modus: zeigt an, welche Blöcke vom Befehl entfernt werden.

>[!NOTE]
>Der Block-ID-Parameter ist für diesen Befehl nicht anwendbar.

```kusto
.drop-pretend extents older 10 days from all tables
```

### <a name="remove-all-extents-from-testtable"></a>Alle Blöcke aus "testtable" entfernen

```kusto
.drop extents from TestTable
```

## <a name="return-output"></a>Ausgabe zurückgeben

|Output-Parameter |Typ |Beschreibung 
|---|---|---
|Extentid |String |Extentid, die aufgrund des Befehls gelöscht wurde
|TableName |String |Tabellenname, wobei Block zu gehört  
|CreatedOn |Datetime |Zeitstempel, der Informationen zu dem Zeitpunkt enthält, zu dem der Block anfänglich erstellt wurde
 
## <a name="sample-output"></a>Beispielausgabe

|Block-ID |Tabellenname |Erstellt am 
|---|---|---
|43c6e03f -1713-4ca7-a52a-5db8a4e8b87d |TestTable |2015-01-12 12:48:49.4298178
