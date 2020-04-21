---
title: Kusto.Ingest-Referenz - Erfassungsstatusberichte - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird Kusto.Ingest Reference - Ingestion Status Reporting in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 1a3eed1db0ec7d3dd4bc83c0a65f342020b2a387
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523714"
---
# <a name="kustoingest-reference---ingestion-status-reporting"></a>Kusto.Ingest Reference - Aufnahmestatusberichte
In diesem Artikel wird erläutert, wie Sie [IKustoQueuedIngestClient-Funktionen](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) zum Nachverfolgen des Status einer Aufnahmeanforderung verwenden.

## <a name="sourcedescription-datareaderdescription-streamdescription-filedescription-and-blobdescription"></a>SourceDescription, DataReaderDescription, StreamDescription, FileDescription und BlobDescription
Diese verschiedenen Beschreibungsklassen enthalten wichtige Details zu den zu verführenden Quelldaten und können und sollten sogar für den Aufnahmevorgang bereitgestellt werden.
Alle diese Klassen werden von `SourceDescription` der abstrakten Klasse abgeleitet und zum Instanziieren eines eindeutigen Bezeichners für jede Datenquelle verwendet.
Der bereitgestellte Bezeichner ist für die nachfolgende Vorgangsstatusverfolgung vorgesehen und wird in allen Berichten, Ablaufverfolgungen und Ausnahmen im Zusammenhang mit dem entsprechenden Vorgang angezeigt.

### <a name="class-sourcedescription"></a>Klassen-SourceDescription
>Bei der Einnahme eines großen Datasets (z. B. eines DataReaders über 1 GB) werden die Daten in 1 GB-Blöcke aufgeteilt und separat aufgenommen.<BR>In diesem Fall gilt dieselbe SourceId für alle Aufnahmevorgänge, die aus demselben Dataset stammen.   

```csharp
public abstract class SourceDescription
{
    public Guid? SourceId { get; set; }
}
```

### <a name="class-datareaderdescription"></a>Klasse DataReaderDescription
```csharp
public class DataReaderDescription : SourceDescription
{
    public  IDataReader DataReader { get; set; }
}
```

### <a name="class-streamdescription"></a>Klasse StreamDescription
```csharp
public class StreamDescription : SourceDescription
{
    public Stream Stream { get; set; }
}
```

### <a name="class-filedescription"></a>KlassendateiBeschreibung
```csharp
public class FileDescription : SourceDescription
{
    public string FilePath { get; set; }
}
```

### <a name="class-blobdescription"></a>Klasse BlobDescription
```csharp
public class BlobDescription : SourceDescription
{
    public string BlobUri { get; set; }
    // Setting the Blob size here is important, as this saves the client the trouble of probing the blob for size
    public long? BlobSize { get; set; }
}
```

## <a name="ingestion-result-representation"></a>AufnahmeergebnisDarstellung

### <a name="interface-ikustoingestionresult"></a>Schnittstelle IKustoIngestionResult
Diese Schnittstelle erfasst das Ergebnis eines einzelnen Aufnahmevorgangs in der `SourceId`Warteschlange und ermöglicht deren Abruf durch .
```csharp
public interface IKustoIngestionResult
{
    // Retrieves the detailed ingestion status of the ingestion source with the given sourceId.
    IngestionStatus GetIngestionStatusBySourceId(Guid sourceId);

    // Retrieves the detailed ingestion status of all data ingestion operations into Kusto associated with this IKustoIngestionResult instance.
    IEnumerable<IngestionStatus> GetIngestionStatusCollection();
}
```

