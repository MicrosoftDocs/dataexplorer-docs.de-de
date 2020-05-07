---
title: Datenerfassung durch howto ohne Kusto. Erfassungs Bibliothek-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird die Beschreibung der Datenerfassung ohne Kusto. Erfassungs Bibliothek in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 02/19/2020
ms.openlocfilehash: 2ea7fd33a6e6ed8728fb12d53fbe76eadf8fd6b6
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82862070"
---
# <a name="howto-data-ingestion-without-kustoingest-library"></a>Datenerfassung durch howto ohne Kusto. Erfassungs Bibliothek

## <a name="when-to-consider-not-using-kustoingest-library"></a>Wann sollten Sie in Erwägung gezogen werden, die Kusto. Erfassungs Bibliothek zu verwenden?
Im Allgemeinen sollte die Verwendung der Kusto. Erfassungs Bibliothek bevorzugt werden, wenn das Erfassen von Daten in Kusto berücksichtigt wird.<BR>
Wenn dies nicht möglich ist (in der Regel aufgrund von Betriebssystem Einschränkungen), kann mit einem gewissen Aufwand fast die gleiche Funktionalität erreicht werden.<BR>
In diesem Artikel wird beschrieben, wie Sie die Erfassung von Warteschlangen in Kusto implementieren, ohne dass eine Abhängigkeit vom Kusto. **Ingestion** -Paket besteht.

>**Hinweis:** Der folgende Code ist in c# geschrieben und nutzt Azure Storage SDK, Adal Authentication Library und das newtonsoft. JSON-Paket, um den Beispielcode zu vereinfachen.<BR>Bei Bedarf kann der entsprechende Code durch entsprechende [Azure Storage Rest-API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) -Aufrufe, [Non-.net Adal-Paket](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) und beliebiges verfügbares JSON-Verarbeitungs Paket ersetzt werden.

## <a name="overview"></a>Übersicht
Im folgenden Codebeispiel wird die Datenerfassung in der Warteschlange (über die Kusto-Datenverwaltung Dienst) in Kusto ohne Verwendung der Kusto. Erfassungs Bibliothek veranschaulicht.<BR>
Dies kann hilfreich sein, wenn auf vollständiges .net aufgrund der Umgebung oder anderer Einschränkungen nicht zugegriffen werden kann oder nicht verfügbar ist.<BR>

