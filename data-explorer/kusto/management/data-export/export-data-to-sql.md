---
title: Exportieren von Daten nach SQL-Azure Daten-Explorer | Microsoft-Dokumentation
description: Dieser Artikel beschreibt das Exportieren von Daten nach SQL in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 61c65e2667356f2b2ee9e53c3dd1c210e84c72f8
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342806"
---
# <a name="export-data-to-sql"></a>Exportieren von Daten nach SQL

Durch das Exportieren von Daten nach SQL können Sie eine Abfrage ausführen und die Ergebnisse an eine Tabelle in einer SQL-Datenbank senden, z. b. eine SQL-Datenbank, die vom Azure SQL-Datenbankdienst gehostet wird.

**Syntax**

`.export`[ `async` ] `to` `sql` *Sqltablename* *sqlConnectionString* [ `with` `(` *propertyName* `=` *PropertyValue* `,` ... `)` ] `<|` *Abfrage*

Hierbei gilt:
* *Async*: der Befehl wird im asynchronen Modus ausgeführt (optional).
* *Sqltablename* Name der SQL-Datenbanktabelle, in die die Daten eingefügt werden.
  Zum Schutz vor Injection-Angriffen ist dieser Name eingeschränkt.
* *SqlConnectionString* ist ein `string` Literalzeichen, das dem `ADO.NET` Verbindungs Zeichen folgen Format folgt und den SQL-Endpunkt und die Datenbank beschreibt, mit denen Sie eine Verbindung herstellen. Aus Sicherheitsgründen wird die Verbindungs Zeichenfolge eingeschränkt.
* *PropertyName*, *PropertyValue* sind Paare eines Namens (Bezeichner) und ein Wert (Zeichenfolgenliterale).

Eigenschaften:

|Name               |Werte           |BESCHREIBUNG|
|-------------------|-----------------|-----------|
|`firetriggers`     |`true` oder `false`|Wenn `true` , wird das Zielsystem angewiesen, INSERT-Trigger auszulösen, die für die SQL-Tabelle definiert sind. Der Standardwert ist `false`. (Weitere Informationen finden Sie unter [Bulk Insert](/sql/t-sql/statements/bulk-insert-transact-sql) und [System. Data. SqlClient. SqlBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy)).|
|`createifnotexists`|`true` oder `false`|Wenn `true` , wird die SQL-Ziel Tabelle erstellt, wenn Sie nicht bereits vorhanden `primarykey` ist. die-Eigenschaft muss in diesem Fall angegeben werden, um die Ergebnisspalte anzugeben, die der Primärschlüssel ist. Der Standardwert ist `false`.|
|`primarykey`       |                 |Wenn `createifnotexists` `true` den Wert hat, wird der Name der Spalte im Ergebnis angegeben, die als Primärschlüssel der SQL-Tabelle verwendet wird, wenn Sie mit diesem Befehl erstellt wird.|
|`persistDetails`   |`bool`           |Gibt an, dass der Befehl seine Ergebnisse beibehalten soll (siehe `async` Flag). Der Standardwert ist in asynchronen Ausführungen `true` , kann aber deaktiviert werden, wenn der Aufrufer die Ergebnisse nicht benötigt). Wird standardmäßig `false` in synchronen Ausführungen verwendet, kann jedoch aktiviert werden. |
|`token`            |`string`         |Das Aad-Zugriffs Token, das Kusto zur Authentifizierung an den SQL-Endpunkt weiterleiten soll. Wenn festgelegt, sollte die SQL-Verbindungs Zeichenfolge keine Authentifizierungsinformationen wie `Authentication` , `User ID` oder enthalten `Password` .|

## <a name="limitations-and-restrictions"></a>Einschränkungen

Beim Exportieren von Daten in eine SQL-Datenbank gibt es eine Reihe von Einschränkungen:

1. Kusto ist ein clouddienst, daher muss die Verbindungs Zeichenfolge auf eine Datenbank verweisen, die über die Cloud zugänglich ist. (Vor allem kann eine nicht in eine lokale Datenbank exportiert werden, da Sie nicht über die Public Cloud zugänglich ist.)

2. Kusto unterstützt Active Directory integrierte Authentifizierung, wenn der aufrufende Prinzipal ein Azure Active Directory Prinzipal ( `aaduser=` oder `aadapp=` ) ist.
   Alternativ unterstützt Kusto auch die Bereitstellung der Anmelde Informationen für die SQL-Datenbank als Teil der Verbindungs Zeichenfolge. Andere Authentifizierungsmethoden werden nicht unterstützt. Die Identität, die für die SQL-Datenbank angezeigt wird, wird immer vom Befehls Aufrufer und nicht von der Kusto-Dienst Identität selbst ausgegeben.