### <a name="class-ingestionstatus"></a>Klassenerfassungsstatus
IngestionStatus kapselt den vollständigen Status eines einzelnen Aufnahmevorgangs.
```csharp
public class IngestionStatus
{
    // The ingestion status returns from the service. Status remains 'Pending' during the ingestion process and
    // is updated by the service once the ingestion completes. When <see cref="IngestionReportMethod"/> is set to 'Queue' the ingestion status
    // will always be 'Queued' and the caller needs to query the report queues for ingestion status, as configured. To query statuses that were
    // reported to queue, see: <see href="https://docs.microsoft.com/azure/kusto/api/netfx/kusto-ingest-client-status#ingestion-status-in-azure-queue"/>.
    // When <see cref="IngestionReportMethod"/> is set to 'Table', call <see cref="IKustoIngestionResult.GetIngestionStatusBySourceId"/> or
    // <see cref="IKustoIngestionResult.GetIngestionStatusCollection"/> to retrieve the most recent ingestion status.
    public Status Status { get; set; }
    // A unique identifier representing the ingested source. Can be supplied during the ingestion execution.
    public Guid IngestionSourceId { get; set; }
    // The URI of the blob, potentially including the secret needed to access the blob.
    // This can be a filesystem URI (on-premises deployments only), or an Azure Blob Storage URI (including a SAS key or a semicolon followed by the account key)
    public string IngestionSourcePath { get; set; }
    // The name of the database holding the target table.
    public string Database { get; set; }
    // The name of the target table into which the data will be ingested.
    public string Table { get; set; }
    // The last updated time of the ingestion status.
    public DateTime UpdatedOn { get; set; }
    // The ingestion's operation Id.
    public Guid OperationId { get; set; }
    // The ingestion operation activity Id.
    public Guid ActivityId { get; set; }
    // In case of a failure - indicates the failure's error code.
    public IngestionErrorCode ErrorCode { get; set; }
    // In case of a failure - indicates the failure's status.
    public FailureStatus FailureStatus { get; set; }
    // In case of a failure - indicates the failure's details.
    public string Details { get; set; }
    // In case of a failure - indicates whether or not the failures originate from an Update Policy.
    public bool OriginatesFromUpdatePolicy { get; set; }
}
```

### <a name="status-enumeration"></a>Statusenumeration
|Wert |Bedeutung |
|------------|------------|
|Ausstehend |Temporäre. Kann sich im Laufe der Einnahme auf der Grundlage des Ergebnisses der Aufnahmeoperation ändern |
|Erfolgreich |permanent sind. die Daten wurden erfolgreich aufgenommen |
|Fehler |permanent sind. Verunstesung fehlgeschlagen |
|In Warteschlange |permanent sind. Die Daten wurden für die Aufnahme in die Warteschlange gestellt. |
|Ausgelassen |permanent sind. Es wurden keine Daten bereitgestellt, und der Aufnahmevorgang wurde übersprungen. |
|TeilweiseErfolgreich |permanent sind. Ein Teil der Daten wurde erfolgreich aufgenommen, während einige fehlgeschlagensind |

## <a name="tracking-ingestion-status-kustoqueuedingestclient"></a>Nachverfolgen des Erfassungsstatus (KustoQueuedIngestClient)
[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) ist ein "fire-and-forget"-Client - der Aufnahmevorgang auf der Clientseite endet mit dem Posten einer Nachricht an eine Azure-Warteschlange, nach der der Clientauftrag ausgeführt wird. KustoQueuedIngestClient bietet einen Mechanismus zum Nachverfolgen des individuellen Aufnahmestatus. Dies ist nicht für den Masseneinsatz an Durchsatz-Erfassungspipelines gedacht, sondern für die "Präzisionsaufnahme", wenn die Rate relativ niedrig ist und die Tracking-Anforderungen sehr streng sind.

> [!WARNING]
> Das Aktivieren positiver Benachrichtigungen für jede Aufnahmeanforderung für Datenströme mit großem Volumen sollte vermieden werden, da dies die zugrunde liegenden xStore-Ressourcen extrem belastet, > was zu einer erhöhten Aufnahmelatenz und sogar zur vollständigen Cluster-Nichtreaktion führen kann.


Die folgenden Eigenschaften (auf [KustoQueuedIngestionProperties](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties)festgelegt) steuern die Ebene und den Transport für Aufnahmeerfolgs- oder Fehlerbenachrichtigungen:

### <a name="ingestionreportlevel-enumeration"></a>IngestionReportLevel-Enumeration
```csharp
public enum IngestionReportLevel
{
    FailuresOnly = 0,
    None,
    FailuresAndSuccesses
}
```

### <a name="ingestionreportmethod-enumeration"></a>IngestionReportMethod-Enumeration
```csharp
public enum IngestionReportMethod
{
    Queue = 0,
    Table,
    QueueAndTable
}
```

Um den Status Ihrer Aufnahme nachverfolgen zu können, stellen Sie sicher, dass Sie dem [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) Folgendes zur Verfügung stellen, mit dem Sie den Aufnahmevorgang ausführen:
1.  Legen `IngestionReportLevel`Sie die Eigenschaft auf die gewünschte Berichtsebene fest – entweder FailuresOnly (der Standardwert) oder FailuresAndSuccesses.
Wenn auf Keine festgelegt, wird am Ende der Einnahme nichts gemeldet.
2.  Geben Sie `IngestionReportMethod` die gewünschte - Warteschlange, Tabelle oder beides an.

