---
title: Kusto. Erfassungs Statusberichte-Azure-Daten-Explorer
description: Dieser Artikel beschreibt die Erfassung von Erfassungs Statusberichten in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 41c6f7868d5300dc6a7234ee774ad8c78b08c36e
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382181"
---
# <a name="kustoingest-ingestion-status-reporting"></a>Kusto. Erfassung der Erfassungs Statusberichte

In diesem Artikel wird erläutert, wie Sie die [ikustoqueuedingestclient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) -Features verwenden, um den Status einer Erfassungs Anforderung zu verfolgen.

## <a name="description-classes"></a>Beschreibungs Klassen

Diese Beschreibungs Klassen enthalten wichtige Details zu den zu erfassenden Quelldaten und sollten beim Erfassungs Vorgang verwendet werden. 

* SourceDescription
* Datareaderdescription
* Streamdescription
* FileDescription
* Blobdescription

Die Klassen werden alle von der abstrakten-Klasse abgeleitet `SourceDescription` und zum Instanziieren eines eindeutigen Bezeichners für jede Datenquelle verwendet. Jeder Bezeichner wird dann für die Status Nachverfolgung verwendet und in allen Berichten, Ablauf Verfolgungen und Ausnahmen angezeigt, die mit dem entsprechenden Vorgang in Zusammenhang stehen.

### <a name="class-sourcedescription"></a>Klasse sourcedescription

Große Datasets werden in 1-GB-Segmente aufgeteilt, und jeder Teil wird separat erfasst. Dieselbe SourceID wird dann auf alle Erfassungs Vorgänge angewendet, die aus demselben Dataset stammen.   

```csharp
public abstract class SourceDescription
{
    public Guid? SourceId { get; set; }
}
```

### <a name="class-datareaderdescription"></a>Class datareaderdescription

```csharp
public class DataReaderDescription : SourceDescription
{
    public  IDataReader DataReader { get; set; }
}
```

### <a name="class-streamdescription"></a>Class streamdescription

```csharp
public class StreamDescription : SourceDescription
{
    public Stream Stream { get; set; }
}
```

### <a name="class-filedescription"></a>Class File Description

```csharp
public class FileDescription : SourceDescription
{
    public string FilePath { get; set; }
}
```

### <a name="class-blobdescription"></a>Klassenblobbeschreibung

```csharp
public class BlobDescription : SourceDescription
{
    public string BlobUri { get; set; }
    // Setting the Blob size here is important, as this saves the client the trouble of probing the blob for size
    public long? BlobSize { get; set; }
}
```

## <a name="ingestion-result-representation"></a>Darstellung der Erfassungs Ergebnisse

### <a name="interface-ikustoingestionresult"></a>Ikustoingestionresult-Schnittstelle

Diese Schnittstelle erfasst das Ergebnis eines einzelnen Erfassungs Vorgangs in der Warteschlange und kann von abgerufen werden `SourceId` .

```csharp
public interface IKustoIngestionResult
{
    // Retrieves the detailed ingestion status of the ingestion source with the given sourceId.
    IngestionStatus GetIngestionStatusBySourceId(Guid sourceId);

    // Retrieves the detailed ingestion status of all data ingestion operations into Kusto associated with this IKustoIngestionResult instance.
    IEnumerable<IngestionStatus> GetIngestionStatusCollection();
}
```

### <a name="class-ingestionstatus"></a>Class ingestionstatus

Ingestionstatus enthält den gesamten Status eines einzelnen Erfassungs Vorgangs.

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

