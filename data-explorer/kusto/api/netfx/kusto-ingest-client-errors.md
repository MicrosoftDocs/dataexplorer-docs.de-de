---
title: Kusto.Ingest-Referenz - Fehler und Ausnahmen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird Kusto.Ingest Reference - Errors and Exceptions in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: f8f50322a79dea8890b4a4ad5eaa78f0b8fe3bc0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81524343"
---
# <a name="kustoingest-reference---errors-and-exceptions"></a>Kusto.Ingest Reference - Fehler und Ausnahmen
Jeder Fehler während der Erfassungsbehandlung auf der Clientseite wird über eine C-Ausnahme für den Benutzercode verfügbar gemacht.

## <a name="failures-overview"></a>Fehlerübersicht

### <a name="kustodirectingestclient-exceptions"></a>KustoDirectIngestClient-Ausnahmen
Beim Versuch, aus mehreren Quellen zu erfassen, können Fehler während der Einnahme einiger dieser Quellen auftreten, während andere erfolgreich aufgenommen werden können. Wenn eine Aufnahme für eine bestimmte Quelle fehlschlägt, wird sie protokolliert, und der Client nimmt weiterhin die verbleibenden Quellen für die Aufnahme auf. Nachdem alle Quellen für die Einnahme `IngestClientAggregateException` übergegangen sind, `IList<IngestClientException> IngestionErrors`wird ein ausgelöst, das ein Member enthält.
`IngestClientException`und seine abgeleiteten `IngestionSource` Klassen `Error` enthalten ein Feld und ein Feld, die zusammen eine Zuordnung aus der Quelle bilden, die nicht zu dem Fehler aufgenommen wurde, der beim Versuch, ihn aufzunehmen, aufgetreten ist. Sie können die Informationen in der Liste IngestionErrors verwenden, um zu untersuchen, welche Quellen nicht aufgenommen wurden und warum. `IngestClientAggregateException`Ausnahme enthält auch eine `GlobalError`boolesche Eigenschaft , die angibt, ob ein Fehler für alle Quellen aufgetreten ist.

### <a name="failures-ingesting-from-files-or-blobs"></a>Fehler beim Einleiten von Dateien oder Blobs 
Wenn beim Versuch, die Aufnahme aus blob-datei zu erfassen, ein Aufnahmefehler aufgetreten ist, werden die Aufnahmequellen nicht gelöscht, auch wenn das `deleteSourceOnSuccess` Flag auf `true`festgelegt ist.
Die Quellen bleiben für die weitere Analyse erhalten. Sobald der Fehler der Ursache des Fehlers verstanden wurde und der Fehler nicht von der Aufnahmequelle selbst stammt, kann der Benutzer des Clients versuchen, ihn erneut aufzunehmen.

### <a name="failures-ingesting-from-idatareader"></a>Fehler beim Einsingen von IDataReader
Während der Aufnahme von DataReader werden die zu erfassenden Daten in `<Temp Path>\Ingestions_<current date and time>`einem temporären Ordner gespeichert, dessen Standardspeicherort ist. Dieser Ordner wird nach erfolgreicher Einnahme immer gelöscht.<BR>
In `IngestFromDataReader` der `IngestFromDataReaderAsync` und `retainCsvOnFailure` Methoden bestimmt das `false`Flag, dessen Standardwert ist , ob die Dateien nach einer fehlgeschlagenen Aufnahme beibehalten werden sollen. Wenn dieses Flag `false`auf festgelegt ist, werden Daten, die die Aufnahme fehlschlägt, nicht beibehalten, sodass es schwer zu verstehen ist, was schief gelaufen ist.

## <a name="kustoqueuedingestclient-exceptions"></a>KustoQueuedIngestClient-Ausnahmen
KustoQueuedIngestClient erfasst Daten, indem Nachrichten in eine Azure-Warteschlange hochgeladen werden. Wenn vor und während des Warteschlangenvorgangs ein Fehler auftritt, wird am Ende der Ausführung eine `IngestClientAggregateException` Auflistung `IngestClientException` ausgelöst, die die Quelle enthält, die nicht in der Warteschlange (bei jedem Fehler) und dem Fehler aufgetreten ist, der beim Senden der Nachricht aufgetreten ist.

