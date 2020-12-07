---
title: 'mysql_request-Plug-in: Azure Daten-Explorer'
description: In diesem Artikel wird mysql_request-Plug-in in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: e20e266e6fbae55c308cf13b7601277b8b0f30b2
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320739"
---
# <a name="mysql_request-plugin-preview"></a>mysql_request-Plug-in (Vorschau)

::: zone pivot="azuredataexplorer"

Das `mysql_request` Plug-in sendet eine SQL-Abfrage an einen MySQL Server-Netzwerk Endpunkt und gibt das erste Rowset in den Ergebnissen zurück. Die Abfrage gibt möglicherweise mehr als ein Rowset zurück, aber nur das erste Rowset wird für den Rest der Kusto-Abfrage zur Verfügung gestellt.

> [!IMPORTANT]
> Das `mysql_request` Plug-in befindet sich im Vorschaumodus und ist standardmäßig deaktiviert.
> Um das Plug-in zu aktivieren, führen Sie den [ `.enable plugin mysql_request` Befehl](../management/enable-plugin.md)aus. Verwenden Sie zum Anzeigen der aktivierten Plug-Ins [ `.show plugin` Verwaltungs Befehle](../management/show-plugins.md).

## <a name="syntax"></a>Syntax

`evaluate``mysql_request` `(` *ConnectionString* `,` *sqlQuery* [ `,` *SQLPARAMETERS*]`)`

## <a name="arguments"></a>Argumente

