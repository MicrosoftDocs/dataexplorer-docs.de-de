---
title: Hinzufügen von Datenbankprinzipalen für Azure Data Explorer mithilfe von C#
description: In diesem Artikel erfahren Sie, wie Sie Datenbankprinzipale für Azure Data Explorer mithilfe von C# hinzufügen.
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 97e7b7430410ce73f3577fdf37a2635169ea191b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350025"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-c"></a>Hinzufügen von Datenbankprinzipalen für Azure Data Explorer mithilfe von C#

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Azure Resource Manager-Vorlage](database-principal-resource-manager.md)

Azure-Daten-Explorer ist ein schneller und hochgradig skalierbarer Dienst zur Untersuchung von Daten (Protokoll- und Telemetriedaten). In diesem Artikel fügen Sie Datenbankprinzipale für Azure Data Explorer mithilfe von C# hinzu.

## <a name="prerequisites"></a>Voraussetzungen

* Falls Sie Visual Studio 2019 nicht installiert haben, können Sie die **kostenlose** [Visual Studio 2019 Community-Edition](https://www.visualstudio.com/downloads/) herunterladen und verwenden. Aktivieren Sie beim Setup von Visual Studio die Option **Azure-Entwicklung**.
* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* [Erstellen eines Clusters und einer Datenbank](create-cluster-database-csharp.md)

## <a name="install-c-nuget"></a>Installieren eines C#-NuGet-Pakets

* Installieren Sie [Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installieren Sie [Microsoft.Rest.ClientRuntime.Azure.Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) für die Authentifizierung.

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>Hinzufügen eines Datenbankprinzipals

Im folgenden Beispiel wird gezeigt, wie Sie einen Datenbankprinzipal programmgesteuert hinzufügen.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var kustoManagementClient = new KustoManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
string principalAssignmentName = "databasePrincipalAssignment1";
string principalId = "xxxxxxxx";//User email, application ID, or security group name
string role = "Admin";//Admin, Ingestor, Monitor, User, UnrestrictedViewers, Viewer
string tenantIdForPrincipal = tenantId;
string principalType = "App";//User, App, or Group

var databasePrincipalAssignment = new DatabasePrincipalAssignment(principalId, role, principalType, tenantId: tenantIdForPrincipal);
await kustoManagementClient.DatabasePrincipalAssignments.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, principalAssignmentName, databasePrincipalAssignment);
```

|**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Ihre Mandanten-ID. Wird auch als Verzeichnis-ID bezeichnet.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Die Abonnement-ID, die Sie für die Ressourcenerstellung verwenden.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Die Client-ID der Anwendung, die auf Ressourcen in Ihrem Mandanten zugreifen kann.|
| clientSecret | *xxxxxxxxxxxxxx* | Das Clientgeheimnis der Anwendung, die auf Ressourcen in Ihrem Mandanten zugreifen kann. |
| resourceGroupName | *testrg* | Der Name der Ressourcengruppe, die Ihren Cluster enthält.|
| clusterName | *mykustocluster* | Der Name Ihres Clusters.|
| databaseName | *mykustodatabase* | Der Name Ihrer Datenbank.|
| principalAssignmentName | *databasePrincipalAssignment1* | Der Name Ihrer Datenbankprinzipalressource.|
| principalId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Die Prinzipal-ID. Dies kann die E-Mail-Adresse des Benutzers, die Anwendungs-ID oder der Name der Sicherheitsgruppe sein.|
| Rolle (role) | *Administrator* | Die Rolle Ihres Datenbankprinzipals, die „Admin“, „Ingestor“, „Monitor“, „Benutzer“, „UnrestrictedViewers“'oder „Viewer“ sein kann.|
| tenantIdForPrincipal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Die Mandanten-ID des Prinzipals.|
| principalType | *App* | Der Typ des Prinzipals, der „Benutzer“, „App“ oder „Gruppe“ sein kann.|

## <a name="next-steps"></a>Nächste Schritte

* [Erfassen von Daten mit der Azure Data Explorer-Bibliothek für Node](node-ingest-data.md)