### <a name="posting-to-queue-failures-with-file-or-blob-as-a-source"></a>Buchen in Warteschlangenfehler mit Datei oder Blob als Quelle
Wenn bei der Verwendung der IngestFromFile/IngestFromBlob-Methoden von KustoQueuedIngestClient ein Fehler `deleteSourceOnSuccess` aufgetreten ist, werden die Quellen nicht gelöscht, auch wenn das Flag auf `true`festgelegt ist, sondern für die weitere Analyse beibehalten. Nachdem der Benutzer des Clients den Ursprung des Fehlers erkannt hat und der Fehler nicht von der Quelle selbst stammt, kann er versuchen, die Daten mithilfe der entsprechenden IngestFromFile/IngestFromBlob-Methoden mit der fehlerhaften Quelle erneut in die Warteschlange zu stellen. 

### <a name="posting-to-queue-failures-with-idatareader-as-a-source"></a>Buchen von Warteschlangenfehlern mit IDataReader als Quelle
Bei Verwendung einer DataReader-Quelle werden die in der Warteschlange zu veröffentlichenden Daten in einem temporären Ordner gespeichert, dessen Standardspeicherort ist. `<Temp Path>\Ingestions_<current date and time>`
Dieser Ordner wird immer gelöscht, nachdem die Daten erfolgreich in die Warteschlange gesendet wurden.
In `IngestFromDataReader` der `IngestFromDataReaderAsync` und `retainCsvOnFailure` Methoden bestimmt das `false`Flag, dessen Standardwert ist , ob die Dateien nach einer fehlgeschlagenen Aufnahme beibehalten werden sollen. Wenn dieses Flag `false`auf festgelegt ist, werden Daten, die die Aufnahme fehlschlägt, nicht beibehalten, sodass es schwer zu verstehen ist, was schief gelaufen ist.

### <a name="common-failures"></a>Häufige Fehler
|Fehler|`Reason`|Minderung|
|------------------------------|----|------------|
|Datenbankname <database name> ist nicht vorhanden| Die Datenbank ist nicht vorhanden.|Überprüfen Sie den Datenbanknamen unter kustoIngestionProperties/Create the database |
|Der Entitätsname 'Tabellenname, der nicht vorhanden ist' der Art 'Tabelle' wurde nicht gefunden.|Die Tabelle ist nicht vorhanden, und es gibt keine CSV-Zuordnung.| CSV-Zuordnung hinzufügen / die erforderliche Tabelle erstellen |
|Blob <blob path> aus Gründen ausgeschlossen: json-Muster muss mit jsonMapping-Parameter aufgenommen werden| Json-Aufnahme, wenn keine json-Zuordnung bereitgestellt wird.|Bereitstellen einer JSON-Zuordnung |
|Fehler beim Herunterladen von Blob: 'Der Remoteserver hat einen Fehler zurückgegeben: (404) Nicht gefunden.'| Der Blob ist nicht vorhanden.|Überprüfen Sie, ob der Blob vorhanden ist, falls vorhanden, und wenden Sie sich an das Kusto-Team. |
|Die Json-Spaltenzuordnung ist ungültig: Zwei oder mehr Zuordnungselemente verweisen auf dieselbe Spalte.| JSON-Zuordnung hat 2 Spalten mit unterschiedlichen Pfaden|Korrektur der JSON-Zuordnung |
|EngineError - [UtilsException] IngestionDownloader.Download: Eine oder mehrere Dateien konnten nicht<GUID1>heruntergeladen werden (Suche KustoLogs nach ActivityID: , RootActivityId:<GUID2>)| Mindestens eine Datei konnte nicht heruntergeladen werden. |Wiederholen |
|Fehler beim Analysieren: Stream<stream name>mit id ' ' hat ein fehlerhaftes Csv-Format, das nach der ValidationOptions-Richtlinie fehlschlägt |Falsch formatierte csv-Datei (z. B. nicht die gleiche Anzahl von Spalten in jeder Zeile). Schlägt nur fehl, wenn die Validierungsrichtlinie auf ValidationOptions festgelegt ist. ValidateCsvInputConstantColumns |Überprüfen Sie Ihre csv-Dateien. Diese Meldung gilt nur für csv/tsv-Dateien |
|IngestClientAggregateException mit Fehlermeldung 'Fehlende obligatorische Parameter für gültige Shared Access Signature' |Die verwendete SAS ist vom Dienst und nicht vom Speicherkonto |Verwenden der SAS des Speicherkontos |

