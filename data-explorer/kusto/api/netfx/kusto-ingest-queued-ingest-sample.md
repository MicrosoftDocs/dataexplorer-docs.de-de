---
title: HowTo Datenerfassung mit Kusto.Ingest-Bibliothek - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt HowTo-Datenerfassung mit Kusto.Ingest Library in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/05/2020
ms.openlocfilehash: 80b2b61c70269c5bd166a064fe9d0e2c59dd8197
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523629"
---
# <a name="howto-data-ingestion-with-kustoingest-library"></a>HowTo Datenerfassung mit Kusto.Ingest Library
In diesem Artikel wird Beispielcode dargestellt, der die Kusto.Ingest-Clientbibliothek verwendet.

## <a name="overview"></a>Übersicht
Das folgende Codebeispiel veranschaulicht die Datenerfassung von Queued (über Kusto Data Management Service) an Kusto mithilfe der Kusto.Ingest-Bibliothek.

> Dieser Artikel befasst sich mit dem empfohlenen Erfassungsmodus für Pipelines für Produktionsqualität, der auch als **Queued Ingestion** bezeichnet wird (in Bezug auf die Kusto.Ingest-Bibliothek ist die entsprechende Entität die [IKustoQueuedIngestClient-Schnittstelle).](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) In diesem Modus interagiert der Clientcode mit dem Kusto-Dienst, indem er Erfassungsbenachrichtigungen in einer Azure-Warteschlange sendet, auf die verwiesen wird, die von der Kusto-Datenverwaltung (auch bekannt als "Kusto Data Management") abgerufen wird. Verunstesung) Service. Die Interaktion mit dem Datenverwaltungsdienst muss mit **AAD**authentifiziert werden.

#### <a name="authentication"></a>Authentifizierung
Dieses Codebeispiel verwendet die AAD-Benutzerauthentifizierung und wird unter der Identität des interaktiven Benutzers ausgeführt.

## <a name="dependencies"></a>Abhängigkeiten
Dieser Beispielcode erfordert die folgenden NuGet-Pakete:
* Microsoft.Kusto.Ingest
* Microsoft.IdentityModel.Clients.ActiveDirectory
* WindowsAzure.Storage
* Newtonsoft.Json

## <a name="namespaces-used"></a>Verwendete Namespaces
```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Kusto.Data;
using Kusto.Data.Common;
using Kusto.Data.Net.Client;
using Kusto.Ingest;
```

## <a name="code"></a>Code
Der unten dargestellte Code führt Folgendes aus:
1. Erstellt eine `KustoLab` Tabelle im freigegebenen Kusto-Cluster unter `KustoIngestClientDemo` Datenbank
2. Stellt ein [JSON-Spaltenzuordnungsobjekt](../../management/create-ingestion-mapping-command.md) für diese Tabelle zur Zeit ein
3. Erstellt eine [IKustoQueuedIngestClient-Instanz](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) für den `Ingest-KustoLab` Datenverwaltungsdienst
4. Richtet [KustoQueuedIngestionProperties](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties) mit entsprechenden Aufnahmeoptionen ein
5. Erstellt einen MemoryStream, der mit einigen generierten Daten gefüllt ist, die aufgenommen werden sollen
6. Erfasst die Daten `KustoQueuedIngestClient.IngestFromStream` mithilfe der Methode
7. Umfragen zu [Einnahmefehlern](kusto-ingest-client-status.md#tracking-ingestion-status-kustoqueuedingestclient)

```csharp
static void Main(string[] args)
{
    var clusterName = "KustoLab";
    var db = "KustoIngestClientDemo";
    var table = "Table1";
    var mappingName = "Table1_mapping_1";
    var serviceNameAndRegion = "clusterNameAndRegion"; // For example, "mycluster.westus"
    var authority = "AAD Tenant or name"; // For example, "microsoft.com"

    // Set up table
    var kcsbEngine =
        new KustoConnectionStringBuilder($"https://{serviceNameAndRegion}.kusto.windows.net").WithAadUserPromptAuthentication(authority: $"{authority}");

    using (var kustoAdminClient = KustoClientFactory.CreateCslAdminProvider(kcsbEngine))
    {
        var columns = new List<Tuple<string, string>>()
        {
            new Tuple<string, string>("Column1", "System.Int64"),
            new Tuple<string, string>("Column2", "System.DateTime"),
            new Tuple<string, string>("Column3", "System.String"),
        };

        var command = CslCommandGenerator.GenerateTableCreateCommand(table, columns);
        kustoAdminClient.ExecuteControlCommand(databaseName: db, command: command);

        // Set up mapping
        var columnMappings = new List<JsonColumnMapping>();
        columnMappings.Add(new JsonColumnMapping()
            { ColumnName = "Column1", JsonPath = "$.Id" });
        columnMappings.Add(new JsonColumnMapping()
            { ColumnName = "Column2", JsonPath = "$.Timestamp" });
        columnMappings.Add(new JsonColumnMapping()
            { ColumnName = "Column3", JsonPath = "$.Message" });

        command = CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
                                            table, mappingName, columnMappings);
        kustoAdminClient.ExecuteControlCommand(databaseName: db, command: command);
    }

    // Create Ingest Client
    var kcsbDM =
        new KustoConnectionStringBuilder($"https://ingest-{serviceNameAndRegion}.kusto.windows.net").WithAadUserPromptAuthentication(authority: $"{authority}");

    using (var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(kcsbDM))
    {
        var ingestProps = new KustoQueuedIngestionProperties(db, table);
        // For the sake of getting both failure and success notifications we set this to IngestionReportLevel.FailuresAndSuccesses
        // Usually the recommended level is IngestionReportLevel.FailuresOnly
        ingestProps.ReportLevel = IngestionReportLevel.FailuresAndSuccesses;
        ingestProps.ReportMethod = IngestionReportMethod.Queue;
        ingestProps.JSONMappingReference = mappingName;
        ingestProps.Format = DataSourceFormat.json;

        // Prepare data for ingestion
        using (var memStream = new MemoryStream())
        using (var writer = new StreamWriter(memStream))
        {
            for (int counter = 1; counter <= 10; ++counter)
            {
                writer.WriteLine(
                    "{{ \"Id\":\"{0}\", \"Timestamp\":\"{1}\", \"Message\":\"{2}\" }}",
                    counter, DateTime.UtcNow.AddSeconds(100 * counter),
                    $"This is a dummy message number {counter}");
            }

            writer.Flush();
            memStream.Seek(0, SeekOrigin.Begin);

            // Post ingestion message
            ingestClient.IngestFromStream(memStream, ingestProps, leaveOpen: true);
        }

        // Wait and retrieve all notifications
        //  - Actual duration should be decided based on the effective Ingestion Batching Policy set on the table/database
        Thread.Sleep(<timespan>);
        var errors = ingestClient.GetAndDiscardTopIngestionFailures().GetAwaiter().GetResult();
        var successes = ingestClient.GetAndDiscardTopIngestionSuccesses().GetAwaiter().GetResult();

        errors.ForEach((f) => { Console.WriteLine($"Ingestion error: {f.Info.Details}"); });
        successes.ForEach((s) => { Console.WriteLine($"Ingested: {s.Info.IngestionSourcePath}"); });
    }
}
```