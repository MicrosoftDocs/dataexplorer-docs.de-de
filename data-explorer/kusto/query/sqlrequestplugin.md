---
title: 'sql_request-Plug-in: Azure Daten-Explorer'
description: In diesem Artikel wird sql_request-Plug-in in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: e412c1ec4f08af9820018f4c8dc172bd8c748a7f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350977"
---
# <a name="sql_request-plugin"></a>Plug-In „sql_request“

::: zone pivot="azuredataexplorer"

  `evaluate``sql_request` `(` *ConnectionString* `,` *sqlQuery* [ `,` *SQLPARAMETERS* [ `,` *Optionen*]]`)`

Das `sql_request` Plug-in sendet eine SQL-Abfrage an einen SQL Server Netzwerk Endpunkt und gibt das erste Rowset in den Ergebnissen zurück.

## <a name="arguments"></a>Argumente

* *ConnectionString*: ein `string` Literalwert, der die Verbindungs Zeichenfolge angibt, die auf den SQL Server Netzwerk Endpunkt zeigt. Weitere Informationen finden Sie unter [gültige Authentifizierungsmethoden](#authentication) und angeben des [Netzwerk Endpunkts](#specify-the-network-endpoint).

* *SqlQuery*: ein `string` Literalwert, der die Abfrage angibt, die für den SQL-Endpunkt ausgeführt werden soll. Muss mindestens ein Rowset zurückgeben, aber nur das erste Rowset wird für den Rest der Kusto-Abfrage zur Verfügung gestellt.

* *SQLPARAMETERS*: ein konstanter Wert des Typs `dynamic` , der Schlüssel-Wert-Paare enthält, die als Parameter zusammen mit der Abfrage übergeben werden. Optional.
  
* *Optionen*: ein konstanter Wert des Typs `dynamic` , der Erweiterte Einstellungen als Schlüssel-Wert-Paare enthält. Zurzeit `token` kann nur festgelegt werden, um einen vom Aufrufer bereitgestellten Azure AD Zugriffs Token zu übergeben, der zur Authentifizierung an den SQL-Endpunkt weitergeleitet wird. Optional.

## <a name="examples"></a>Beispiele

Im folgenden Beispiel wird eine SQL-Abfrage an eine Azure SQL DB-Datenbank gesendet. Dabei werden alle Datensätze aus abgerufen `[dbo].[Table]` , und anschließend werden die Ergebnisse auf der Kusto-Seite verarbeitet. Bei der Authentifizierung wird das Azure AD Token des aufrufenden Benutzers erneut verwendet. 

> [!NOTE]
> Dieses Beispiel sollte nicht als Empfehlung zum Filtern oder projizieren von Daten auf diese Weise verwendet werden. SQL-Abfragen sollten erstellt werden, um das kleinste DataSet zurückzugeben, da der Kusto-Optimierer derzeit nicht versucht, Abfragen zwischen Kusto und SQL zu optimieren.

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Authentication="Active Directory Integrated";'
    'Initial Catalog=Fabrikam;',
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

Das folgende Beispiel ist mit dem vorherigen identisch, mit der Ausnahme, dass die SQL-Authentifizierung über Benutzername/Kennwort erfolgt. Aus Gründen der Vertraulichkeit verwenden wir hier verborgene Zeichen folgen.

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Initial Catalog=Fabrikam;'
    h'User ID=USERNAME;'
    h'Password=PASSWORD;',
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

Im folgenden Beispiel wird eine SQL-Abfrage an eine Azure SQL-Datenbank gesendet, von der alle Datensätze aus abgerufen `[dbo].[Table]` werden, während eine andere `datetime` Spalte angehängt wird und die Ergebnisse dann auf der Kusto-Seite verarbeitet werden.
Gibt einen SQL-Parameter ( `@param0` ) an, der in der SQL-Abfrage verwendet werden soll.

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Authentication="Active Directory Integrated";'
    'Initial Catalog=Fabrikam;',
  'select *, @param0 as dt from [dbo].[Table]',
  dynamic({'param0': datetime(2020-01-01 16:47:26.7423305)}))
| where Id > 0
| project Name
```

## <a name="authentication"></a>Authentifizierung

Das sql_request-Plug-in unterstützt drei Authentifizierungsmethoden für den SQL Server-Endpunkt:

### <a name="azure-ad-integrated-authentication"></a>Azure AD integrierte Authentifizierung 

`Authentication="Active Directory Integrated"`

  Die Azure AD integrierte Authentifizierung ist die bevorzugte Methode. Bei dieser Methode wird der Benutzer oder die Anwendung über Azure AD bei Kusto authentifiziert. Das gleiche Token wird dann verwendet, um auf den SQL Server Netzwerk Endpunkt zuzugreifen.

### <a name="usernamepassword-authentication"></a>Authentifizierung mit Benutzername und Kennwort

`User ID=...; Password=...;`

  Unterstützung für Benutzername und Kennwort-Authentifizierung wird bereitgestellt, wenn Azure AD integrierte Authentifizierung nicht möglich ist. Vermeiden Sie diese Methode, wenn möglich, da geheime Informationen über Kusto gesendet werden.

### <a name="azure-ad-access-token"></a>Azure AD Zugriffs Token

`dynamic({'token': h"eyJ0..."})`

   Mit der Azure AD Zugriffs Token-Authentifizierungsmethode generiert der Aufrufer das Zugriffs Token, das von Kusto an den SQL-Endpunkt weitergeleitet wird. Die Verbindungs Zeichenfolge darf keine Authentifizierungsinformationen wie `Authentication` , `User ID` oder enthalten `Password` . Stattdessen wird das Zugriffs Token als `token` Eigenschaft im- `Options` Argument des sql_request-Plug-ins übermittelt.
     
> [!WARNING]
> Verbindungs Zeichenfolgen und Abfragen, die vertrauliche Informationen oder Informationen enthalten, die geschützt werden sollen, sollten verdeckt werden, damit Sie bei jeder Kusto-Ablauf Verfolgung ausgelassen werden.
> Weitere Informationen finden Sie unter verborgene [Zeichen folgen Literale](scalar-data-types/string.md#obfuscated-string-literals).

## <a name="encryption-and-server-validation"></a>Verschlüsselung und Server Validierung

Die folgenden Verbindungs Eigenschaften werden aus Sicherheitsgründen beim Herstellen einer Verbindung mit einem SQL Server Netzwerk Endpunkt erzwungen.

* `Encrypt`wird auf bedingungslos festgelegt `true` .
* `TrustServerCertificate`wird auf bedingungslos festgelegt `false` .

Folglich muss der SQL Server mit einem gültigen SSL/TLS-Server Zertifikat konfiguriert werden.

## <a name="specify-the-network-endpoint"></a>Netzwerk Endpunkt angeben

Die Angabe des SQL-Netzwerk Endpunkts als Teil der Verbindungs Zeichenfolge ist obligatorisch.
Die erforderliche Syntax lautet:

`Server`Voll `=` `tcp:` *qualifizierter* Name [ `,` *Port*]

Hierbei gilt Folgendes:

* *FQDN* ist der voll qualifizierte Domänen Name des Endpunkts.
* *Port* ist der TCP-Port des Endpunkts. Standardmäßig `1433` wird angenommen.

> [!NOTE]
> Andere Formen der Angabe des Netzwerk Endpunkts werden nicht unterstützt.
> Sie können beispielsweise das Präfix nicht weglassen, `tcp:` obwohl dies möglich ist, wenn die SQL-Client Bibliotheken Programm gesteuert verwendet werden.

::: zone-end

::: zone pivot="azuremonitor"

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