### <a name="ingestion-error-codes"></a>Aufnahmefehlercodes

Um Erfassungsfehler programmgesteuert zu behandeln, werden Fehlerinformationen mit einem numerischen Fehlercode (IngestionErrorCode-Enumeration) angereichert.

|ErrorCode|`Reason`|
|-----------|-------|
|Unknown| Unbekannter Fehler ist aufgetreten|
|Stream_LowMemoryCondition| Der Vorgang war nicht mehr speicherverfügbar|
|Stream_WrongNumberOfFields| CSV-Dokument weist eine inkonsistente Anzahl von Feldern auf|
|Stream_InputStreamTooLarge| Das zur Aufnahme übermittelte Dokument hat die zulässige Größe überschritten.|
|Stream_NoDataToIngest| Es wurden keine zu erfassenden Datenströme gefunden.|
|Stream_DynamicPropertyBagTooLarge| Eine der dynamischen Spalten in den aufgenommenen Daten enthält zu viele eindeutige Eigenschaften|
|Download_SourceNotFound| Fehler beim Herunterladen der Quelle aus Azure-Speicher - Quelle wurde nicht gefunden|
|Download_AccessConditionNotSatisfied| Fehler beim Herunterladen der Quelle aus Azure-Speicher - Zugriff verweigert|
|Download_Forbidden| Fehler beim Herunterladen der Quelle aus Azure-Speicher - Zugriff verboten|
|Download_AccountNotFound| Fehler beim Herunterladen der Quelle aus Azure-Speicher - Konto wurde nicht gefunden|
|Download_BadRequest| Fehler beim Herunterladen der Quelle aus Azure-Speicher - ungültige Anforderung|
|Download_NotTransient| Fehler beim Herunterladen der Quelle aus Azure-Speicher - kein vorübergehender Fehler|
|Download_UnknownError| Fehler beim Herunterladen der Quelle aus Azure-Speicher - unbekannter Fehler|
|UpdatePolicy_QuerySchemaDoesNotMatchTableSchema| Fehler beim Aufrufen der Aktualisierungsrichtlinie. Abfrageschema stimmt nicht mit Tabellenschema überein|
|UpdatePolicy_FailedDescendantTransaction| Fehler beim Aufrufen der Aktualisierungsrichtlinie. Fehlgeschlagene abhängige Transaktionsaktualisierungsrichtlinie|
|UpdatePolicy_IngestionError| Fehler beim Aufrufen der Aktualisierungsrichtlinie. Aufnahmefehler aufgetreten|
|UpdatePolicy_UnknownError| Fehler beim Aufrufen der Aktualisierungsrichtlinie. Unbekannter Fehler ist aufgetreten|
|BadRequest_MissingJsonMappingtFailure| Json-Muster wurde nicht mit jsonMapping-Parameter aufgenommen|
|BadRequest_InvalidOrEmptyBlob| Blob ist ungültig oder leeres ZIP-Archiv|
|BadRequest_DatabaseNotExist| Die Datenbank ist nicht vorhanden.|
|BadRequest_TableNotExist| Tabelle ist nicht vorhanden|
|BadRequest_InvalidKustoIdentityToken| Ungültiges kusto-Identitätstoken|
|BadRequest_UriMissingSas| Blobpfad ohne SAS aus unbekanntem Blobspeicher|
|BadRequest_FileTooLarge| Es wird versucht, zu große Dateien aufzunehmen|
|BadRequest_NoValidResponseFromEngine| Keine gültige Antwort vom Ingest-Befehl|
|BadRequest_TableAccessDenied| Der Zugriff auf die Tabelle wird verweigert|
|BadRequest_MessageExhausted| Nachricht ist erschöpft|
|General_BadRequest| Allgemeine fehlerhafte Anforderung (kann Aufhinweise für die Aufnahme in nicht vorhandene Datenbank/Tabelle)|
|General_InternalServerError| Interner Serverfehler aufgetreten|

