---
title: Erfassen von Daten mit dem .NET SDK für Azure Data Explorer
description: In diesem Artikel erfahren Sie, wie Sie Daten mit .NET SDK in Azure Data Explorer erfassen (laden).
author: orspod
ms.author: orspodek
ms.reviewer: vladikb
ms.service: data-explorer
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: c6a544228d5527f1703567256bd3e824ddc0504a
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872708"
---
# <a name="ingest-data-using-the-azure-data-explorer-net-sdk"></a>Erfassen von Daten mit dem .NET SDK für Azure Data Explorer 

> [!div class="op_single_selector"]
> * [.NET](net-sdk-ingest-data.md)
> * [Python](python-ingest-data.md)
> * [Node](node-ingest-data.md)
> * [Go](go-ingest-data.md)

Azure-Daten-Explorer ist ein schneller und hochgradig skalierbarer Dienst zur Untersuchung von Daten (Protokoll- und Telemetriedaten). Er bietet zwei Clientbibliotheken für .NET: eine [Erfassungsbibliothek](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest/) und eine [Datenbibliothek](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data/). Weitere Informationen zum .NET SDK finden Sie unter [Über .NET SDK](/azure/data-explorer/kusto/api/netfx/about-the-sdk).
Mit diesen Bibliotheken können Sie über Ihren Code Daten in einem Cluster erfassen (laden) und Daten abfragen. In diesem Artikel erstellen Sie zunächst eine Tabelle und eine Datenzuordnung in einem Testcluster. Anschließend stellen Sie die Erfassung im Cluster in eine Warteschlange und überprüfen die Ergebnisse.

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

* [Einen Testcluster und eine Testdatenbank](create-cluster-database-portal.md)

## <a name="install-the-ingest-library"></a>Installieren der Erfassungsbibliothek

```azurecli
Install-Package Microsoft.Azure.Kusto.Ingest
```

## <a name="add-authentication-and-construct-connection-string"></a>Hinzufügen von Authentifizierung und Erstellen einer Verbindungszeichenfolge

### <a name="authentication"></a>Authentifizierung

Für die Authentifizierung von Anwendungen verwendet das Azure Data Explorer SDK Ihre AAD-Mandanten-ID. Um Ihre Mandanten-ID zu suchen, verwenden Sie die folgende URL, und ersetzen Sie dabei *YourDomain* durch Ihre Domäne.

```http
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Wenn Ihre Domäne beispielsweise *contoso.com* ist, lautet die URL: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Klicken Sie auf diese URL, um die Ergebnisse anzuzeigen. Die erste Zeile lautet wie folgt. 

```console
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Die Mandanten-ID ist in diesem Fall `6babcaad-604b-40ac-a9d7-9fd97c0b779f`.

In diesem Beispiel wird eine interaktive AAD-Benutzerauthentifizierung verwendet, um auf den Cluster zuzugreifen. Sie können auch die AAD-Anwendungsauthentifizierung mit Zertifikat oder Anwendungsgeheimnis verwenden. Achten Sie darauf, vor dem Ausführen dieses Codes die richtigen Werte für `tenantId` und `clusterUri` festzulegen. 

Das Azure Data Explorer SDK bietet eine bequeme Möglichkeit zum Einrichten der Authentifizierungsmethode als Teil der Verbindungszeichenfolge. Eine umfassende Dokumentation zu Azure Data Explorer-Verbindungszeichenfolgen finden Sie unter [Verbindungszeichenfolgen](kusto/api/connection-strings/kusto.md).

> [!NOTE]
> Die aktuelle Version des SDK unterstützt keine interaktive Benutzerauthentifizierung für .NET Core. Verwenden Sie bei Bedarf stattdessen AAD-Benutzername und -Kennwort oder die Anwendungsauthentifizierung.

### <a name="construct-the-connection-string"></a>Erstellen der Verbindungszeichenfolge

Nun können Sie die Azure Data Explorer-Verbindungszeichenfolge erstellen. Sie erstellen die Zieltabelle und die Zuordnung in einem späteren Schritt.

```csharp
var tenantId = "<TenantId>";
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net/";

var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(kustoUri).WithAadUserPromptAuthentication(tenantId);
```

## <a name="set-source-file-information"></a>Festlegen der Informationen zur Quelldatei

