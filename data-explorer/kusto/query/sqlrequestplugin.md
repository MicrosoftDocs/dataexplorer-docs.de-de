---
title: 'sql_request-Plug-in: Azure Daten-Explorer | Microsoft-Dokumentation'
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
ms.openlocfilehash: 725021ad8089d7e9ad4f897bd5a1c68f6912bf7a
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737282"
---
# <a name="sql_request-plugin"></a>Plug-In „sql_request“

::: zone pivot="azuredataexplorer"

  `evaluate``sql_request` `,` *Options* *ConnectionString* `,` *SqlQuery* `,` *SqlParameters* ConnectionString sqlQuery [SQLPARAMETERS [Optionen]] `(``)`

Das `sql_request` Plug-in sendet eine SQL-Abfrage an einen SQL Server Netzwerk Endpunkt und gibt das erste Rowset in den Ergebnissen zurück.

**Argumente**

* *ConnectionString*: ein `string` Literalwert, der die Verbindungs Zeichenfolge angibt, die auf den SQL Server Netzwerk Endpunkt zeigt. Im folgenden finden Sie gültige Authentifizierungsmethoden und die Angabe des Netzwerk Endpunkts.

* *SqlQuery*: ein `string` Literalwert, der die Abfrage angibt, die für den SQL-Endpunkt ausgeführt werden soll. Muss mindestens ein Rowset zurückgeben, aber nur das erste Rowset wird für den Rest der Kusto-Abfrage zur Verfügung gestellt.

* *SQLPARAMETERS*: ein konstanter Wert des Typs `dynamic` , der Schlüssel-Wert-Paare enthält, die als Parameter zusammen mit der Abfrage übergeben werden. Dies ist optional.
  
* *Optionen*: ein konstanter Wert des Typs `dynamic` , der Erweiterte Einstellungen als Schlüssel-Wert-Paare enthält. Derzeit kann `token` nur festgelegt werden, um ein vom Aufrufer bereitgestelltes Aad-Zugriffs Token zu übergeben, das zur Authentifizierung an den SQL-Endpunkt weitergeleitet wird Dies ist optional.

**Beispiele**

Im folgenden Beispiel wird eine SQL-Abfrage an eine Azure SQL-DatenbankDatenbank gesendet, `[dbo].[Table]`von der alle Datensätze abgerufen werden. Anschließend werden die Ergebnisse auf der Kusto-Seite verarbeitet. Bei der Authentifizierung wird das Aad-Token des aufrufenden Benutzers erneut verwendet.

Hinweis: Dieses Beispiel sollte nicht als Empfehlung zum Filtern/projizieren von Daten auf diese Weise verwendet werden. In der Regel ist es vorzuziehen, dass SQL-Abfragen erstellt werden, um das kleinste DataSet zurückzugeben, da der Kusto-Optimierer momentan nicht versucht, Abfragen zwischen Kusto und SQL zu optimieren.

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Authentication="Active Directory Integrated";'
    'Initial Catalog=Fabrikam;',
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

Das folgende Beispiel ist mit dem vorherigen identisch, mit der Ausnahme, dass die SQL-Authentifizierung über Benutzername/Kennwort erfolgt. Beachten Sie, dass wir aus Gründen der Vertraulichkeit hier verborgene Zeichen folgen verwenden.

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

**Authentifizierung**

Das sql_request-Plug-in unterstützt drei Authentifizierungsmethoden für den SQL Server-Endpunkt:

* **Integrierte Aad** -Authentifizierung`Authentication="Active Directory Integrated"`(): Dies ist die bevorzugte Methode, bei der sich der Benutzer oder die Anwendung über Aad bei Kusto authentifiziert. Anschließend wird das gleiche Token verwendet, um auf den SQL Server Netzwerk Endpunkt zuzugreifen.

* **Benutzernamen-/Kennwortauthentifizierung** (`User ID=...; Password=...;`): die Unterstützung für diese Methode wird bereitgestellt, wenn die integrierte Aad-Authentifizierung nicht ausgeführt werden Vermeiden Sie diese Methode, wenn möglich, da geheime Informationen über Kusto gesendet werden.

* **Aad-Zugriffs Token** (`dynamic({'token': h"eyJ0..."})`): bei dieser Authentifizierungsmethode wird das Zugriffs Token vom Aufrufer generiert und von Kusto an den SQL-Endpunkt weitergeleitet. Die Verbindungs Zeichenfolge darf keine Authentifizierungsinformationen `Authentication`wie `User ID`, oder `Password`enthalten. Stattdessen wird das Zugriffs Token als `token` Eigenschaft im- `Options` Argument des sql_request-Plug-ins übermittelt.
     
> [!WARNING]
> Verbindungs Zeichenfolgen und Abfragen, die vertrauliche Informationen oder Informationen enthalten, die geschützt werden sollen, sollten verdeckt werden, damit Sie bei jeder Kusto-Ablauf Verfolgung ausgelassen werden.
> Weitere Informationen finden Sie unter verborgene [Zeichen folgen Literale](scalar-data-types/string.md#obfuscated-string-literals) .

**Verschlüsselung und Server Validierung**

Die folgenden Verbindungs Eigenschaften werden erzwungen, wenn eine Verbindung mit einem SQL Server Netzwerk-Endpunkt hergestellt wird, aus Sicherheitsgründen:

* `Encrypt`wird auf `true` bedingungslos festgelegt.
* `TrustServerCertificate`wird auf `false` bedingungslos festgelegt.

Folglich muss der SQL Server mit einem gültigen SSL/TLS-Server Zertifikat konfiguriert werden.

**Angeben des Netzwerk Endpunkts**

Die Angabe des SQL-Netzwerk Endpunkts als Teil der Verbindungs Zeichenfolge ist obligatorisch.
Die erforderliche Syntax lautet:

`Server``=` *FQDN* `,` *Port*Voll qualifizierter Name [Port] `tcp:`

Hierbei gilt:

* *FQDN* ist der voll qualifizierte Domänen Name des Endpunkts.

* *Port* ist der TCP-Port des Endpunkts. Standardmäßig `1433` wird angenommen.

> [!NOTE]
> Andere Formen der Angabe des Netzwerk Endpunkts werden nicht unterstützt.
> Sie können beispielsweise das Präfix `tcp:` nicht weglassen, obwohl dies möglich ist, wenn die SQL-Client Bibliotheken Programm gesteuert verwendet werden.



::: zone-end

::: zone pivot="azuremonitor"

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
