---
title: Exportieren von Daten nach SQL - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird beschrieben, wie Daten in Azure Data Explorer in SQL exportiert werden.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7deeb3868800f00a828eb09cc605e4d7e5227032
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521623"
---
# <a name="export-data-to-sql"></a>Exportieren von Daten in SQL

Mit dem Exportieren von Daten nach SQL können Sie eine Abfrage ausführen und deren Ergebnisse an eine Tabelle in einer SQL-Datenbank senden, z. B. an eine SQL-Datenbank, die vom Azure SQL-Datenbankdienst gehostet wird.

**Syntax**

`.export`[`async` `to` `sql` ] *SqlTableName* *SqlConnectionString* `with` `(`[ *PropertyName* `=` *PropertyValue*`,`... `)`] `<|` *Abfrage*

Hierbei gilt:
* *async*: Der Befehl wird im asynchronen Modus ausgeführt (optional).
* *SqlTableName* SQL-Datenbanktabellenname, in den die Daten eingefügt werden.
  Zum Schutz vor Injektionsangriffen ist dieser Name eingeschränkt.
* *SqlConnectionString* ist `string` ein Literal, das dem `ADO.NET` Verbindungszeichenfolgenformat folgt und den SQL-Endpunkt und die Datenbank beschreibt, mit der Sie eine Verbindung herstellen. Aus Sicherheitsgründen ist die Verbindungszeichenfolge eingeschränkt.
* *PropertyName*, *PropertyValue* sind Paare eines Namens (Bezeichners) und eines Werts (Zeichenfolgenliteral).

Eigenschaften:

