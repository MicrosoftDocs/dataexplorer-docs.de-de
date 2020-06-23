---
title: 'Allgemeine Kusto-Tabellen Steuerungsbefehle: Azure Daten-Explorer'
description: In diesem Artikel werden allgemeine Befehle der externen Tabellensteuerung beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/26/2020
ms.openlocfilehash: 8e419d471419a291b3680c4b91d3e6908b2e7f2e
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128834"
---
# <a name="external-table-general-control-commands"></a>Externe Tabelle (allgemeine Steuerungsbefehle)

Eine Übersicht über externe Tabellen finden Sie unter [externe Tabellen](../query/schema-entities/externaltables.md) . Die folgenden Befehle sind für _jede_ externe Tabelle (eines beliebigen Typs) relevant.

## <a name="show-external-tables"></a>. anzeigen externer Tabellen

* Gibt alle externen Tabellen in der Datenbank zurück (oder eine bestimmte externe Tabelle).
* Erfordert die [Datenbank-Monitor-Berechtigung](../management/access-control/role-based-authorization.md).

**Syntax:** 

`.show` `external` `tables`

`.show` `external` `table` *TableName*

**Ausgabe**

| Output-Parameter | type   | Beschreibung                                                         |
|------------------|--------|---------------------------------------------------------------------|
| TableName        | Zeichenfolge | Name der externen Tabelle                                             |
| TableType        | Zeichenfolge | Typ externer Tabelle                                              |
| Ordner           | Zeichenfolge | Tabellen Ordner                                                     |
| DocString        | Zeichenfolge | Zeichenfolge, die die Tabelle dokumentiert                                       |
| Eigenschaften       | Zeichenfolge | JSON-serialisierte Eigenschaften der Tabelle (spezifisch für den Typ der Tabelle) |


**Beispiele:**

```kusto
.show external tables
.show external table T
```

| TableName | TableType | Ordner         | DocString | Eigenschaften |
|-----------|-----------|----------------|-----------|------------|
| T         | Blob      | Externaltables | Docs      | {}         |


## <a name="show-external-table-schema"></a>. Anzeigen eines externen Tabellen Schemas

* Gibt das Schema der externen Tabelle als JSON oder CSL zurück. 
* Erfordert die [Datenbank-Monitor-Berechtigung](../management/access-control/role-based-authorization.md).

**Syntax:** 

`.show``external` `table` *TableName* `schema` `as` ( `json`  |  `csl` )

`.show` `external` `table` *TableName* `cslschema`

**Ausgabe**

| Output-Parameter | type   | Beschreibung                        |
|------------------|--------|------------------------------------|
| TableName        | Zeichenfolge | Name der externen Tabelle            |
| Schema           | Zeichenfolge | Das Tabellen Schema in einem JSON-Format. |
| DatabaseName     | Zeichenfolge | Datenbankname der Tabelle             |
| Ordner           | Zeichenfolge | Tabellen Ordner                    |
| DocString        | Zeichenfolge | Zeichenfolge, die die Tabelle dokumentiert      |

**Beispiele:**

```kusto
.show external table T schema as JSON
```

```kusto
.show external table T schema as CSL
.show external table T cslschema
```

**Ausgabe:**

*JSON*

| TableName | Schema    | DatabaseName | Ordner         | DocString |
|-----------|----------------------------------|--------------|----------------|-----------|
| T         | {"Name": "externalblob",<br>"Folder": "externaltables",<br>"DocString": "docs",<br>"Orderedcolumns": [{"Name": "x", "Type": "System. Int64", "csltype": "Long", "DocString": ""}, {"Name": "s", "Type": "System. String", "csltype": "String", "DocString": ""}]} | DB           | Externaltables | Docs      |


*CSL*

| TableName | Schema          | DatabaseName | Ordner         | DocString |
|-----------|-----------------|--------------|----------------|-----------|
| T         | x:Long, s:Zeichenfolge | DB           | Externaltables | Docs      |

## <a name="drop-external-table"></a>. externe Tabelle löschen

* Löscht eine externe Tabelle. 
* Die Definition der externen Tabelle kann nach diesem Vorgang nicht wieder hergestellt werden.
* Erfordert die [Administrator Berechtigung](../management/access-control/role-based-authorization.md)für die Datenbank.

**Syntax:**  

`.drop``external` `table` *Tabellenname* [ `ifexists` ]

**Ausgabe**

Gibt die Eigenschaften der gelöschten Tabelle zurück. Weitere Informationen finden Sie unter [. anzeigen externer Tabellen](#show-external-tables).

**Beispiele:**

```kusto
.drop external table ExternalBlob
```

| TableName | TableType | Ordner         | DocString | Schema       | Eigenschaften |
|-----------|-----------|----------------|-----------|-----------------------------------------------------|------------|
| T         | Blob      | Externaltables | Docs      | [{"Name": "x", "csltype": "Long"},<br> {"Name": "s", "csltype": "String"}] | {}         |

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen und Ändern externer Tabellen in Azure Storage oder Azure Data Lake](external-tables-azurestorage-azuredatalake.md)
* [Erstellen und Ändern externer SQL-Tabellen](external-sql-tables.md)
