---
title: 'Kusto. Erfassungs Fehler & Ausnahmen: Azure Daten-Explorer'
description: In diesem Artikel werden Kusto. inerfassungs Fehler und Ausnahmen in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 97798fa62d588769636966c7155dc5f398bd001a
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382317"
---
# <a name="kustoingest-errors-and-exceptions"></a>Kusto. Erfassungs Fehler und Ausnahmen
Jeder Fehler während der Erfassungs Behandlung auf der Clientseite wird durch eine c#-Ausnahme angegeben.

## <a name="failures"></a>Fehler

### <a name="kustodirectingestclient-exceptions"></a>Kustodirectingestclient-Ausnahmen

Beim Versuch, aus mehreren Quellen zu erfassen, können während des Erfassungs Vorgangs Fehler auftreten. Wenn eine Erfassung für eine der Quellen fehlschlägt, wird Sie protokolliert, und der Client nimmt weiterhin die verbleibenden Quellen an. Nachdem Sie alle Quellen für die Erfassung überlaufen haben, wird eine ausgelöst `IngestClientAggregateException` , die den `IList<IngestClientException> IngestionErrors` Member enthält.

`IngestClientException`und die abgeleiteten Klassen enthalten ein Feld `IngestionSource` und ein `Error` Feld. Die beiden Felder erstellen eine Zuordnung von der Quelle, bei der ein Fehler aufgetreten ist, zu dem Fehler, der während der Erfassung aufgetreten ist. Die Informationen können in der Liste verwendet werden `IngestionErrors` , um zu ermitteln, bei welchen Quellen die Erfassung fehlgeschlagen ist und warum. Die `IngestClientAggregateException` Ausnahme enthält auch eine boolesche Eigenschaft `GlobalError` , die angibt, ob für alle Quellen ein Fehler aufgetreten ist.

### <a name="failures-ingesting-from-files-or-blobs"></a>Fehler beim Erfassen von Dateien oder blobdateien

Wenn beim Erfassen eines BLOBs oder einer Datei ein Erfassungs Fehler auftritt, werden die Erfassungs Quellen auch dann nicht gelöscht, wenn das- `deleteSourceOnSuccess` Flag auf festgelegt ist `true` . Die Quellen werden zur weiteren Analyse beibehalten. Wenn der Ursprung des Fehlers erkannt wird und der Fehler nicht von der Erfassungs Quelle selbst stammt, kann der Benutzer des Clients versuchen, ihn wiederherzustellen.

### <a name="failures-ingesting-from-idatareader"></a>Fehler beim Erfassen von IDataReader

Während der Erfassung aus DataReader werden die zu erfasbenden Daten in einem temporären Ordner gespeichert, dessen Standard Speicherort ist `<Temp Path>\Ingestions_<current date and time>` . Dieser Standardordner wird nach einer erfolgreichen Erfassung immer gelöscht.

In der `IngestFromDataReader` - `IngestFromDataReaderAsync` Methode und der-Methode bestimmt das- `retainCsvOnFailure` Flag, dessen Standardwert ist `false` ,, ob die Dateien nach einer fehlgeschlagenen Erfassung aufbewahrt werden sollen. Wenn dieses Flag auf festgelegt ist `false` , werden Daten, bei denen die Erfassung fehlschlägt, nicht persistent gespeichert, sodass es schwer zu verstehen ist, was schief gelaufen ist.

## <a name="kustoqueuedingestclient-exceptions"></a>Kustoqueuedingestclient-Ausnahmen

`KustoQueuedIngestClient`erfasst Daten, indem Nachrichten in eine Azure-Warteschlange hochgeladen werden. Wenn ein Fehler vor oder während des Warteschlangen Prozesses auftritt, `IngestClientAggregateException` wird am Ende des Prozesses eine ausgelöst. Die ausgelöste Ausnahme enthält eine Auflistung von `IngestClientException` , die die Quelle der einzelnen Fehler enthält und die nicht an die Warteschlange gesendet wurde. Der Fehler, der beim Versuch aufgetreten ist, die Nachricht zu veröffentlichen, wird ebenfalls ausgelöst.

