---
title: Kusto. Erfassungs Codebeispiele-Azure Daten-Explorer
description: In diesem Artikel werden die Beispiele für die Erfassung von Erfassungs Code in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: ohbitton
ms.service: data-explorer
ms.topic: reference
ms.date: 05/19/2019
ms.openlocfilehash: 73c2a01d3b71a541e8d3152f190732c394f5c435
ms.sourcegitcommit: ee90472a4f9d751d4049744d30e5082029c1b8fa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83722115"
---
# <a name="kustoingest-ingestion-code-examples"></a>Beispiele für die Erfassung von Erfassungs Code

Diese Auflistung kurzer Code Ausschnitte veranschaulicht verschiedene Techniken zum Erfassen von Daten in einer Kusto-Tabelle.

> [!NOTE]
> Diese Beispiele sehen so aus, als ob der Erfassungs Client sofort nach der Erfassung zerstört wird. Verwenden Sie dies nicht wörtlich.
> Erfassungs Clients sind Wiedereintritts fähige und Thread sicher und sollten nicht in großen Zahlen erstellt werden. Die empfohlene Kardinalität der Erfassungs Client Instanzen ist eine pro Hostingprozess pro Ziel-Kusto-Cluster.

## <a name="async-ingestion-from-a-single-azure-blob"></a>Asynchrone Erfassung von einem einzelnen Azure-BLOB

Verwenden Sie kustoqueuedingestclient mit optionaler retrypolicy für die asynchrone Erfassung von einem einzelnen Azure-BLOB.

```csharp
//Create Kusto connection string with App Authentication
var kustoConnectionStringBuilderDM =
    new KustoConnectionStringBuilder(@"https://ingest-{clusterNameAndRegion}.kusto.windows.net").WithAadApplicationKeyAuthentication(
        applicationClientId: "{Application Client ID}",
        applicationKey: "{Application Key (secret)}",
        authority: "{AAD TenantID or name}");

// Create an ingest client
// Note, that creating a separate instance per ingestion operation is an anti-pattern.
// IngestClient classes are thread-safe and intended for reuse
IKustoIngestClient client = KustoIngestFactory.CreateQueuedIngestClient(kustoConnectionStringBuilderDM);

// Ingest from blobs according to the required properties
var kustoIngestionProperties = new KustoIngestionProperties(databaseName: "myDB", tableName: "myTable");

var sourceOptions = new StorageSourceOptions() { DeleteSourceOnSuccess = true };

//// Create your custom implementation of IRetryPolicy, which will affect how the ingest client handles retrying on transient failures
IRetryPolicy retryPolicy = new NoRetry();
//// This line sets the retry policy on the ingest client that will be enforced on every ingest call from here on
((IKustoQueuedIngestClient)client).QueueRetryPolicy = retryPolicy;

await client.IngestFromStorageAsync(uri: @"BLOB-URI-WITH-SAS-KEY", ingestionProperties: kustoIngestionProperties, sourceOptions);

client.Dispose();
```

## <a name="ingest-from-local-file"></a>Erfassung aus lokaler Datei 

Verwenden Sie kustodirectingestclient zur Erfassung aus einer lokalen Datei.


> [!NOTE]
> Wir empfehlen diese Methode für ein begrenztes Volume und eine Erfassung mit geringer Frequenz.

```csharp
// Create Kusto connection string with App Authentication
var kustoConnectionStringBuilderEngine =
    new KustoConnectionStringBuilder(@"https://{clusterNameAndRegion}.kusto.windows.net").WithAadApplicationKeyAuthentication(
        applicationClientId: "{Application Client ID}",
        applicationKey: "{Application Key (secret)}",
        authority: "{AAD TenantID or name}");

// Create a disposable client that will execute the ingestion
using (IKustoIngestClient client = KustoIngestFactory.CreateDirectIngestClient(kustoConnectionStringBuilderEngine))
{
    //Ingest from blobs according to the required properties
    var kustoIngestionProperties = new KustoIngestionProperties(databaseName: "myDB", tableName: "myTable");

    client.IngestFromStorageAsync(@"< Path to local file >", ingestionProperties: kustoIngestionProperties).GetAwaiter().GetResult();
}
```

