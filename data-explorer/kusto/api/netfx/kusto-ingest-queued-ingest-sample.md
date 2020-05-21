---
title: Datenerfassung mit Kusto. Erfassungs Bibliothek-Azure Daten-Explorer
description: Dieser Artikel beschreibt die Erfassung von Daten mit der Kusto. Erfassungs Bibliothek von Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/05/2020
ms.openlocfilehash: 0e6564e6c27c62621678ae350514bf1df39c73ae
ms.sourcegitcommit: ee90472a4f9d751d4049744d30e5082029c1b8fa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83722081"
---
# <a name="data-ingestion-with-the-kustoingest-library"></a>Datenerfassung mit der Kusto. Erfassungs Bibliothek

Dieser Artikel enthält Beispielcode, in dem die Kusto. Erfassungs-Client Bibliothek für die Datenerfassung verwendet wird. Der Code erläutert den empfohlenen Erfassungs Modus für Pipelines auf Produktionsbasis, die als Erfassung in der Warteschlange bezeichnet werden. Die zugehörige Entität für die Kusto. Erfassungs Bibliothek ist die [ikustoqueuedingestclient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) -Schnittstelle. Der Client Code interagiert mit dem Azure-Daten-Explorer Dienst, indem Erfassungs Benachrichtigungen an eine Azure-Warteschlange gesendet werden. Der Verweis auf die Warteschlange wird von der Datenverwaltung Entität abgerufen, die für die Erfassung verantwortlich ist. 

> [!NOTE]
> Die Interaktion mit dem Datenverwaltung-Dienst muss mithilfe Azure Active Directory (Azure AD) authentifiziert werden.

Das Beispiel verwendet Azure AD Benutzerauthentifizierung und wird unter der Identität des interaktiven Benutzers ausgeführt.

## <a name="dependencies"></a>Abhängigkeiten

Dieser Beispielcode erfordert die folgenden nuget-Pakete:
* Microsoft. Kusto. Erfassung
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

Der Code bewirkt Folgendes:
1. Erstellt eine Tabelle auf dem frei `KustoLab` gegebenen Azure Daten-Explorer-Cluster unter der `KustoIngestClientDemo` Datenbank.
2. Stellt ein [JSON-Spalten Zuordnung-Objekt](../../management/create-ingestion-mapping-command.md) für diese Tabelle bereit.
3. Erstellt eine [ikustoqueuedingestclient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) -Instanz für den `Ingest-KustoLab` Datenverwaltung-Dienst.
4. Richtet [kustoqueuedingestionproperties](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties) mit den entsprechenden Erfassungs Optionen ein.
5. Erstellt einen MemoryStream mit einigen generierten Daten, die erfasst werden sollen.
6. Erfassen der Daten mithilfe der- `KustoQueuedIngestClient.IngestFromStream` Methode
7. Fragt nach Erfassungs [Fehlern](kusto-ingest-client-status.md#tracking-ingestion-status-kustoqueuedingestclient) ab.

```csharp
static void Main(string[] args)
{
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
        var columnMappings = new List<ColumnMapping>();
            columnMappings.Add(new ColumnMapping()
            {
                ColumnName = "Column1",
                Properties = new Dictionary<string, string>() {
                    { Data.Common.MappingConsts.Path, "$.Id" },
            } });
            columnMappings.Add(new ColumnMapping()
            {
                ColumnName = "Column2",
                Properties = new Dictionary<string, string>() {
                    { Data.Common.MappingConsts.Path, "$.Timestamp" },
            }
            });
            columnMappings.Add(new ColumnMapping()
            {
                ColumnName = "Column3",
                Properties = new Dictionary<string, string>() {
                    { Data.Common.MappingConsts.Path, "$.Message" },
            }
            });
            var secondCommand = CslCommandGenerator.GenerateTableMappingCreateCommand(
                Data.Ingestion.IngestionMappingKind.Json, table, mappingName, columnMappings);
        kustoAdminClient.ExecuteControlCommand(databaseName: db, command: secondCommand);
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
        ingestProps.IngestionMapping = new IngestionMapping()
        { 
            IngestionMappingReference = mappingName
        };
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
        var errors = ingestClient.GetAndDiscardTopIngestionFailuresAsync().GetAwaiter().GetResult();
        var successes = ingestClient.GetAndDiscardTopIngestionSuccessesAsync().GetAwaiter().GetResult();

        errors.ForEach((f) => { Console.WriteLine($"Ingestion error: {f.Info.Details}"); });
        successes.ForEach((s) => { Console.WriteLine($"Ingested: {s.Info.IngestionSourcePath}"); });
    }
}
```