### <a name="posting-to-queue-failures-with-a-file-or-blob-as-a-source"></a>Veröffentlichen in Warteschlangen Fehlern mit einer Datei oder einem BLOB als Quelle

Wenn ein Fehler auftritt, während die `KustoQueuedIngestClient` - `IngestFromFile/IngestFromBlob` Methoden verwendet werden, werden die Quellen nicht gelöscht, auch wenn das- `deleteSourceOnSuccess` Flag auf festgelegt ist `true` . Stattdessen werden die Quellen zur weiteren Analyse beibehalten. 

Wenn der Ursprung des Fehlers erkannt wird und der Fehler nicht von der Erfassungs Quelle selbst stammt, kann der Benutzer des Clients versuchen, die Daten mithilfe der relevanten `IngestFromFile/IngestFromBlob` Methoden mit der fehlgeschlagenen Quelle zu übermitteln. 

### <a name="posting-to-queue-failures-with-idatareader-as-a-source"></a>Veröffentlichen in Warteschlangen Fehlern mit IDataReader als Quelle

Wenn eine DataReader-Quelle verwendet wird, werden die Daten, die in die Warteschlange eingereiht werden, in einem temporären Ordner gespeichert, dessen Standard Speicherort ist `<Temp Path>\Ingestions_<current date and time>` . Dieser Ordner wird immer gelöscht, nachdem die Daten erfolgreich an die Warteschlange gesendet wurden.
In der `IngestFromDataReader` - `IngestFromDataReaderAsync` Methode und der-Methode bestimmt das- `retainCsvOnFailure` Flag, dessen Standardwert ist `false` ,, ob die Dateien nach einer fehlgeschlagenen Erfassung aufbewahrt werden sollen. Wenn dieses Flag auf festgelegt ist `false` , werden Daten, bei denen die Erfassung fehlschlägt, nicht persistent gespeichert, sodass es schwer zu verstehen ist, was schief gelaufen ist.

### <a name="common-failures"></a>Häufige Fehler
|Fehler                         |`Reason`           |Minderung                                   |
|------------------------------|-----------------|---------------------------------------------|
|Der Daten <database name> Bankname ist nicht vorhanden| Die Datenbank ist nicht vorhanden.|Überprüfen Sie den Datenbanknamen unter `kustoIngestionProperties` /Create the Database. |
|Die Entität "der Tabellenname, der nicht vorhanden ist" der Art "Table" wurde nicht gefunden.|Die Tabelle ist nicht vorhanden, und es gibt keine CSV-Zuordnung.| CSV-Zuordnung hinzufügen/erforderliche Tabelle erstellen |
|BLOB <blob path> ausgeschlossen aus Grund: das JSON-Muster muss mit dem jsonmapping-Parameter erfasst werden.| JSON-Erfassung, wenn keine JSON-Zuordnung bereitgestellt wird.|Bereitstellen einer JSON-Zuordnung |
|Fehler beim Herunterladen des BLOBs: "der Remote Server hat einen Fehler zurückgegeben: (404) nicht gefunden."| Das Blob ist nicht vorhanden.|Überprüfen Sie, ob das BLOB vorhanden ist. Falls vorhanden, versuchen Sie es erneut, und wenden Sie sich an das Kusto-Team |
|Die JSON-Spalten Zuordnung ist ungültig: zwei oder mehr Mapping-Elemente zeigen auf dieselbe Spalte.| Die JSON-Zuordnung verfügt über zwei Spalten mit unterschiedlichen Pfaden.|JSON-Zuordnung korrigieren |
|EngineError-[utilsexception] `IngestionDownloader.Download` : mindestens eine Datei konnte nicht heruntergeladen werden (suchen Sie nach "kustologs" nach "ActivityID:" <GUID1> , rootactivityid: <GUID2> ).| Mindestens eine Datei konnte nicht heruntergeladen werden. |Wiederholen |
|Fehler beim Analysieren: der Stream mit der ID " <stream name> " weist ein falsch formatiertes CSV-Format auf, das für die Richtlinie "validationoptions" nicht |Falsch formatierte CSV-Datei (z. b., nicht über die gleiche Anzahl von Spalten in jeder Zeile). Schlägt nur fehl, wenn die Validierungs Richtlinie auf festgelegt ist `ValidationOptions` . Validatecsvinputconstantcolumns |Überprüfen Sie die CSV-Dateien. Diese Meldung gilt nur für CSV/TSV-Dateien. |
|`IngestClientAggregateException`mit der Fehlermeldung "fehlende obligatorische Parameter für gültiges Shared Access Signature" |Die verwendete SAS ist der Dienst und nicht das Speicherkonto. |Verwenden Sie die SAS des Speicher Kontos. |