3. Wenn die Ziel Tabelle in der SQL-Datenbank vorhanden ist, muss Sie dem Abfrageergebnis Schema entsprechen. Beachten Sie, dass in einigen Fällen (z. b. Azure SQL-Datenbank) Dies bedeutet, dass für die Tabelle eine Spalte als Identitäts Spalte gekennzeichnet ist.

4. Der Export großer Datenmengen kann viel Zeit in Anspruch nehmen. Es wird empfohlen, die SQL-Ziel Tabelle für die minimale Protokollierung während des Massen Imports festzulegen.
   Weitere Informationen finden Sie [unter SQL Server Datenbank-Engine >... > Daten Bank Features > Massen Import und-Export von Daten](/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import).

5. Der Datenexport erfolgt mithilfe von SQL-Massen Kopiervorgang und bietet keine Transaktions Garantien für die SQL-Zieldatenbank. Weitere Informationen finden Sie unter [Transaktionen und Massen Kopiervorgänge](/dotnet/framework/data/adonet/sql/transaction-and-bulk-copy-operations) .

6. Der Name der SQL-Tabelle ist auf einen Namen beschränkt, der aus Buchstaben, Ziffern, Leerzeichen, unterstrichen ( `_` ), Punkten ( `.` ) und Bindestrichen ( `-` ) besteht.

7. Die SQL-Verbindungs Zeichenfolge ist wie folgt eingeschränkt: `Persist Security Info` ist explizit auf festgelegt `false` , `Encrypt` ist auf festgelegt, `true` und `Trust Server Certificate` ist auf festgelegt `false` .

8. Die Primärschlüssel Eigenschaft für die Spalte kann beim Erstellen einer neuen SQL-Tabelle angegeben werden. Wenn die Spalte vom Typ ist `string` , kann SQL die Erstellung der Tabelle aufgrund anderer Einschränkungen in der Primärschlüssel Spalte ablehnen. Die Problem Umgehung besteht darin, die Tabelle vor dem Exportieren der Daten in SQL manuell zu erstellen. Der Grund für diese Einschränkung besteht darin, dass Primärschlüssel Spalten in SQL keine unbegrenzte Größe aufweisen dürfen, dass die Kusto-Tabellen Spalten jedoch keine Einschränkungen aufweisen.

## <a name="azure-db-aad-integrated-authentication-documentation"></a>Dokumentation zur integrierten Azure DB Aad-Authentifizierung

* [Verwenden der Azure Active Directory Authentifizierung für die Authentifizierung mit SQL-Datenbank](/azure/sql-database/sql-database-aad-authentication)
* [Azure AD Authentifizierungs Erweiterungen für Azure SQL-Datenbank und SQL DW-Tools] (https://azure.microsoft.com/blog/azure-ad-authentication-extensions-for-azure-sql-db-and-sql-dw-tools/)

**Beispiele** 

In diesem Beispiel führt Kusto die Abfrage aus und exportiert dann den ersten von der Abfrage erstellten Daten Satz Satz in die- `MySqlTable` Tabelle in der- `MyDatabase` Datenbank auf dem-Server `myserver` .

```kusto 
.export async to sql MySqlTable
    h@"Server=tcp:myserver.database.windows.net,1433;Database=MyDatabase;Authentication=Active Directory Integrated;Connection Timeout=30;"
    <| print Id="d3b68d12-cbd3-428b-807f-2c740f561989", Name="YSO4", DateOfBirth=datetime(2017-10-15)
```

In diesem Beispiel führt Kusto die Abfrage aus und exportiert dann den ersten von der Abfrage erstellten Daten Satz Satz in die- `MySqlTable` Tabelle in der- `MyDatabase` Datenbank auf dem-Server `myserver` .
Wenn die Ziel Tabelle in der Zieldatenbank nicht vorhanden ist, wird Sie erstellt.

```kusto 
.export async to sql ['dbo.MySqlTable']
    h@"Server=tcp:myserver.database.windows.net,1433;Database=MyDatabase;Authentication=Active Directory Integrated;Connection Timeout=30;"
    with (createifnotexists="true", primarykey="Id")
    <| print Message = "Hello World!", Timestamp = now(), Id=12345678
```