Ein Anwendungsbeispiel finden Sie auf der Seite [Kusto.Ingest Examples.](kusto-ingest-client-examples.md)

### <a name="ingestion-status-in-azure-table"></a>Erfassungsstatus in Azure-Tabelle
Die `IKustoIngestionResult` Schnittstelle, die von jedem Aufnahmevorgang zurückgegeben wird, enthält Funktionen, die zum Abfragen des Status der Aufnahme verwendet werden können.
Achten Sie besonders `Status` auf die `IngestionStatus` Eigenschaft der zurückgegebenen Objekte:
* `Pending`gibt an, dass die Quelle für die Aufnahme in die Warteschlange gestellt wurde und noch nicht aktualisiert werden muss. Verwenden Sie die Funktion erneut, um den Status der Quelle abzufragen
* `Succeeded`Gibt an, dass die Quelle erfolgreich aufgenommen wurde
* `Failed`Gibt an, dass die Quelle nicht aufgenommen wurde

>Beachten Sie, `Queued` dass das `IngestionReportMethod` Abrufen eines Status angibt, dass der Beim Standardwert "Warteschlange" belassen wurde. Dies ist ein permanenter Status, und das erneute Aufrufen der Funktionen GetIngestionStatusBySourceId oder GetIngestionStatusCollection führt immer zum gleichen "Queued"-Status.<BR>Um den Status einer Aufnahme in einer Azure-Tabelle überprüfen zu können, überprüfen `IngestionReportMethod` Sie vor der Aufnahme, ob die `Table` Eigenschaft `QueueAndTable` der [KustoQueuedIngestionProperties](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties) auf (oder wenn Sie auch möchten, dass der Aufnahmestatus an eine Warteschlange gemeldet wird) festgelegt ist.

### <a name="ingestion-status-in-azure-queue"></a>Erfassungsstatus in Azure-Warteschlange
Die `IKustoIngestionResult` Methoden sind nur für die Überprüfung eines Status in einer Azure-Tabelle relevant. Um Status abzufragen, die an eine Azure-Warteschlange gemeldet wurden, verwenden Sie die folgenden Methoden von [IKustoQueuedIngestClient:](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)

|Methode |Zweck |
|------------|------------|
|PeekTopIngestionFehler |Async-Methode, die Informationen zu den frühesten Aufnahmefehlern zurückgibt, die nicht verworfen wurden, entsprechend dem Limit für angeforderte Nachrichten |
|GetAndDiscardTopIngestionFailures |Async-Methode, die die frühesten Aufnahmefehler zurückgibt und verwirft, die nicht verworfen wurden, entsprechend dem Limit für angeforderte Nachrichten |
|GetAndDiscardTopIngestionSuccesses |Async-Methode, die die frühesten Aufnahmeerfolge zurückgibt und verwirft, die nicht verworfen wurden, `IngestionReportLevel` entsprechend dem Limit der angeforderten Nachrichten (nur relevant, wenn die auf`FailuresAndSuccesses` |


### <a name="ingestion-failures-retrieved-from-azure-queue"></a>Aus Azure-Warteschlange abgerufene Aufnahmefehler
Die Aufnahmefehler werden durch `IngestionFailure` ein Objekt dargestellt, das nützliche Informationen zum Fehler enthält:

|Eigenschaft |Bedeutung |
|------------|------------|
|Datenbank& Tabelle |Die beabsichtigten Datenbank- und Tabellennamen |
|IngestionSourcePath |Der Pfad des aufgenommenen Blobs. Will enthält den ursprünglichen Dateinamen im Falle der Dateiaufnahme. Wird zufällig im Falle der DataReader-Aufnahme sein |
|FailureStatus |`Permanent`(es wird kein Wiederholungsversuch ausgeführt), `Transient` (Wiederholung wird `Exhausted` ausgeführt) oder (mehrere Wiederholungen wurden ebenfalls fehl) |
|OperationId & RootActivityId |Betriebs-ID und RootActivity-ID der Aufnahme (nützlich für weitere Fehlerbehebung) |
|FailedOn |UTC-Zeit des Fehlers. Ist größer als der Zeitpunkt, zu dem die Aufnahmemethode aufgerufen wurde, da die Daten vor der Ausführung der Aufnahme aggregiert werden |
|Details |Weitere Einzelheiten zum Ausfall (falls vorhanden) |
|ErrorCode |`IngestionErrorCode`Enumeration, die den Aufnahmefehlercode darstellt, falls der Fehler vorhanden ist|