### <a name="ingestion-error-codes"></a>Erfassungs Fehlercodes

Um Erfassungs Fehler Programm gesteuert zu behandeln, werden Fehlerinformationen durch einen numerischen Fehlercode ( `IngestionErrorCode enumeration` ) erweitert.

|ErrorCode                                      |`Reason`                                                        |
|-----------------------------------------------|--------------------------------------------------------------|
|Unknown                                        | Unbekannter Fehler ist aufgetreten|
|Stream_LowMemoryCondition                      | Nicht genügend Arbeitsspeicher für den Vorgang.|
|Stream_WrongNumberOfFields                     | CSV-Dokument enthält inkonsistente Anzahl von Feldern|
|Stream_InputStreamTooLarge                     | Das für die Erfassung übermittelte Dokument hat die zulässige Größe überschritten.|
|Stream_NoDataToIngest                          | Es wurden keine Datenströme für die Erfassung gefunden.|
|Stream_DynamicPropertyBagTooLarge              | Eine der dynamischen Spalten in den erfassten Daten enthält zu viele eindeutige Eigenschaften.|
|Download_SourceNotFound                        | Fehler beim Herunterladen der Quelle von Azure Storage-Quelle nicht gefunden.|
|Download_AccessConditionNotSatisfied           | Fehler beim Herunterladen der Quelle aus Azure Storage: Zugriff verweigert.|
|Download_Forbidden                             | Fehler beim Herunterladen der Quelle aus Azure Storage: der Zugriff ist nicht zulässig.|
|Download_AccountNotFound                       | Fehler beim Herunterladen der Quelle von Azure Storage-das Konto wurde nicht gefunden.|
|Download_BadRequest                            | Fehler beim Herunterladen der Quelle aus Azure Storage-ungültige Anforderung.|
|Download_NotTransient                          | Fehler beim Herunterladen der Quelle aus Azure Storage: vorübergehender Fehler.|
|Download_UnknownError                          | Fehler beim Herunterladen der Quelle aus Azure Storage-unbekannter Fehler.|
|UpdatePolicy_QuerySchemaDoesNotMatchTableSchema| Fehler beim Aufrufen der Update Richtlinie. Das Abfrage Schema stimmt nicht mit dem Tabellen Schema.|
|UpdatePolicy_FailedDescendantTransaction       | Fehler beim Aufrufen der Update Richtlinie. Fehler bei der nachfolgenden Richtlinie für transaktionale Updates|
|UpdatePolicy_IngestionError                    | Fehler beim Aufrufen der Update Richtlinie. Erfassungs Fehler|
|UpdatePolicy_UnknownError                      | Fehler beim Aufrufen der Update Richtlinie. Unbekannter Fehler ist aufgetreten|
|BadRequest_MissingJsonMappingtFailure          | Das JSON-Muster konnte nicht mit dem jsonmapping-Parameter erfasst werden.|
|BadRequest_InvalidOrEmptyBlob                  | Das BLOB ist ungültig oder ein leeres ZIP-Archiv.|
|BadRequest_DatabaseNotExist                    | Die Datenbank ist nicht vorhanden|
|BadRequest_TableNotExist                       | Tabelle ist nicht vorhanden.|
|BadRequest_InvalidKustoIdentityToken           | Ungültiges Kusto-Identitäts Token.|
|BadRequest_UriMissingSas                       | BLOB-Pfad ohne SAS aus unbekanntem BLOB-Speicher|
|BadRequest_FileTooLarge                        | Es wird versucht, zu große Dateien zu erfassen.|
|BadRequest_NoValidResponseFromEngine           | Keine gültige Antwort vom Erfassungs Befehl.|
|BadRequest_TableAccessDenied                   | Der Zugriff auf die Tabelle wurde verweigert.|
|BadRequest_MessageExhausted                    | Nachricht ist erschöpft.|
|General_BadRequest                             | Allgemeine ungültige Anforderung. Hinweise zur Erfassung an eine nicht vorhandene Datenbank/Tabelle|
|General_InternalServerError                    | Interner Server Fehler.|

