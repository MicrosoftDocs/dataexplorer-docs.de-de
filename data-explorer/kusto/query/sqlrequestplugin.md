---
title: sql_request-Plugin - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird sql_request-Plugin in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: f0c0837c6bb8e4dcd3cf2e28af18d02c19edb676
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766175"
---
# <a name="sql_request-plugin"></a>Plug-In „sql_request“

::: zone pivot="azuredataexplorer"

  `evaluate``sql_request` `,` `,` *SqlParameters* `,` *Options* *SqlQuery* *ConnectionString* ConnectionString SqlQuery [ SqlParameters [ Optionen ]] `(``)`

Das `sql_request` Plugin sendet eine SQL-Abfrage an einen SQL Server-Netzwerkendpunkt und gibt das erste Rowset in den Ergebnissen zurück.

**Argumente**

* *ConnectionString*: `string` Ein Literal, das die Verbindungszeichenfolge angibt, die auf den SQL Server-Netzwerkendpunkt verweist. Siehe unten für gültige Authentifizierungsmethoden und wie Sie den Netzwerkendpunkt angeben.

* *SqlQuery*: `string` Ein Literal, das die Abfrage angibt, die für den SQL-Endpunkt ausgeführt werden soll. Muss ein oder mehrere Rowsets zurückgeben, aber nur das erste wird für den Rest der Kusto-Abfrage verfügbar gemacht.

* *SqlParameters*: Ein konstanter Wert des Typs, `dynamic` der Schlüssel-Wert-Paare enthält, die zusammen mit der Abfrage als Parameter übergeben werden sollen. Optional.
  
* *Optionen*: Ein konstanter Wert des Typs, `dynamic` der erweiterte Einstellungen als Schlüssel-Wert-Paare enthält. Derzeit `token` kann nur festgelegt werden, um ein vom Aufrufer bereitgestelltes AAD-Zugriffstoken zu übergeben, das zur Authentifizierung an den SQL-Endpunkt weitergeleitet wird. Optional.

**Beispiele**

Im folgenden Beispiel wird eine SQL-Abfrage an eine `[dbo].[Table]`Azure SQL DB-Datenbank gesendet, in der alle Datensätze von abgerufen und anschließend die Ergebnisse auf der Kusto-Seite verarbeitet werden. Die Authentifizierung verwendet das AAD-Token des aufrufenden Benutzers wieder.

Hinweis: Dieses Beispiel sollte nicht als Empfehlung zum Filtern/Projektieren von Daten auf diese Weise genommen werden. Es ist in der Regel vorzuziehen, dass SQL-Abfragen erstellt werden, um den kleinsten Datensatz zurückzugeben, der möglich ist, da der Kusto-Optimierer derzeit nicht versucht, Abfragen zwischen Kusto und SQL zu optimieren.

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Authentication="Active Directory Integrated";'
    'Initial Catalog=Fabrikam;',
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

Das folgende Beispiel ist identisch mit dem vorherigen Beispiel, mit der Ausnahme, dass die SQL-Authentifizierung durch Benutzername/Kennwort erfolgt. Beachten Sie, dass wir aus Gründen der Vertraulichkeit verschleierte Zeichenfolgen hier verwenden.

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

Das sql_request-Plugin unterstützt drei Methoden der Authentifizierung für den SQL Server-Endpunkt:

* **AAD-integrierte Authentifizierung** (`Authentication="Active Directory Integrated"`): Dies ist die bevorzugte Methode, bei der sich der Benutzer oder die Anwendung über AAD zu Kusto authentifiziert und das gleiche Token dann für den Zugriff auf den SQL Server-Netzwerkendpunkt verwendet wird.

* **Benutzername/Kennwortauthentifizierung** (`User ID=...; Password=...;`): Unterstützung für diese Methode wird bereitgestellt, wenn die integrierte AAD-Authentifizierung nicht durchgeführt werden kann. Vermeiden Sie diese Methode, wenn möglich, da geheime Informationen über Kusto gesendet werden.

* **AAD-Zugriffstoken** (`dynamic({'token': h"eyJ0..."})`): Mit dieser Authentifizierungsmethode wird das Zugriffstoken vom Aufrufer generiert und von Kusto an den SQL-Endpunkt weitergeleitet. Die Verbindungszeichenfolge sollte keine `Authentication`Authentifizierungsinformationen wie enthalten, wie , `User ID`oder `Password`. Stattdessen wird das Zugriffstoken `token` als `Options` Eigenschaft im Argument des sql_request-Plugins übergeben.
     
> [!WARNING]
> Verbindungszeichenfolgen und Abfragen, die vertrauliche Informationen oder Informationen enthalten, die geschützt werden sollen, sollten verschleiert werden, damit sie in jeder Kusto-Ablaufverfolgung weggelassen werden.
> Weitere Informationen finden Sie unter [verschleierte Zeichenfolgenliterale.](scalar-data-types/string.md#obfuscated-string-literals)

**Verschlüsselung und Servervalidierung**

Die folgenden Verbindungseigenschaften werden beim Herstellen einer Verbindung mit einem SQL Server-Netzwerkendpunkt aus Sicherheitsgründen erzwungen:

* `Encrypt`ist `true` bedingungslos eingestellt.
* `TrustServerCertificate`ist `false` bedingungslos eingestellt.

Daher muss der SQL Server mit einem gültigen SSL/TLS-Serverzertifikat konfiguriert werden.

**Angeben des Netzwerkendpunkts**

Die Angabe des SQL-Netzwerkendpunkts als Teil der Verbindungszeichenfolge ist obligatorisch.
Die erforderliche Syntax lautet:

`Server``=` *FQDN* `,` *Port*FQDN [ Anschluss ] `tcp:`

Hierbei gilt:

* *FQDN* ist der vollqualifizierte Domänenname des Endpunkts.

* *Port* ist der TCP-Port des Endpunkts. Standardmäßig `1433` wird angenommen.

> [!NOTE]
> Andere Formen der Angabe des Netzwerkendpunkts werden nicht unterstützt.
> Man kann z. B. das `tcp:` Präfix nicht weglassen, obwohl dies bei programmgesteuerter Verwendung der SQL-Clientbibliotheken möglich ist.



::: zone-end

::: zone pivot="azuremonitor"

Dies wird in Azure Monitor nicht unterstützt.

::: zone-end