|Wert              |Bedeutung                                                                                     |Temporär/permanent
|-------------------|-----------------------------------------------------------------------------------------------------|---------|
|Pending            |Der Wert ändert sich möglicherweise während der Erfassung, basierend auf dem Ergebnis der Erfassungs Operation. |Temporäre Prozeduren|
|Erfolgreich          |Die Daten wurden erfolgreich erfasst.                                                              |Dauerhaft| 
|Fehler             |Fehler bei der Erfassung                                                                                     |Dauerhaft|
|In Warteschlange             |Die Daten wurden zur Erfassung in die Warteschlange eingereiht.                                                               |Dauerhaft|
|Ausgelassen            |Es wurden keine Daten bereitgestellt, und der Erfassungs Vorgang wurde übersprungen.                                            |Dauerhaft|
|"Partiallysucceeded" |Ein Teil der Daten wurde erfolgreich erfasst, während einige fehlgeschlagen sind.                                        |Dauerhaft|

## <a name="tracking-ingestion-status-kustoqueuedingestclient"></a>Status der Überwachungs Erfassung (kustoqueuedingestclient)

[Ikustoqueuedingestclient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) ist ein "Fire-and-Forget"-Client. Der Erfassungs Vorgang auf der Clientseite wird beendet, indem eine Nachricht an eine Azure-Warteschlange übermitteln wird. Nach der Bereitstellung wird der Client Auftrag abgeschlossen. Zur Unterstützung des Client Benutzers bietet kustoqueuedingestclient einen Mechanismus zum Überwachen des individuellen Erfassungs Status. Dieser Mechanismus ist nicht für die Massen Verwendung bei Erfassungs Pipelines mit hohem Durchsatz vorgesehen. Dieser Mechanismus dient der Genauigkeits Erfassung, wenn die Rate relativ niedrig ist und die nach Verfolgungs Anforderungen streng sind.

> [!WARNING]
> Das Aktivieren von positiven Benachrichtigungen für jede Erfassungs Anforderung für große volumedatenströme sollte vermieden werden, da dadurch die zugrunde liegenden XStore-Ressourcen extrem hochgeladen werden können, was zu einer längeren Erfassungs Latenz und sogar zum Vervollständigen der nicht Reaktionsfähigkeit von Clustern führen kann.

Die folgenden Eigenschaften (festgelegt auf [kustoqueuedingestionproperties](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties)) steuern die Ebene und den Transport für erfolgreiche Erfassungs-oder Fehler Benachrichtigungen.

### <a name="ingestionreportlevel-enumeration"></a>Ingestionreportlevel-Enumeration

```csharp
public enum IngestionReportLevel
{
    FailuresOnly = 0,
    None,
    FailuresAndSuccesses
}
```

### <a name="ingestionreportmethod-enumeration"></a>Ingestionreportmethod-Enumeration

```csharp
public enum IngestionReportMethod
{
    Queue = 0,
    Table,
    QueueAndTable
}
```

Um den Status ihrer Erfassung zu verfolgen, stellen Sie Folgendes für [ikustoqueuedingestclient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) bereit, mit dem Sie den Erfassungs Vorgang ausführen:
1.  Legen `IngestionReportLevel` Sie die Eigenschaft auf die erforderliche Berichts Ebene fest. Entweder `FailuresOnly` (der Standardwert) oder `FailuresAndSuccesses` .
Wenn diese Einstellung auf festgelegt `None` ist, wird am Ende der Erfassung nichts gemeldet.
1.  Geben Sie `IngestionReportMethod`  -  `Queue` , `Table` oder an `both` .

Ein Verwendungs Beispiel finden Sie auf der Seite " [Kusto.](kusto-ingest-client-examples.md) Erfassungs Beispiele".

### <a name="ingestion-status-in-the-azure-table"></a>Erfassungs Status in der Azure-Tabelle

Die `IKustoIngestionResult` Schnittstelle, die von jedem Erfassungs Vorgang zurückgegeben wird, enthält Funktionen, die verwendet werden können, um den Status der Erfassung abzufragen.
Achten Sie besonders auf die- `Status` Eigenschaft der zurückgegebenen- `IngestionStatus` Objekte:
* `Pending`Gibt an, dass die Quelle zur Erfassung in die Warteschlange eingereiht wurde und noch nicht aktualisiert werden muss. Verwenden Sie die Funktion erneut, um den Status der Quelle abzufragen.
* `Succeeded`Gibt an, dass die Quelle erfolgreich erfasst wurde.
* `Failed`Gibt an, dass die Quelle nicht erfasst werden konnte.