Legen Sie den Pfad für die Quelldatei fest. In diesem Beispiel wird eine Beispieldatei verwendet, die in Azure Blob Storage gehostet wird. Das **StormEvents**-Beispieldataset enthält wetterbezogene Daten der [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

```csharp
var blobPath = "https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
```

## <a name="create-a-table-on-your-test-cluster"></a>Erstellen einer Tabelle im Testcluster

Erstellen Sie eine Tabelle mit dem Namen `StormEvents`, die dem Schema der Daten in der Datei `StormEvents.csv` entspricht.

> [!TIP]
> Die folgenden Codeausschnitte erstellen eine Instanz eines Clients für fast jeden Aufruf. Dadurch kann jeder Ausschnitt einzeln ausgeführt werden. In der Produktionsumgebung sind die Clientinstanzen wiedereintrittsfähig und sollten so lange wie nötig aufbewahrt werden. Eine einzelne Clientinstanz pro URI ist ausreichend, auch wenn Sie mit mehreren Datenbanken arbeiten (Datenbanken können auf Befehlsebene angegeben werden).

```csharp
var databaseName = "<DatabaseName>";
var table = "StormEvents";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("StartTime", "System.DateTime"),
                Tuple.Create("EndTime", "System.DateTime"),
                Tuple.Create("EpisodeId", "System.Int32"),
                Tuple.Create("EventId", "System.Int32"),
                Tuple.Create("State", "System.String"),
                Tuple.Create("EventType", "System.String"),
                Tuple.Create("InjuriesDirect", "System.Int32"),
                Tuple.Create("InjuriesIndirect", "System.Int32"),
                Tuple.Create("DeathsDirect", "System.Int32"),
                Tuple.Create("DeathsIndirect", "System.Int32"),
                Tuple.Create("DamageProperty", "System.Int32"),
                Tuple.Create("DamageCrops", "System.Int32"),
                Tuple.Create("Source", "System.String"),
                Tuple.Create("BeginLocation", "System.String"),
                Tuple.Create("EndLocation", "System.String"),
                Tuple.Create("BeginLat", "System.Double"),
                Tuple.Create("BeginLon", "System.Double"),
                Tuple.Create("EndLat", "System.Double"),
                Tuple.Create("EndLon", "System.Double"),
                Tuple.Create("EpisodeNarrative", "System.String"),
                Tuple.Create("EventNarrative", "System.String"),
                Tuple.Create("StormSummary", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(databaseName, command);
}
```

## <a name="define-ingestion-mapping"></a>Definieren der Erfassungszuordnung

Ordnen Sie die eingehenden CSV-Daten den beim Erstellen der Tabelle verwendeten Spaltennamen zu.
Stellen Sie ein [Objekt für die CSV-Spaltenzuordnung](kusto/management/create-ingestion-mapping-command.md) in dieser Tabelle bereit.

```csharp
var tableMapping = "StormEvents_CSV_Mapping";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableMappingCreateCommand(
            Data.Ingestion.IngestionMappingKind.Csv,
            table,
            tableMapping,
            new[] {
                new ColumnMapping() { ColumnName = "StartTime", Properties = new Dictionary<string, string>() { { MappingConsts.Ordinal, "0" } } },
                new ColumnMapping() { ColumnName = "EndTime", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "1" } } },
                new ColumnMapping() { ColumnName = "EpisodeId", Properties = new Dictionary<string, string>() { { MappingConsts.Ordinal, "2" } } },
                new ColumnMapping() { ColumnName = "EventId", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "3" } } },
                new ColumnMapping() { ColumnName = "State", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "4" } } },
                new ColumnMapping() { ColumnName = "EventType", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "5" } } },
                new ColumnMapping() { ColumnName = "InjuriesDirect", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "6" } } },
                new ColumnMapping() { ColumnName = "InjuriesIndirect", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "7" } } },
                new ColumnMapping() { ColumnName = "DeathsDirect", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "8" } } },
                new ColumnMapping() { ColumnName = "DeathsIndirect", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "9" } } },
                new ColumnMapping() { ColumnName = "DamageProperty", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "10" } } },
                new ColumnMapping() { ColumnName = "DamageCrops", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "11" } } },
                new ColumnMapping() { ColumnName = "Source", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "12" } } },
                new ColumnMapping() { ColumnName = "BeginLocation", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "13" } } },
                new ColumnMapping() { ColumnName = "EndLocation", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "14" } } },
                new ColumnMapping() { ColumnName = "BeginLat", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "15" } } },
                new ColumnMapping() { ColumnName = "BeginLon", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "16" } } },
                new ColumnMapping() { ColumnName = "EndLat", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "17" } } },
                new ColumnMapping() { ColumnName = "EndLon", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "18" } } },
                new ColumnMapping() { ColumnName = "EpisodeNarrative", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "19" } } },
                new ColumnMapping() { ColumnName = "EventNarrative", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "20" } } },
                new ColumnMapping() { ColumnName = "StormSummary", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "21" } } }
        });

    kustoClient.ExecuteControlCommand(databaseName, command);
}
```

## <a name="define-batching-policy-for-your-table"></a>Definieren der Batchverarbeitungsrichtlinie für die Tabelle

Die Azure Data Explorer-Erfassung führt die Batchverarbeitung der eingehenden Daten durch, um die Größe der Daten-Shards zu optimieren. Dieser Prozess wird von der [Richtlinie zur Erfassungsbatchverarbeitung](kusto/management/batchingpolicy.md) gesteuert und kann durch den [Steuerungsbefehl für die Richtlinie zur Erfassungsbatchverarbeitung](kusto/management/batching-policy.md) geändert werden. Verwenden Sie diese Richtlinie, um die Latenz von langsam eintreffenden Daten zu reduzieren.

```kusto
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableAlterIngestionBatchingPolicyCommand(
        databaseName,
        table,
        new IngestionBatchingPolicy(maximumBatchingTimeSpan: TimeSpan.FromSeconds(10.0), maximumNumberOfItems: 100, maximumRawDataSizeMB: 1024));

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="queue-a-message-for-ingestion"></a>Stellen einer Nachricht in eine Warteschlange für die Erfassung

Stellen Sie eine Nachricht zum Abrufen von Daten aus Blob Storage in eine Warteschlange, und erfassen Sie diese Daten im Azure-Daten-Explorer. Eine Verbindung mit dem Datenerfassungsendpunkt des Azure Data Explorer-Clusters wird, und ein weiterer Client wird erstellt, um mit diesem Endpunkt zu arbeiten. 

> [!TIP]
> Die folgenden Codeausschnitte erstellen eine Instanz eines Clients für fast jeden Aufruf. Dadurch kann jeder Ausschnitt einzeln ausgeführt werden. In der Produktionsumgebung sind die Clientinstanzen wiedereintrittsfähig und sollten so lange wie nötig aufbewahrt werden. Eine einzelne Clientinstanz pro URI ist ausreichend, auch wenn Sie mit mehreren Datenbanken arbeiten (Datenbanken können auf Befehlsebene angegeben werden).

```csharp
var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net";
var ingestConnectionStringBuilder = new KustoConnectionStringBuilder(ingestUri).WithAadUserPromptAuthentication(tenantId);

using (var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder))
{
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.csv,
            IngestionMapping = new IngestionMapping()
            { 
                IngestionMappingReference = tableMapping,
                IngestionMappingKind = IngestionMappingKind.Csv
            },
            IgnoreFirstRecord = true
        };

    ingestClient.IngestFromStorageAsync(blobPath, ingestionProperties: properties).GetAwaiter().GetResult();
}
```

## <a name="validate-data-was-ingested-into-the-table"></a>Überprüfen, ob die Daten in der Tabelle erfasst wurden

Warten Sie fünf bis zehn Minuten, bis die Warteschlange die Erfassung geplant und die Daten in den Azure-Daten-Explorer geladen hat. Führen Sie dann den folgenden Code aus, um die Anzahl von Datensätzen in der Tabelle `StormEvents` zu erhalten.

```csharp
using (var cslQueryProvider = KustoClientFactory.CreateCslQueryProvider(kustoConnectionStringBuilder))
{
    var query = $"{table} | count";

    var results = cslQueryProvider.ExecuteQuery<long>(databaseName, query);
    Console.WriteLine(results.Single());
}
```

## <a name="run-troubleshooting-queries"></a>Ausführen von Abfragen zur Problembehandlung

Melden Sie sich bei [https://dataexplorer.azure.com](https://dataexplorer.azure.com) an, und stellen Sie eine Verbindung mit Ihrem Cluster her. Führen Sie den folgenden Befehl in Ihrer Datenbank aus, um festzustellen, ob in den letzten vier Stunden Erfassungsfehler aufgetreten sind. Ersetzen Sie den Namen der Datenbank vor dem Ausführen.

```kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

Führen Sie den folgenden Befehl aus, um den Status aller Erfassungsvorgänge in den letzten vier Stunden anzuzeigen. Ersetzen Sie den Namen der Datenbank vor dem Ausführen.

```kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie unsere anderen Artikel durcharbeiten möchten, behalten Sie die erstellten Ressourcen bei. Wenn dies nicht der Fall ist, führen Sie den folgenden Befehl in der Datenbank aus, um die Tabelle `StormEvents` zu bereinigen.

```kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Nächste Schritte

* [Write queries](write-queries.md) (Schreiben von Abfragen)