## <a name="ingest-from-local-files-and-validate-ingestion"></a>Erfassung aus lokalen Dateien und Überprüfen der Erfassung

Verwenden Sie kustoqueuedingestclient zum erfassen aus lokalen Dateien, und überprüfen Sie dann die Erfassung.

```csharp
// Create Kusto connection string with App Authentication
var kustoConnectionStringBuilderDM =
    new KustoConnectionStringBuilder(@"https://ingest-{clusterNameAndRegion}.kusto.windows.net").WithAadApplicationKeyAuthentication(
        applicationClientId: "{Application Client ID}",
        applicationKey: "{Application Key (secret)}",
        authority: "{AAD TenantID or name}");

// Create a disposable client that will execute the ingestion
IKustoQueuedIngestClient client = KustoIngestFactory.CreateQueuedIngestClient(kustoConnectionStringBuilderDM);

// Ingest from files according to the required properties
var kustoIngestionProperties = new KustoIngestionProperties(databaseName: "myDB", tableName: "myTable");

client.IngestFromStorageAsync(@"ValidTestFile.csv", kustoIngestionProperties);
client.IngestFromStorageAsync(@"InvalidTestFile.csv", kustoIngestionProperties);

// Waiting for the aggregation
Thread.Sleep(TimeSpan.FromMinutes(8));

// Retrieve and validate failures
var ingestionFailures = client.PeekTopIngestionFailuresAsync().GetAwaiter().GetResult();
Ensure.IsTrue((ingestionFailures.Count() > 0), "Failures expected");
// Retrieve, delete and validate failures
ingestionFailures = client.GetAndDiscardTopIngestionFailuresAsync().GetAwaiter().GetResult();
Ensure.IsTrue((ingestionFailures.Count() > 0), "Failures expected");

// Dispose of the client
client.Dispose();
```

### <a name="ingest-from-local-files-and-report-status-to-a-queue"></a>Erfassen von lokalen Dateien und melden des Status in eine Warteschlange

Verwenden Sie kustoqueuedingestclient, um lokale Dateien zu erfassen und den Status dann an eine Warteschlange zu melden.

```csharp
// Create Kusto connection string with App Authentication
var kustoConnectionStringBuilderDM =
    new KustoConnectionStringBuilder(@"https://ingest-{clusterNameAndRegion}.kusto.windows.net").WithAadApplicationKeyAuthentication(
        applicationClientId: "{Application Client ID}",
        applicationKey: "{Application Key (secret)}",
        authority: "{AAD TenantID or name}");

// Create a disposable client that will execute the ingestion
IKustoQueuedIngestClient client = KustoIngestFactory.CreateQueuedIngestClient(kustoConnectionStringBuilderDM);

// Ingest from a file according to the required properties
var kustoIngestionProperties = new KustoQueuedIngestionProperties(databaseName: "myDB", tableName: "myTable")
{
    // Setting the report level to FailuresAndSuccesses will cause both successful and failed ingestions to be reported
    // (Rather than the default "FailuresOnly" level - which is demonstrated in the
    // 'Ingest From Local File(s) using KustoQueuedIngestClient and Ingestion Validation' section)
    ReportLevel = IngestionReportLevel.FailuresAndSuccesses,
    // Choose the report method of choice. 'Queue' is the default method.
    // For the sake of the example, we will choose it anyway. 
    ReportMethod = IngestionReportMethod.Queue
};

client.IngestFromStorageAsync("ValidTestFile.csv", kustoIngestionProperties);
client.IngestFromStorageAsync("InvalidTestFile.csv", kustoIngestionProperties);

// Waiting for the aggregation
Thread.Sleep(TimeSpan.FromMinutes(8));

// Retrieve and validate failures
var ingestionFailures = client.PeekTopIngestionFailures().GetAwaiter().GetResult();
Ensure.IsTrue((ingestionFailures.Count() > 0), "The failed ingestion should have been reported to the failed ingestions queue");
// Retrieve, delete and validate failures
ingestionFailures = client.GetAndDiscardTopIngestionFailures().GetAwaiter().GetResult();
Ensure.IsTrue((ingestionFailures.Count() > 0), "The failed ingestion should have been reported to the failed ingestions queue");

// Verify the success has also been reported to the queue
var ingestionSuccesses = client.GetAndDiscardTopIngestionSuccesses().GetAwaiter().GetResult();
Ensure.ConditionIsMet((ingestionSuccesses.Count() > 0),
    "The successful ingestion should have been reported to the successful ingestions queue");

// Dispose of the client
client.Dispose();
```