> [!NOTE]
> Das erhalten eines `Queued` Status gibt an, dass der `IngestionReportMethod` Standardwert von "Queue" beibehalten wurde. Dies ist ein dauerhafter Status und der erneute Aufruf der- `GetIngestionStatusBySourceId` Funktion oder der- `GetIngestionStatusCollection` Funktion führt immer zu demselben Status in der Warteschlange.
> Überprüfen Sie, ob die- `IngestionReportMethod` Eigenschaft von [kustoqueuedingestionproperties](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties) auf festgelegt ist, um den Status einer Erfassung in einer Azure-Tabelle zu überprüfen `Table` . Wenn Sie auch den Erfassungs Status an eine Warteschlange melden möchten, legen Sie den Status auf fest `QueueAndTable` .

### <a name="ingestion-status-in-azure-queue"></a>Erfassungs Status in der Azure-Warteschlange

Die `IKustoIngestionResult` Methoden sind nur für das Überprüfen eines Status in einer Azure-Tabelle relevant. Verwenden Sie zum Abfragen von Statusberichten, die an eine Azure-Warteschlange gesendet wurden, die folgenden [ikustoqueuedingestclient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) -Methoden.

|Methode                                  |Zweck     |
|----------------------------------------|------------|
|"Peer Failure"-Fehler                |Async-Methode, die Informationen zu den frühesten Erfassungs Fehlern zurückgibt, die aufgrund des Limits für angeforderte Nachrichten nicht bereits verworfen wurden |
|Getandverwerdtopingestionfailure       |Async-Methode, die die frühesten Erfassungs Fehler zurückgibt und verwirft, die aufgrund des Limits für angeforderte Nachrichten nicht bereits verworfen wurden. |
|Getandverwerdtopingestionerfolge      |Async-Methode, die die frühesten Erfassungs Erfolge zurückgibt und verwirft, die aufgrund des Limits für angeforderte Nachrichten nicht bereits verworfen wurden. Diese Methode ist nur relevant, wenn `IngestionReportLevel` auf festgelegt ist.`FailuresAndSuccesses` |

### <a name="ingestion-failures-retrieved-from-the-azure-queue"></a>Von der Azure-Warteschlange abgerufene Erfassungs Fehler

Die Erfassungs Fehler werden durch das- `IngestionFailure` Objekt dargestellt, das nützliche Informationen über den Fehler enthält.

|Eigenschaft                      |Bedeutung     |
|------------------------------|------------|
|Daten Bank & Tabelle              |Die gewünschte Datenbank und die Tabellennamen |
|Ingestionsourcepath           |Der Pfad des erfassten BLOBs. Enthält den ursprünglichen Dateinamen, wenn die Datei erfasst wird. Wird zufällig, wenn DataReader erfasst wird |
|Failurestatus                 |`Permanent`(es werden keine Wiederholungen ausgeführt), `Transient` (Wiederholungs Versuche werden ausgeführt) oder `Exhausted` (mehrere Wiederholungen sind ebenfalls fehlgeschlagen). |
|OperationId & rootactivityid  |Vorgangs-ID und RootActivity-ID der Erfassung (nützlich für die weitere Problembehandlung) |
|Failedon                      |Die UTC-Zeit des Fehlers. Ist größer als die Zeit, zu der die Erfassungs Methode aufgerufen wurde, da die Daten vor dem Ausführen der Erfassung aggregiert werden. |
|Details                       |Weitere Details zu dem Fehler (falls vorhanden) |
|ErrorCode                     |`IngestionErrorCode`Enumeration stellt den Erfassungs Fehlercode dar, wenn ein Fehler aufgetreten ist. |