Name | type | BESCHREIBUNG | Erforderlich/Optional |
---|---|---|---
| *ConnectionString* | `string`-Literal | Gibt die Verbindungs Zeichenfolge an, die auf den MySQL Server-Netzwerk Endpunkt zeigt. Weitere Informationen finden Sie unter [Authentifizierung](#username-and-password-authentication) und angeben des [Netzwerk Endpunkts](#specify-the-network-endpoint). | Erforderlich |
| *SqlQuery* | `string`-Literal | Gibt die Abfrage an, die für den SQL-Endpunkt ausgeführt werden soll. Muss mindestens ein Rowset zurückgeben, aber nur das erste Rowset wird für den Rest der Kusto-Abfrage zur Verfügung gestellt. | Erforderlich|
| *SQLPARAMETERS* | Konstanter Wert des Typs `dynamic` | Enthält Schlüssel-Wert-Paare, die als Parameter zusammen mit der Abfrage übergeben werden. | Optional |

## <a name="set-callout-policy"></a>Festlegen der Legenden Richtlinie

Das Plug-in stellt Aufrufe für die MySQL-Datenbank dar. Stellen Sie sicher, dass die [Legenden-Richtlinie](../management/calloutpolicy.md) des Clusters Aufrufe vom Typ `mysql` zum Ziel *mysqldburi* ermöglicht.

Im folgenden Beispiel wird gezeigt, wie die Legenden Richtlinie für MySQL DB definiert wird. Es wird empfohlen, die Legenden Richtlinie auf bestimmte Endpunkte ( `my_endpoint1` ,) zu beschränken `my_endpoint2` .

```kusto
[
  {
    "CalloutType": "mysql",
    "CalloutUriRegex": "my_endpoint1\\.mysql\\.database\\.azure\\.com",
    "CanCall": true
  },
  {
    "CalloutType": "mysql",
    "CalloutUriRegex": "my_endpoint2\\.mysql\\.database\\.azure\\.com",
    "CanCall": true
  }
]
```

Das folgende Beispiel zeigt einen Befehl für die alter Legenden-Richtlinie für `mysql` *callouttype*:

```kusto
.alter cluster policy callout @'[{"CalloutType": "mysql", "CalloutUriRegex": "\\.mysql\\.database\\.azure\\.com", "CanCall": true}]'
```

## <a name="username-and-password-authentication"></a>Authentifizierung mit Benutzername und Kennwort

Das mysql_request-Plug-in unterstützt nur die Benutzernamen-und Kenn Wort Authentifizierung beim MySQL-Server Endpunkt und ist nicht in Azure Active Directory Authentifizierung integriert 

Benutzername und Kennwort werden als Teil der Verbindungs Zeichenfolge mit den folgenden Parametern bereitgestellt:

`User ID=...; Password=...;`
    
> [!WARNING]
> Vertrauliche oder geschützte Informationen sollten aus Verbindungs Zeichenfolgen und Abfragen verdeckt werden, damit Sie bei jeder Kusto-Ablauf Verfolgung ausgelassen werden. Weitere Informationen finden Sie unter verborgene [Zeichen folgen Literale](scalar-data-types/string.md#obfuscated-string-literals).

## <a name="encryption-and-server-validation"></a>Verschlüsselung und Server Validierung

Aus Sicherheitsgründen `SslMode` wird `Required` beim Herstellen einer Verbindung mit einem MySQL Server-Netzwerk Endpunkt bedingungslos auf festgelegt. Folglich muss der SQL Server mit einem gültigen SSL/TLS-Server Zertifikat konfiguriert werden.

## <a name="specify-the-network-endpoint"></a>Netzwerk Endpunkt angeben

Geben Sie den SQL-Netzwerk Endpunkt als Teil der Verbindungs Zeichenfolge an.

**Syntax**:

`Server`Voll `=` *qualifizierter* Name [ `Port` `=` *Port*]

Hierbei gilt:

* *FQDN* ist der voll qualifizierte Domänen Name des Endpunkts.
* *Port* ist der TCP-Port des Endpunkts. Standardmäßig `3306` wird angenommen.

## <a name="examples"></a>Beispiele


### <a name="sql-query-to-azure-mysql-db"></a>SQL-Abfrage an Azure MySQL-DB

Im folgenden Beispiel wird eine SQL-Abfrage an eine Azure MySQL DB-Datenbank gesendet. Dabei werden alle Datensätze aus abgerufen `[dbo].[Table]` , und anschließend werden die Ergebnisse verarbeitet.

> [!NOTE]
> Dieses Beispiel sollte nicht als Empfehlung zum Filtern oder projizieren von Daten auf diese Weise verwendet werden. SQL-Abfragen sollten erstellt werden, um das kleinste DataSet zurückzugeben, da der Kusto-Optimierer derzeit nicht versucht, Abfragen zwischen Kusto und SQL zu optimieren.

```kusto
evaluate sql_request(
    'Server=contoso.mysql.database.azure.com; Port = 3306;'
    'Database=Fabrikam;'
    h'UID=USERNAME;'
    h'Pwd=PASSWORD;', 
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

### <a name="sql-authentication-with-username-and-password"></a>SQL-Authentifizierung mit Benutzername und Kennwort

Das folgende Beispiel ist mit dem vorherigen identisch, aber die SQL-Authentifizierung erfolgt durch Benutzername und Kennwort. Aus Gründen der Vertraulichkeit verwenden wir verborgene Zeichen folgen.

```kusto
evaluate sql_request(
   'Server=contoso.mysql.database.azure.com; Port = 3306;'
    'Database=Fabrikam;'
    h'UID=USERNAME;'
    h'Pwd=PASSWORD;', 
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

### <a name="sql-query-to-azure-sql-db-with-modifications"></a>SQL-Abfrage an Azure SQL-Datenbank mit Änderungen

Im folgenden Beispiel wird eine SQL-Abfrage an eine Azure SQL-Datenbank gesendet, von der alle Datensätze aus abgerufen `[dbo].[Table]` werden, während eine andere `datetime` Spalte angehängt wird und die Ergebnisse dann auf der Kusto-Seite verarbeitet werden.
Gibt einen SQL-Parameter ( `@param0` ) an, der in der SQL-Abfrage verwendet werden soll.

```kusto
evaluate mysql_request(
  'Server=contoso.mysql.database.azure.com; Port = 3306;'
    'Database=Fabrikam;'
    h'UID=USERNAME;'
    h'Pwd=PASSWORD;', 
  'select *, @param0 as dt from [dbo].[Table]',
  dynamic({'param0': datetime(2020-01-01 16:47:26.7423305)}))
| where Id > 0
| project Name
```

::: zone-end

::: zone pivot="azuremonitor"

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