### <a name="ingest-from-local-files-and-report-status-to-a-table"></a>Erfassen von lokalen Dateien und Berichts Status in einer Tabelle

Verwenden Sie kustoqueuedingestclient, um von lokalen Dateien zu erfassen und den Status einer Tabelle zu melden.

```csharp
// Create Kusto connection string with App Authentication
var kustoConnectionStringBuilderDM =
    new KustoConnectionStringBuilder(@"https://ingest-{clusterNameAndRegion}.kusto.windows.net").WithAadApplicationKeyAuthentication(
        applicationClientId: "{Application Client ID}",
        applicationKey: "{Application Key (secret)}",
        authority: "{AAD TenantID or name}");

// Create a disposable client that will execute the ingestion
IKustoQueuedIngestClient client = KustoIngestFactory.CreateQueuedIngestClient(kustoConnectionStringBuilderDM);

// Ingest from a file according to the required properties
var kustoIngestionProperties = new KustoQueuedIngestionProperties(databaseName: "myDB", tableName: "myDB")
{
    // Setting the report level to FailuresAndSuccesses will cause both successful and failed ingestions to be reported
    // (Rather than the default "FailuresOnly" level)
    ReportLevel = IngestionReportLevel.FailuresAndSuccesses,
    // Choose the report method of choice
    ReportMethod = IngestionReportMethod.Table
};

var filePath = @"< Path to file >";
var fileIdentifier = Guid.NewGuid();
var fileDescription = new FileDescription() { FilePath = filePath, SourceId = fileIdentifier };
var sourceOptions = new StorageSourceOptions() { SourceId = fileDescription.SourceId.Value };

// Execute the ingest operation and save the result.
var clientResult = await client.IngestFromStorageAsync(fileDescription.FilePath,
    ingestionProperties: kustoIngestionProperties, sourceOptions);

// Use the fileIdentifier you supplied to get the status of your ingestion 
var ingestionStatus = clientResult.GetIngestionStatusBySourceId(fileIdentifier);
while (ingestionStatus.Status == Status.Pending)
{
    // Wait a minute...
    Thread.Sleep(TimeSpan.FromMinutes(1));
    // Try again
    ingestionStatus = clientResult.GetIngestionStatusBySourceId(fileIdentifier);
}

// Verify the results of the ingestion
Ensure.ConditionIsMet(ingestionStatus.Status == Status.Succeeded,
    "The file should have been ingested successfully");

// Dispose of the client
client.Dispose();
```

## <a name="next-steps"></a>Nächste Schritte

* [Kusto. Erfassungs Client Verweis](kusto-ingest-client-reference.md)
* [Kusto. Erfassungs Vorgangs Status](kusto-ingest-client-errors.md)
* [Kusto. Erfassungs Ausnahmen](kusto-ingest-client-errors.md)
* [Kusto-Verbindungszeichenfolgen](../connection-strings/kusto.md)
* [Kusto-Autorisierungs Modell](../../management/security-roles.md)