> In diesem Artikel wird der empfohlene Modus für die Erfassung von Pipelines auf Produktionsqualität behandelt. Dies wird auch als Erfassung in der **Warteschlange** bezeichnet (in Bezug auf die Kusto. Erfassungs Bibliothek ist die zugehörige Entität die [ikustoqueuedingestclient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) -Schnittstelle). In diesem Modus interagiert der Client Code mit dem Kusto-Dienst, indem er Erfassungs Benachrichtigungs Meldungen an eine Azure-Warteschlange bereitstellt, auf die vom Kusto-Datenverwaltung (auch bekannt als Erfassungs Dienst. Die Interaktion mit dem Datenverwaltung-Dienst muss mit **Aad**authentifiziert werden.

Der Umriss dieses Flows wird im folgenden Codebeispiel beschrieben und besteht aus folgendem:
1. Erstellen eines Azure Storage Clients und Hochladen der Daten in ein BLOB
1. Authentifizierungs Token für den Zugriff auf den Kusto-Erfassungs Dienst abrufen
2. Fragen Sie den Kusto-Erfassungs Dienst ab, um Folgendes zu erhalten:
    * Erfassungs Ressourcen (Warteschlangen und BLOB-Container)
    * Kusto-Identitäts Token, das jeder Erfassungs Nachricht hinzugefügt wird
3. Hochladen von Daten in ein BLOB in einem der BLOB-Container, die aus Kusto abgerufen wurden in (2)
4. Erstellen Sie eine Erfassungs Nachricht, die die Zieldatenbank und-Tabelle identifiziert und auf das BLOB verweist (3).
5. Stellen Sie die Erfassungs Nachricht, die wir in (4) erstellt haben, zu einer der Erfassungs Warteschlangen bereit, die in Kusto abgerufen wurden (2)
6. Abrufen aller Fehler, die vom Dienst während der Erfassung aufgetreten sind

In den nachfolgenden Abschnitten werden die einzelnen Schritte ausführlicher erläutert.

```csharp
// A container class for ingestion resources we are going to obtain from Kusto
internal class IngestionResourcesSnapshot
{
    public IList<string> IngestionQueues { get; set; } = new List<string>();
    public IList<string> TempStorageContainers { get; set; } = new List<string>();

    public string FailureNotificationsQueue { get; set; } = string.Empty;
    public string SuccessNotificationsQueue { get; set; } = string.Empty;
}

public static void IngestSingleFile(string file, string db, string table, string ingestionMappingRef)
{
    // Your Kusto ingestion service URI, typically ingest-<your cluster name>.kusto.windows.net
    string DmServiceBaseUri = @"https://ingest-{serviceNameAndRegion}.kusto.windows.net";

    // 1. Authenticate the interactive user (or application) to access Kusto ingestion service
    string bearerToken = AuthenticateInteractiveUser(DmServiceBaseUri);

    // 2a. Retrieve ingestion resources
    IngestionResourcesSnapshot ingestionResources = RetrieveIngestionResources(DmServiceBaseUri, bearerToken);

    // 2b. Retrieve Kusto identity token
    string identityToken = RetrieveKustoIdentityToken(DmServiceBaseUri, bearerToken);

    // 3. Upload file to one of the blob containers we got from Kusto.
    // This example uses the first one, but when working with multiple blobs,
    // one should round-robin the containers in order to prevent throttling
    long blobSizeBytes = 0;
    string blobName = $"TestData{DateTime.UtcNow.ToString("yyyy-MM-dd_HH-mm-ss.FFF")}";
    string blobUriWithSas = UploadFileToBlobContainer(file, ingestionResources.TempStorageContainers.First(),
                                                            "temp001", blobName, out blobSizeBytes);

    // 4. Compose ingestion command
    string ingestionMessage = PrepareIngestionMessage(db, table, blobUriWithSas, blobSizeBytes, ingestionMappingRef, identityToken);

    // 5. Post ingestion command to one of the ingestion queues we got from Kusto.
    // This example uses the first one, but when working with multiple blobs,
    // one should round-robin the queues in order to prevent throttling
    PostMessageToQueue(ingestionResources.IngestionQueues.First(), ingestionMessage);

    Thread.Sleep(20000);

    // 6a. Read success notifications
    var successes = PopTopMessagesFromQueue(ingestionResources.SuccessNotificationsQueue, 32);
    foreach (var sm in successes)
    {
        Console.WriteLine($"Ingestion completed: {sm}");
    }

    // 6b. Read failure notifications
    var errors = PopTopMessagesFromQueue(ingestionResources.FailureNotificationsQueue, 32);
    foreach (var em in errors)
    {
        Console.WriteLine($"Ingestion error: {em}");
    }
}
```

## <a name="1-obtain-authentication-evidence-from-aad"></a>1. Abrufen von Authentifizierungs beweisen aus Aad
Hier verwenden wir Adal zum Abrufen eines Aad-Tokens für den Zugriff auf den Kusto-Datenverwaltung-Dienst, um die Eingabe Warteschlangen anzufordern.
Adal ist bei Bedarf auf [nicht-Windows-Plattformen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) verfügbar.
```csharp
// Authenticates the interactive user and retrieves AAD Access token for specified resource
internal static string AuthenticateInteractiveUser(string resource)
{
    // Create Auth Context for MSFT AAD:
    AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/{AAD Tenant ID or name}");

    // Acquire user token for the interactive user for Kusto:
    AuthenticationResult result =
        authContext.AcquireTokenAsync(resource, "<your client app ID>", new Uri(@"<your client app URI>"),
                                        new PlatformParameters(PromptBehavior.Auto), UserIdentifier.AnyUser, "prompt=select_account").Result;
    return result.AccessToken;
}
```

## <a name="2-retrieve-kusto-ingestion-resources"></a>2. Abrufen von Kusto-Erfassungs Ressourcen
An dieser Stelle werden die Dinge interessant. Hier erstellen Sie manuell eine HTTP POST-Anforderung an den Kusto-Datenverwaltung-Dienst, der anfordert, die Erfassungs Ressourcen zurückzugeben.
Dazu zählen Warteschlangen, die der DM-Dienst überwacht, sowie BLOB-Container für das Hochladen von Daten.
Der Datenverwaltung-Dienst verarbeitet alle Nachrichten, die Erfassungs Anforderungen enthalten, die in einer dieser Warteschlangen eintreffen.
```csharp
// Retrieve ingestion resources (queues and blob containers) with SAS from specified Kusto Ingestion service using supplied Access token
internal static IngestionResourcesSnapshot RetrieveIngestionResources(string ingestClusterBaseUri, string accessToken)
{
    string ingestClusterUri = $"{ingestClusterBaseUri}/v1/rest/mgmt";
    string requestBody = $"{{ \"csl\": \".get ingestion resources\" }}";

    IngestionResourcesSnapshot ingestionResources = new IngestionResourcesSnapshot();

    using (WebResponse response = SendPostRequest(ingestClusterUri, accessToken, requestBody))
    using (StreamReader sr = new StreamReader(response.GetResponseStream()))
    using (JsonTextReader jtr = new JsonTextReader(sr))
    {
        JObject responseJson = JObject.Load(jtr);
        IEnumerable<JToken> tokens;

        // Input queues
        tokens = responseJson.SelectTokens("Tables[0].Rows[?(@.[0] == 'SecuredReadyForAggregationQueue')]");
        foreach (var token in tokens)
        {
            ingestionResources.IngestionQueues.Add((string) token[1]);
        }

        // Temp storage containers
        tokens = responseJson.SelectTokens("Tables[0].Rows[?(@.[0] == 'TempStorage')]");
        foreach (var token in tokens)
        {
            ingestionResources.TempStorageContainers.Add((string)token[1]);
        }

        // Failure notifications queue
        var singleToken =
            responseJson.SelectTokens("Tables[0].Rows[?(@.[0] == 'FailedIngestionsQueue')].[1]").FirstOrDefault();
        ingestionResources.FailureNotificationsQueue = (string)singleToken;

        // Success notifications queue
        singleToken =
            responseJson.SelectTokens("Tables[0].Rows[?(@.[0] == 'SuccessfulIngestionsQueue')].[1]").FirstOrDefault();
        ingestionResources.SuccessNotificationsQueue = (string)singleToken;
    }

    return ingestionResources;
}

// Executes a POST request on provided URI using supplied Access token and request body
internal static WebResponse SendPostRequest(string uriString, string authToken, string body)
{
    WebRequest request = WebRequest.Create(uriString);

    request.Method = "POST";
    request.ContentType = "application/json";
    request.ContentLength = body.Length;
    request.Headers.Set(HttpRequestHeader.Authorization, $"Bearer {authToken}");

    Stream bodyStream = request.GetRequestStream();
    using (StreamWriter sw = new StreamWriter(bodyStream))
    {
        sw.Write(body);
        sw.Flush();
    }

    bodyStream.Close();
    return request.GetResponse();
}
```

## <a name="obtaining-kusto-identity-token"></a>Abrufen von Kusto-Identitäts Token
Ein wichtiger Schritt beim autorialisieren der Datenerfassung ist das Abrufen eines Identitäts Tokens und das Anfügen an jede Erfassungs Nachricht. Wenn Erfassungs Meldungen über einen nicht direkt Kanal (Azure Queue) an Kusto übergeben werden, gibt es keine Möglichkeit, die in-Band-Autorisierungs Überprüfung durchzuführen. Der Identitäts Token-Mechanismus ermöglicht dies durch Ausgeben eines Kusto-signierten Identitätsnachweises, der vom Kusto-Dienst überprüft werden kann, sobald er die Erfassungs Nachricht empfängt.
```csharp
// Retrieves a Kusto identity token that will be added to every ingest message
internal static string RetrieveKustoIdentityToken(string ingestClusterBaseUri, string accessToken)
{
    string ingestClusterUri = $"{ingestClusterBaseUri}/v1/rest/mgmt";
    string requestBody = $"{{ \"csl\": \".get kusto identity token\" }}";
    string jsonPath = "Tables[0].Rows[*].[0]";

    using (WebResponse response = SendPostRequest(ingestClusterUri, accessToken, requestBody))
    using (StreamReader sr = new StreamReader(response.GetResponseStream()))
    using (JsonTextReader jtr = new JsonTextReader(sr))
    {
        JObject responseJson = JObject.Load(jtr);
        JToken identityToken = responseJson.SelectTokens(jsonPath).FirstOrDefault();

        return ((string)identityToken);
    }
}
```

## <a name="3-upload-data-to-azure-blob-container"></a>3. Hochladen von Daten in einen Azure-BLOB-Container
In diesem Schritt wird eine lokale Datei in ein Azure-BLOB hochgeladen, die später für die Erfassung übergeben wird. In diesem Code wird Azure Storage SDK verwendet. wenn diese Abhängigkeit jedoch nicht möglich ist, kann Sie mit der [Rest-API des Azure-BLOB-Dienstanbieter](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-rest-api)identisch sein.
```csharp
// Uploads a single local file to an Azure Blob container, returns blob URI and original data size
internal static string UploadFileToBlobContainer(string filePath, string blobContainerUri, string containerName, string blobName, out long blobSize)
{
    var blobUri = new Uri(blobContainerUri);
    CloudBlobContainer blobContainer = new CloudBlobContainer(blobUri);
    CloudBlockBlob blockBlob = blobContainer.GetBlockBlobReference(blobName);

    using (Stream stream = File.OpenRead(filePath))
    {
        blockBlob.UploadFromStream(stream);
        blobSize = blockBlob.Properties.Length;
    }

    return string.Format("{0}{1}", blockBlob.Uri.AbsoluteUri, blobUri.Query);
}
```

## <a name="4-compose-kusto-ingestion-message"></a>4. Verfassen der Kusto-Erfassungs Nachricht
Hier verwenden wir das Newton Soft. JSON-Paket erneut, um eine gültige Erfassungs Anforderungs Nachricht zu verfassen, die an die Azure-Warteschlange gesendet wird, an die der entsprechende Kusto-Datenverwaltung Dienst lauscht.
* Dies ist das absolute Minimum für die Erfassungs Nachricht.
* Beachten Sie, dass dieses Identitäts Token obligatorisch ist und sich im `AdditionalProperties` JSON-Objekt befinden muss.
* Bei Bedarf müssen `CsvMapping` auch eine `JsonMapping` -Eigenschaft oder eine-Eigenschaft bereitgestellt werden.
* Weitere Informationen finden Sie [im Artikel zur vorab Erstellung der Erfassungs Zuordnung](../../management/create-ingestion-mapping-command.md) .
* [Anhang A](#appendix-a-ingestion-message-internal-structure) enthält Erläuterungen zur Struktur der Erfassungs Nachricht.

```csharp
internal static string PrepareIngestionMessage(string db, string table, string dataUri, long blobSizeBytes, string mappingRef, string identityToken)
{
    var message = new JObject();

    message.Add("Id", Guid.NewGuid().ToString());
    message.Add("BlobPath", dataUri);
    message.Add("RawDataSize", blobSizeBytes);
    message.Add("DatabaseName", db);
    message.Add("TableName", table);
    message.Add("RetainBlobOnSuccess", true);   // Do not delete the blob on success
    message.Add("Format", "json");              // Data is in JSON format
    message.Add("FlushImmediately", true);      // Do not aggregate
    message.Add("ReportLevel", 2);              // Report failures and successes (might incur perf overhead)
    message.Add("ReportMethod", 0);             // Failures are reported to an Azure Queue

    message.Add("AdditionalProperties", new JObject(
                                            new JProperty("authorizationContext", identityToken),
                                            new JProperty("jsonMappingReference", mappingRef)));
    return message.ToString();
}
```

## <a name="5-post-kusto-ingestion-message-to-kusto-ingestion-queue"></a>5. Senden der Kusto-Erfassungs Nachricht an die Kusto-Erfassungs Warteschlange
Und schließlich wird die von uns erstellte Nachricht lediglich in der von uns ausgewählten Warteschlange gepostet.<BR>
Hinweis: bei Verwendung des .NET-Speicher Clients wird die Nachricht standardmäßig in Base64 codiert. Weitere Informationen finden Sie unter [Storage docs](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.encodemessage).<BR>
Wenn Sie diesen Client nicht verwenden, stellen Sie sicher, dass Sie den Nachrichten Inhalt ordnungsgemäß codieren.

```csharp
internal static void PostMessageToQueue(string queueUriWithSas, string message)
{
    CloudQueue queue = new CloudQueue(new Uri(queueUriWithSas));
    CloudQueueMessage queueMessage = new CloudQueueMessage(message);

    queue.AddMessage(queueMessage, null, null, null, null);
}
```

## <a name="6-pop-messages-from-an-azure-queue"></a>6. Pop-Nachrichten aus einer Azure-Warteschlange
Nach der Erfassung wird diese Methode verwendet, um Fehlermeldungen aus der entsprechenden Warteschlange zu lesen, die von Kusto Datenverwaltung-Dienst geschrieben wird.<BR>
[Anhang B](#appendix-b-ingestion-failure-message-structure) enthält Erläuterungen zur Struktur der Fehlermeldungen.

```csharp
internal static IEnumerable<string> PopTopMessagesFromQueue(string queueUriWithSas, int count)
{
    List<string> messages = Enumerable.Empty<string>().ToList();
    CloudQueue queue = new CloudQueue(new Uri(queueUriWithSas));
    var messagesFromQueue = queue.GetMessages(count);
    foreach (var m in messagesFromQueue)
    {
        messages.Add(m.AsString);
        queue.DeleteMessage(m);
    }

    return messages;
}
```

## <a name="appendix-a-ingestion-message-internal-structure"></a>Anhang A: interne Struktur der Erfassungs Nachricht
Die Meldung, dass der Kusto-Datenverwaltung-Dienst aus der Azure-Eingabe Warteschlange lesen soll, ist ein JSON-Dokument im folgenden Format:

```json
{
    "Id" : "<Id>",
    "BlobPath" : "https://<AccountName>.blob.core.windows.net/<ContainerName>/<PathToBlob>?<SasToken>",
    "RawDataSize" : "<RawDataSizeInBytes>",
    "DatabaseName": "<DatabaseName>",
    "TableName" : "<TableName>",
    "RetainBlobOnSuccess" : "<RetainBlobOnSuccess>",
    "Format" : "<csv|tsv|...>",
    "FlushImmediately": "<true|false>",
    "ReportLevel" : <0-Failures, 1-None, 2-All>,
    "ReportMethod" : <0-Queue, 1-Table>,
    "AdditionalProperties" : { "<PropertyName>" : "<PropertyValue>" }
}
```


|Eigenschaft | BESCHREIBUNG |
|---------|-------------|
|Id |Nachrichten-ID (GUID) |
|BlobPath |BLOB-URI, einschließlich des SAS-Schlüssels, der Kusto-Berechtigungen zum Lesen/Schreiben/löschen gewährt (Lese-/Schreibberechtigungen sind erforderlich, wenn Kusto das BLOB nach dem Erfassen der Daten löschen soll) |
|Rawdatasize |Größe der nicht komprimierten Daten in Bytes. Durch die Bereitstellung dieses Werts kann Kusto die Erfassung optimieren, indem mehrere blobvorgänge möglicherweise zusammen aggregierte werden. Diese Eigenschaft ist optional. Wenn Sie jedoch nicht angegeben wird, greift Kusto nur zum Abrufen der Größe auf das BLOB zu. |
|DatabaseName |Name der Zieldatenbank |
|TableName |Name der Ziel Tabelle |
|Retainblobonsuccess |Wenn der Wert `true`auf festgelegt ist, wird das BLOB nicht gelöscht, nachdem die Erfassung erfolgreich abgeschlossen wurde. Der Standardwert lautet `false`. |
|Format |Unkomprimiertes Datenformat |
|Flushimmediately |Wenn diese Einstellung `true`auf festgelegt ist, wird jede Aggregation übersprungen. Der Standardwert lautet `false`. |
|Beim |Erfolgs-/Fehlerberichterstattungs-Ebene: 0-Fehler, 1-keine, 2-alle |
|Report Method |Berichtsmechanismus: 0-Warteschlange, 1-Tabelle |
|AdditionalProperties |Alle zusätzlichen Eigenschaften (Tags usw.) |


## <a name="appendix-b-ingestion-failure-message-structure"></a>Anhang B: Struktur der Erfassungs Fehlermeldung
In der folgenden Tabelle wird die Meldung, dass der Kusto-Datenverwaltung-Dienst aus der Azure-Eingabe Warteschlange liest, ein JSON-Dokument im folgenden Format:

|Eigenschaft | BESCHREIBUNG |
|---------|-------------
|OperationId |Vorgangs-ID (GUID), die zum Nachverfolgen des Vorgangs auf der Dienst Seite verwendet werden kann. |
|Datenbank |Name der Zieldatenbank |
|Tabelle |Name der Ziel Tabelle |
|Failedon |Fehler Zeitstempel |
|Ingestionsourceid |GUID, die den Datenblock identifiziert, den Kusto nicht erfassen konnte |
|Ingestionsourcepath |Der Pfad (URI) zum Datenblock, den Kusto nicht erfassen konnte. |
|Details |Fehlermeldung |
|ErrorCode |Kusto-Fehlercode ( [hier](kusto-ingest-client-errors.md#ingestion-error-codes)finden Sie alle Fehlercodes) |
|Failurestatus |Gibt an, ob der Fehler permanent oder vorübergehend ist. |
|RootActivityId |Kusto-Korrelations Bezeichner (GUID), der zum Nachverfolgen des Vorgangs auf der Dienst Seite verwendet werden kann. |
|Originatesfromupdatepolicy |Gibt an, ob der Fehler durch eine fehlerhafte [Richtlinie für transaktionale Updates](../../management/updatepolicy.md) verursacht wurde. |
|Schultern | Gibt an, ob die Erfassung erfolgreich durchgeführt werden kann, wenn der Vorgang unverändert erfolgt |