## <a name="detailed-exceptions-reference"></a>Ausführliche Ausnahmen Referenz

### <a name="cloudqueuesnotfoundexception"></a>Cloudqueuesnotfoundexception

Wird ausgelöst, wenn keine Warteschlangen vom Datenverwaltung Cluster zurückgegeben wurden.

Basisklasse: [Exception](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

|Feldname |Typ     |Bedeutung
|-----------|---------|------------------------------|
|Fehler      | String  | Der Fehler, der beim Versuch aufgetreten ist, Warteschlangen aus der DM abzurufen.
                            
Nur relevant, wenn der in der [Warteschlange](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)befindliche Erfassungs Client verwendet wird.
Während des Erfassungs Vorgangs werden mehrere Versuche unternommen, die mit der DM verknüpften Azure-Warteschlangen abzurufen. Wenn diese Versuche fehlschlagen, wird die Ausnahme, die den Grund für den Fehler enthält, im Feld "Error" ausgelöst. Möglicherweise wird auch eine innere Ausnahme im Feld "InnerException" ausgelöst.


### <a name="cloudblobcontainersnotfoundexception"></a>Cloudblobcontainersnotfoundexception

Wird ausgelöst, wenn keine BLOB-Container vom Datenverwaltung Cluster zurückgegeben wurden.

Basisklasse: [Exception](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

|Feldname   |Typ     |Bedeutung       
|-------------|---------|------------------------------|
|Kustoendpoint| String  | Der Endpunkt der relevanten DM
                            
Nur relevant, wenn der in der [Warteschlange](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)befindliche Erfassungs Client verwendet wird.  
Beim Erfassen von Quellen, die sich noch nicht in einem Azure-Container befinden (z. b. Dateien, DataReader oder Stream), werden die Daten zur Erfassung in ein temporäres BLOB hochgeladen. Diese Ausnahme wird ausgelöst, wenn keine Container gefunden werden, in die die Daten hochgeladen werden.

### <a name="duplicateingestionpropertyexception"></a>Dupliereingestionpropertyexception

Wird ausgelöst, wenn eine Erfassungs Eigenschaft mehrmals konfiguriert ist.

Basisklasse: [Exception](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

|Feldname   |Typ     |Bedeutung       
|-------------|---------|------------------------------------|
|PropertyName | String  | Der Name der doppelten Eigenschaft.
                            
### <a name="postmessagetoqueuefailedexception"></a>Postmessageto queuefailedexception

Wird ausgelöst, wenn eine Nachricht an die Warteschlange zurückgestellt wird.

Basisklasse: [Exception](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

|Feldname   |Typ     |Bedeutung       
|-------------|---------|---------------------------------|
|Queueuri     | String  | Der URI der Warteschlange.
|Fehler        | String  | Die Fehlermeldung, die generiert wurde, während versucht wurde, in die Warteschlange zu schreiben.
                            
Nur relevant, wenn der in der [Warteschlange](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)befindliche Erfassungs Client verwendet wird.  
Der in die Warteschlange eingereihte Erfassungs Client erfasst Daten, indem er eine Nachricht in die relevante Azure-Warteschlange hochlädt. Wenn ein Post-Fehler auftritt, wird die Ausnahme ausgelöst. Sie enthält den Warteschlangen-URI, den Grund für den Fehler im Feld "Error" und möglicherweise eine innere Ausnahme im Feld "InnerException".

### <a name="dataformatnotspecifiedexception"></a>Dataformatnotspecifiedexception

Wird ausgelöst, wenn ein Datenformat erforderlich ist, aber nicht in`IngestionProperties`

Basisklasse: ingestcliumtexception

Beim erfassen aus einem Stream muss in den [ingestionproperties](kusto-ingest-client-reference.md#class-kustoingestionproperties)ein Datenformat angegeben werden, damit die Daten ordnungsgemäß erfasst werden. Diese Ausnahme wird ausgelöst, wenn `IngestionProperties.Format` nicht angegeben ist.

### <a name="invaliduriingestclientexception"></a>Invaliduriingestcliumtexception

Wird ausgelöst, wenn ein ungültiger BLOB-URI als Erfassungs Quelle übermittelt wird.

Basisklasse: ingestcliumtexception

### <a name="compressfileingestclientexception"></a>Compressfileinstcliumtexception

Wird ausgelöst, wenn der Erfassungs Client die zur Erfassung bereitgestellte Datei nicht komprimieren kann.

Basisklasse: ingestcliumtexception

Dateien werden vor deren Erfassung komprimiert. Diese Ausnahme wird ausgelöst, wenn ein Versuch, die Datei zu komprimieren, fehlschlägt.

### <a name="uploadfiletotempblobingestclientexception"></a>Uploadfiledetempblobingestcliumtexception

Wird ausgelöst, wenn der Erfassungs Client die zur Erfassung bereitgestellte Quelle nicht in ein temporäres BLOB hochladen kann.

Basisklasse: ingestcliumtexception

### <a name="sizelimitexceededingestclientexception"></a>Sizelimitexceededingestclientexception

Wird ausgelöst, wenn eine Erfassungs Quelle zu groß ist.

Basisklasse: ingestcliumtexception

|Feldname   |Typ     |Bedeutung       
|-------------|---------|-----------------------|
|Größe         | long    | Die Größe der Erfassungs Quelle
|MaxSize      | long    | Die maximal zulässige Größe für die Erfassung.

Wenn eine Erfassungs Quelle die maximale Größe von 4 GB überschreitet, wird die Ausnahme ausgelöst. Die Größen Validierung kann durch das- `IgnoreSizeLimit` Flag in der [ingestionproperties-Klasse](kusto-ingest-client-reference.md#class-kustoingestionproperties)überschrieben werden. Es wird jedoch nicht empfohlen, [einzelne Quellen, die größer als 1 GB](about-kusto-ingest.md#ingestion-best-practices)sind, zu erfassen.

### <a name="uploadfiletotempblobingestclientexception"></a>Uploadfiledetempblobingestcliumtexception

Wird ausgelöst, wenn der Erfassungs Client die zur Erfassung bereitgestellte Datei nicht in ein temporäres BLOB hochladen kann.

Basisklasse: ingestcliumtexception

### <a name="directingestclientexception"></a>Directingestcliumtexception

Wird ausgelöst, wenn bei einer direkten Erfassung ein allgemeiner Fehler auftritt.

Basisklasse: ingestcliumtexception

### <a name="queuedingestclientexception"></a>Queuedingestcliumtexception

Wird ausgelöst, wenn ein Fehler beim erfassen in der Warteschlange auftritt.

Basisklasse: ingestcliumtexception

### <a name="ingestclientaggregateexception"></a>Ingestclientaggregateexception

Wird ausgelöst, wenn während einer Erfassung mindestens ein Fehler auftritt.

Basisklasse: [AggregateException](https://msdn.microsoft.com/library/system.aggregateexception(v=vs.110).aspx)

|Feldname      |Typ                             |Bedeutung       
|----------------|---------------------------------|-----------------------|
|Ingestionerrors | IList<IngestClientException>    | Die Fehler, die auftreten, wenn versucht wird, zu erfassen, und die damit verbundenen Quellen
|Isglobalerror   | bool                            | Gibt an, ob die Ausnahme für alle Quellen aufgetreten ist.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Fehlern in nativem Code finden Sie unter [Fehler in](../../concepts/errorsinnativecode.md)System eigenem Code.
