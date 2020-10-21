---
title: 'Erstellen und ändern externer SQL-Tabellen: Azure Daten-Explorer'
description: In diesem Artikel wird beschrieben, wie Sie externe SQL-Tabellen erstellen und ändern.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: d6473fd0efd588bfc7a6990dd2f57d70960b40e2
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342755"
---
# <a name="create-and-alter-external-sql-tables"></a>Erstellen und Ändern externer SQL-Tabellen

Erstellt oder ändert eine externe SQL-Tabelle in der Datenbank, in der der Befehl ausgeführt wird.  

## <a name="syntax"></a>Syntax

( `.create`  |  `.alter`  |  `.create-or-alter` ) `external` `table` *TableName* ([columnName: ColumnType],...)  
`kind` `=` `sql`  
`table``=` *Sqltablename*  
`(`*Sqlserverconnectionstring*`)`  
[ `with` `(` [ `docstring` `=` *Dokumentation*] [ `,` `folder` `=` *FolderName*], *property_name* `=` *Wert* `,` ... `)` ]

## <a name="parameters"></a>Parameter

* *TableName* -Name der externen Tabelle. Muss den Regeln für [Entitäts Namen](../query/schema-entities/entity-names.md)folgen. Eine externe Tabelle kann nicht den gleichen Namen wie eine reguläre Tabelle in derselben Datenbank aufweisen.
* *Sqltablename* : der Name der SQL-Tabelle.
* *Sqlserverconnectionstring* : die Verbindungs Zeichenfolge für den SQL-Server. Kann eine der folgenden Methoden sein: 
  * *Integrierte Aad-Authentifizierung* ( `Authentication="Active Directory Integrated"` ): der Benutzer oder die Anwendung authentifiziert sich über Aad bei Kusto, und das gleiche Token wird dann verwendet, um auf den SQL Server Netzwerk Endpunkt zuzugreifen.
  * *Benutzernamen-/Kennwort-Authentifizierung* ( `User ID=...; Password=...;` ). Wenn die externe Tabelle für den [fortlaufenden Export](data-export/continuous-data-export.md)verwendet wird, muss die Authentifizierung mithilfe dieser Methode durchgeführt werden. 

> [!WARNING]
> Verbindungs Zeichenfolgen und Abfragen, die vertrauliche Informationen enthalten, sollten verdeckt werden, damit Sie bei jeder Kusto-Ablauf Verfolgung ausgelassen werden. Weitere Informationen finden Sie unter verborgene [Zeichen folgen Literale](../query/scalar-data-types/string.md#obfuscated-string-literals).

## <a name="optional-properties"></a>Optionale Eigenschaften

| Eigenschaft            | type            | BESCHREIBUNG                          |
|---------------------|-----------------|---------------------------------------------------------------------------------------------------|
| `folder`            | `string`        | Der Ordner der Tabelle.                  |
| `docString`         | `string`        | Eine Zeichenfolge, die die Tabelle dokumentiert.      |
| `firetriggers`      | `true`/`false`  | Wenn `true` , wird das Zielsystem angewiesen, INSERT-Trigger auszulösen, die für die SQL-Tabelle definiert sind. Der Standardwert ist `false`. (Weitere Informationen finden Sie unter [Bulk Insert](/sql/t-sql/statements/bulk-insert-transact-sql) und [System. Data. SqlClient. SqlBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy)). |
| `createifnotexists` | `true`/ `false` | Wenn `true` , wird die SQL-Ziel Tabelle erstellt, wenn Sie nicht bereits vorhanden `primarykey` ist. die-Eigenschaft muss in diesem Fall angegeben werden, um die Ergebnisspalte anzugeben, die der Primärschlüssel ist. Der Standardwert ist `false`.  |
| `primarykey`        | `string`        | Wenn `createifnotexists` `true` den Wert hat, wird der resultierende Spaltenname als Primärschlüssel der SQL-Tabelle verwendet, wenn er mit diesem Befehl erstellt wird.                  |

> [!NOTE]
> * Wenn die Tabelle vorhanden ist, `.create` tritt bei dem Befehl ein Fehler auf. Verwenden `.create-or-alter` `.alter` Sie oder, um vorhandene Tabellen zu ändern. 
> * Das Ändern des Schemas oder Formats einer externen SQL-Tabelle wird nicht unterstützt. 

Erfordert die [Datenbankbenutzer Berechtigung](../management/access-control/role-based-authorization.md) für `.create` und die [Table admin-Berechtigung](../management/access-control/role-based-authorization.md) für `.alter` . 
 
**Beispiel** 

```kusto
.create external table MySqlExternalTable (x:long, s:string) 
kind=sql
table=MySqlTable
( 
   h@'Server=tcp:myserver.database.windows.net,1433;Authentication=Active Directory Integrated;Initial Catalog=mydatabase;'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables", 
   createifnotexists = true,
   primarykey = x,
   firetriggers=true
)  
```

**Ausgabe**

| TableName   | TableType | Ordner         | DocString | Eigenschaften                            |
|-------------|-----------|----------------|-----------|---------------------------------------|
| Mysqlexternaltable | Sql       | Externaltables | Docs      | {<br>  "Targetentitykind": "sqltable" ",<br>  "Targetentityname": "mysqltable",<br>  "Targetentityconnectionstring": "Server = TCP:myserver. Database. Windows. net, 1433; Authentication = Active Directory integriert; anfangs Katalog = MyDatabase; ",<br>  "FireTriggers": true,<br>  "Kreateif NotExists": true,<br>  "PrimaryKey": "x"<br>} |

## <a name="querying-an-external-table-of-type-sql"></a>Abfragen einer externen Tabelle vom Typ SQL

Das Abfragen einer externen SQL-Tabelle wird unterstützt. Weitere Informationen finden Sie unter [Abfragen externer Tabellen](../../data-lake-query-data.md). 

> [!Note]
> Die Abfrage Implementierung der externen SQL-Tabelle führt `SELECT x, s FROM MySqlTable` die-Anweisung aus, wobei `x` und `s` externe Tabellen Spaltennamen sind. Der Rest der Abfrage wird auf der Kusto-Seite ausgeführt.

Sehen Sie sich die folgende externe Tabellen Abfrage an: 

```kusto
external_table('MySqlExternalTable') | count
```

Kusto führt eine `SELECT x, s FROM MySqlTable` Abfrage für die SQL-Datenbank aus, gefolgt von einer Anzahl auf Kusto-Seite. In solchen Fällen wird erwartet, dass die Leistung besser ist, wenn Sie in T-SQL direkt ( `SELECT COUNT(1) FROM MySqlTable` ) geschrieben und mit dem [sql_request-Plug](../query/sqlrequestplugin.md)-in ausgeführt wird, anstatt die externe Tabellen Funktion zu verwenden. Ebenso werden Filter nicht an die SQL-Abfrage übermittelt.  

Verwenden Sie die externe Tabelle, um die SQL-Tabelle abzufragen, wenn die Abfrage das Lesen der gesamten Tabelle (oder relevanter Spalten) für die weitere Ausführung auf Kusto-Seite erfordert. Wenn eine SQL-Abfrage in T-SQL optimiert werden kann, verwenden Sie das [sql_request-Plug](../query/sqlrequestplugin.md)-in.

## <a name="next-steps"></a>Nächste Schritte

* [Externe Tabelle (allgemeine Steuerungsbefehle)](./external-table-commands.md)
* [Erstellen und Ändern externer Tabellen in Azure Storage oder Azure Data Lake](external-tables-azurestorage-azuredatalake.md)