|Name               |Werte           |Beschreibung|
|-------------------|-----------------|-----------|
|`firetriggers`     |`true` oder `false`|Wenn `true`, weist das Zielsystem an, INSERT-Trigger auszulösen, die in der SQL-Tabelle definiert sind. Der Standardwert ist `false`. (Weitere Informationen finden Sie unter [BULK INSERT](https://msdn.microsoft.com/library/ms188365.aspx) und [System.Data.SqlClient.SqlBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy(v=vs.110).aspx))|
|`createifnotexists`|`true` oder `false`|Wenn `true`, wird die SQL-Zieltabelle erstellt, wenn sie noch nicht vorhanden ist. Die `primarykey` Eigenschaft muss in diesem Fall angegeben werden, um die Ergebnisspalte anzugeben, die der Primärschlüssel ist. Der Standardwert ist `false`.|
|`primarykey`       |                 |Wenn `createifnotexists` `true`dieser Wert ist, gibt der Name der Spalte im Ergebnis an, die als Primärschlüssel der SQL-Tabelle verwendet wird, wenn sie mit diesem Befehl erstellt wird.|
|`persistDetails`   |`bool`           |Gibt an, dass der Befehl `async` seine Ergebnisse beibehalten soll (siehe Flag). Standardwerte `true` für async-Läufe, können jedoch deaktiviert werden, wenn der Aufrufer die Ergebnisse nicht benötigt). Standardmäßig `false` in synchronen Ausführungen, kann jedoch aktiviert werden. |
|`token`            |`string`         |Das AAD-Zugriffstoken, das Kusto zur Authentifizierung an den SQL-Endpunkt weiterleitet. Wenn diese Einstellung festgelegt ist, sollte die `Authentication` `User ID`SQL-Verbindungszeichenfolge keine Authentifizierungsinformationen wie enthalten, z. B. , oder `Password`.|

## <a name="limitations-and-restrictions"></a>Einschränkungen

Beim Exportieren von Daten in eine SQL-Datenbank gibt es eine Reihe von Einschränkungen:

1. Kusto ist ein Clouddienst, daher muss die Verbindungszeichenfolge auf eine Datenbank verweisen, auf die über die Cloud zugegriffen werden kann. (Insbesondere kann man nicht in eine lokale Datenbank exportieren, da sie nicht über die öffentliche Cloud zugänglich ist.)

2. Kusto unterstützt die integrierte Active Directory-Authentifizierung, wenn`aaduser=` `aadapp=`der aufrufende Prinzipal ein Azure Active Directory-Prinzipal ( oder ) ist.
   Alternativ unterstützt Kusto auch die Bereitstellung der Anmeldeinformationen für die SQL-Datenbank als Teil der Verbindungszeichenfolge. Andere Authentifizierungsmethoden werden nicht unterstützt. Die Identität, die der SQL-Datenbank angezeigt wird, geht immer vom Befehlsaufrufer aus und nicht von der Kusto-Dienstidentität selbst.

3. Wenn die Zieltabelle in der SQL-Datenbank vorhanden ist, muss sie mit dem Abfrageergebnisschema übereinstimmen. Beachten Sie, dass dies in einigen Fällen (z. B. Azure SQL-Datenbank) bedeutet, dass die Tabelle über eine Spalte verfügt, die als Identitätsspalte markiert ist.

4. Das Exportieren großer Datenmengen kann lange dauern. Es wird empfohlen, die SQL-Zieltabelle für die minimale Protokollierung während des Massenimports festzulegen.
   Siehe [SQL Server Database Engine > ... > Datenbankfeatures > Massenimport und -export von Daten](https://msdn.microsoft.com/library/ms190422.aspx).

5. Der Datenexport wird mithilfe der SQL-Massenkopie durchgeführt und bietet keine Transaktionsgarantien für die SQL-Zieldatenbank. Weitere Informationen finden Sie unter [Transaktions- und Massenkopiervorgänge.](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/transaction-and-bulk-copy-operations)

6. Der SQL-Tabellenname ist auf einen Namen beschränkt, der aus`_`Buchstaben,`.`Ziffern, Leerzeichen, Unterstrichen ( ), Punkten ( ) und Bindestrichen (`-`) besteht.

7. Die SQL-Verbindungszeichenfolge ist `Persist Security Info` wie folgt `false`eingeschränkt: ist `true`explizit `Trust Server Certificate` auf `false`festgelegt, `Encrypt` ist auf festgelegt und auf festgelegt.

8. Die Primärschlüsseleigenschaft in der Spalte kann beim Erstellen einer neuen SQL-Tabelle angegeben werden. Wenn die Spalte `string`vom Typ ist, weigert sich SQL möglicherweise, die Tabelle aufgrund anderer Einschränkungen in der Primärschlüsselspalte zu erstellen. Die Problemumgehung besteht darin, die Tabelle in SQL manuell zu erstellen, bevor die Daten exportiert werden. Der Grund für diese Einschränkung ist, dass Primärschlüsselspalten in SQL nicht von unbegrenzter Größe sein können, aber Kusto-Tabellenspalten haben keine deklarierten Größenbeschränkungen.

## <a name="azure-db-aad-integrated-authentication-documentation"></a>Azure DB AAD-Dokumentation für integrierte Authentifizierung

* [Verwenden der Azure Active Directory-Authentifizierung für die Authentifizierung mit SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)
* [Azure AD-Authentifizierungserweiterungen für Azure SQL DB- und SQL DW-Tools] (https://azure.microsoft.com/blog/azure-ad-authentication-extensions-for-azure-sql-db-and-sql-dw-tools/)

**Beispiele** 

In diesem Beispiel führt Kusto die Abfrage aus und exportiert dann `MySqlTable` den `MyDatabase` ersten Datensatz, der von der Abfrage erzeugt wird, in die Tabelle in der Datenbank auf dem Server `myserver`.

```kusto 
.export async to sql MySqlTable
    h@"Server=tcp:myserver.database.windows.net,1433;Database=MyDatabase;Authentication=Active Directory Integrated;Connection Timeout=30;"
    <| print Id="d3b68d12-cbd3-428b-807f-2c740f561989", Name="YSO4", DateOfBirth=datetime(2017-10-15)
```

In diesem Beispiel führt Kusto die Abfrage aus und exportiert dann `MySqlTable` den `MyDatabase` ersten Datensatz, der von der Abfrage erzeugt wird, in die Tabelle in der Datenbank auf dem Server `myserver`.
Wenn die Zieltabelle nicht in der Zieldatenbank vorhanden ist, wird sie erstellt.

```kusto 
.export async to sql ['dbo.MySqlTable']
    h@"Server=tcp:myserver.database.windows.net,1433;Database=MyDatabase;Authentication=Active Directory Integrated;Connection Timeout=30;"
    with (createifnotexists="true", primarykey="Id")
    <| print Message = "Hello World!", Timestamp = now(), Id=12345678
```