## <a name="detailed-kustoingest-exceptions-reference"></a>Detaillierte Kusto.Ingest Exceptions Referenz

### <a name="cloudqueuesnotfoundexception"></a>CloudQueuesNotFoundException
Wird ausgelöst, wenn keine Warteschlangen aus dem Datenverwaltungscluster zurückgegeben wurden

Basisklasse: [Ausnahme](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

Felder:

|Name|type|Bedeutung
|-----------|----|------------------------------|
|Fehler| `String`| Der Fehler, der beim Versuch aufgetreten ist, Warteschlangen von der DM abzurufen
                            
Zusätzliche Informationen:

Nur bei Verwendung des [Kusto Queued Ingest Client](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)relevant.
Während des Aufnahmevorgangs werden mehrere Versuche unternommen, die mit der DM verknüpften Azure-Warteschlangen abzurufen. Wenn diese Versuche fehlschlagen, wird die Ausnahme ausgelöst, die den Grund für den Fehler im Feld 'Fehler' und möglicherweise eine innere Ausnahme im Feld 'InnerException' enthält.


### <a name="cloudblobcontainersnotfoundexception"></a>CloudBlobContainersNotFoundException
Wird ausgelöst, wenn keine Blobcontainer aus dem Datenverwaltungscluster zurückgegeben wurden

Basisklasse: [Ausnahme](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

Felder:

|Name|type|Bedeutung       
|-----------|----|------------------------------|
|KustoEndpoint| `String`| Der Endpunkt der jeweiligen DM
                            
Zusätzliche Informationen:

Nur bei Verwendung des [Kusto Queued Ingest Client](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)relevant.  
Beim Einziehen von Quellen, die sich NICHT bereits in einem Azure-Container befinden, d. h. Dateien, DataReader oder Stream, werden die Daten zur Aufnahme in ein temporäres Blob hochgeladen. Die Ausnahme wird ausgelöst, wenn keine Container zum Hochladen der Daten gefunden werden.

### <a name="duplicateingestionpropertyexception"></a>DuplicateIngestionPropertyException
Wird ausgelöst, wenn eine Aufnahmeeigenschaft mehr als einmal konfiguriert wird

Basisklasse: [Ausnahme](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

Felder:

|Name|type|Bedeutung       
|-----------|----|------------------------------|
|PropertyName| `String`| Der Name der duplizierten Eigenschaft
                            
### <a name="postmessagetoqueuefailedexception"></a>PostMessageToQueueFailedException
Ausgelöst, wenn das Senden einer Nachricht in der Warteschlange fehlgeschlagen ist

Basisklasse: [Ausnahme](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

Felder:

|Name|type|Bedeutung       
|-----------|----|------------------------------|
|QueueUri| `String`| Der URI der Warteschlange
|Fehler| `String`| Die Fehlermeldung, die beim Senden in der Warteschlange generiert wurde
                            
Zusätzliche Informationen:

Nur bei Verwendung des [Kusto Queued Ingest Client](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)relevant.  
Der in der Warteschlange eingesende Client erfasst Daten, indem er eine Nachricht in die entsprechende Azure-Warteschlange hochlädt. Im Falle eines Postfehlers wird die Ausnahme ausgelöst, die den Warteschlangen-URI, den Grund für den Fehler im Feld 'Fehler' und möglicherweise eine innere Ausnahme im Feld 'InnerException' enthält.

### <a name="dataformatnotspecifiedexception"></a>DataFormatNotSpecifiedException
Wird ausgelöst, wenn ein Datenformat erforderlich ist, aber nicht in IngestionProperties angegeben ist

Basisklasse: IngestClientException

Zusätzliche Informationen:

Beim Einleiten aus einem Stream muss in den [IngestionProperties](kusto-ingest-client-reference.md#class-kustoingestionproperties) ein Datenformat angegeben werden, um die Daten ordnungsgemäß aufnehmen zu können. Diese Ausnahme wird ausgelöst, wenn das IngestionProperties.Format nicht angegeben ist.

### <a name="invaliduriingestclientexception"></a>InvalidUriIngestClientException
Wird ausgelöst, wenn ein ungültiger Blob-URI als Aufnahmequelle übermittelt wurde

Basisklasse: IngestClientException

### <a name="compressfileingestclientexception"></a>CompressFileIngestClientException
Wird ausgelöst, wenn der Aufnahmeclient die für die Aufnahme bereitgestellte Datei nicht komprimieren konnte.

Basisklasse: IngestClientException

Zusätzliche Informationen:

Dateien werden vor ihrer Einnahme komprimiert. Diese Ausnahme wird ausgelöst, wenn der Versuch, die Datei zu komprimieren, fehlgeschlagen ist.

### <a name="uploadfiletotempblobingestclientexception"></a>UploadFileToTempBlobIngestClientException
Wird ausgelöst, wenn der Aufnahmeclient die für die Aufnahme bereitgestellte Quelle nicht in ein temporäres Blob hochladen konnte.

Basisklasse: IngestClientException

### <a name="sizelimitexceededingestclientexception"></a>SizeLimitExceededIngestClientException
Erhöht, wenn eine Aufnahmequelle zu groß ist

Basisklasse: IngestClientException

Felder:

|Name|type|Bedeutung       
|-----------|----|------------------------------|
|Size| `long`| Die Größe der Aufnahmequelle
|MaxSize| `long`| Die maximal zulässige Größe für die Einnahme

Zusätzliche Informationen:

Wenn eine Aufnahmequelle die maximale Größe von 4 GB überschreitet, wird die Ausnahme ausgelöst. Die Größenüberprüfung kann durch das IgnoreSizeLimit-Flag in der [IngestionProperties-Klasse](kusto-ingest-client-reference.md#class-kustoingestionproperties)überschrieben werden, es wird jedoch nicht empfohlen, [einzelne Quellen zu erfassen,](about-kusto-ingest.md#ingestion-best-practices)die größer als 1 GB sind.

### <a name="uploadfiletotempblobingestclientexception"></a>UploadFileToTempBlobIngestClientException
Wird ausgelöst, wenn der Aufnahmeclient die für die Aufnahme bereitgestellte Datei nicht in ein temporäres Blob hochladen konnte.

Basisklasse: IngestClientException

### <a name="directingestclientexception"></a>DirectIngestClientException
Wird ausgelöst, wenn ein allgemeiner Fehler bei der Direkten Aufnahme aufgetreten ist

Basisklasse: IngestClientException

### <a name="queuedingestclientexception"></a>QueuedIngestClientException
Ausgelöst, wenn beim Ausführen einer Aufnahme in der Warteschlange ein Fehler aufgetreten ist

Basisklasse: IngestClientException

### <a name="ingestclientaggregateexception"></a>IngestClientAggregateException
Wird ausgelöst, wenn ein oder mehrere Fehler während einer Einnahme aufgetreten sind

Basisklasse: [AggregateException](https://msdn.microsoft.com/library/system.aggregateexception(v=vs.110).aspx)

Felder:

|Name|type|Bedeutung       
|-----------|----|------------------------------|
|IngestionErrors| `IList<IngestClientException>`| Die Fehler, die beim Versuch, zu erfassen aufgetreten sind, und die damit verbundenen Quellen
|IsGlobalError| `bool`| Gibt an, ob die Ausnahme für alle Quellen aufgetreten ist

## <a name="errors-in-native-code"></a>Fehler in nativem Code
Die Kusto-Engine ist in systemeigenem Code geschrieben. Weitere Informationen zu Fehlern im systemeigenen Code finden Sie unter [Fehler im systemeigenen Code](../../concepts/errorsinnativecode.md)