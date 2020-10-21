---
title: 'Kusto-Datenerfassung ohne Erfassungs Bibliothek: Azure-Daten-Explorer'
description: In diesem Artikel wird die Beschreibung der Datenerfassung ohne Kusto. Erfassungs Bibliothek in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 02/19/2020
ms.openlocfilehash: 694b229c36a8bbbe6c15531b555dc8467198cd65
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92337600"
---
# <a name="ingestion-without-kustoingest-library"></a>Erfassung ohne Kusto. Erfassungs Bibliothek

Die Kusto. Erfassungs Bibliothek ist für das Erfassen von Daten in Azure Daten-Explorer bevorzugt. Sie können jedoch immer noch fast die gleiche Funktionalität erreichen, ohne vom Kusto. inerfassungs Paket abhängig zu sein.
In diesem Artikel erfahren Sie, wie Sie mithilfe der in der *Warteschlange* befindlichen Erfassung in Azure Daten-Explorer für Pipelines auf Produktionsqualität anwenden.

> [!NOTE]
> Der folgende Code ist in c# geschrieben und nutzt das Azure Storage SDK, die Adal-Authentifizierungs Bibliothek und die NewtonSoft.JSfür das Paket, um den Beispielcode zu vereinfachen. Bei Bedarf kann der entsprechende Code durch entsprechende [Azure Storage Rest-API](/rest/api/storageservices/blob-service-rest-api) -Aufrufe, [Non-.net Adal-Paket](/azure/active-directory/develop/active-directory-authentication-libraries)und beliebiges verfügbares JSON-Verarbeitungs Paket ersetzt werden.

In diesem Artikel wird der empfohlene Modus für die Erfassung behandelt. Die zugehörige Entität für die Kusto. Erfassungs Bibliothek ist die [ikustoqueuedingestclient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) -Schnittstelle. Hier interagiert der Client Code mit dem Azure Daten-Explorer Service, indem er Erfassungs Benachrichtigungs Meldungen an eine Azure-Warteschlange übermitteln. Verweise auf die Nachrichten werden vom Kusto-Datenverwaltung abgerufen (auch als Erfassungs Dienst bezeichnet). Die Interaktion mit dem Dienst muss mit Azure Active Directory (Azure AD) authentifiziert werden.

Der folgende Code zeigt, wie der Kusto-Datenverwaltung-Dienst die Erfassung von Daten in der Warteschlange verarbeitet, ohne die Kusto. Erfassung-Bibliothek zu verwenden. Dieses Beispiel ist möglicherweise hilfreich, wenn auf vollständiges .net aufgrund der Umgebung oder anderer Einschränkungen nicht zugegriffen werden kann oder nicht verfügbar ist.

Der Code enthält die Schritte zum Erstellen eines Azure Storage Clients und zum Hochladen der Daten in ein BLOB.
Jeder Schritt wird nach dem Beispielcode ausführlicher beschrieben.

1. [Abrufen eines Authentifizierungs Tokens für den Zugriff auf den Azure Daten-Explorer-Erfassungs Dienst](#obtain-authentication-evidence-from-azure-ad)
1. Fragen Sie den Azure Daten-Explorer Erfassungs Dienst ab, um Folgendes zu erhalten:
    * [Erfassungs Ressourcen (Warteschlangen und BLOB-Container)](#retrieve-azure-data-explorer-ingestion-resources)
    * [Ein Kusto-Identitäts Token, das jeder Erfassungs Nachricht hinzugefügt wird.](#obtain-a-kusto-identity-token)
1. [Hochladen von Daten in ein BLOB in einem der BLOB-Container, die aus Kusto abgerufen wurden in (2)](#upload-data-to-the-azure-blob-container)
1. [Erstellen Sie eine Erfassungs Nachricht, die die Zieldatenbank und die Ziel Tabelle identifiziert und auf das BLOB verweist (3).](#compose-the-azure-data-explorer-ingestion-message)
1. [Stellen Sie die Erfassungs Nachricht, die wir in (4) erstellt haben, in eine Erfassungs Warteschlange, die von Azure Daten-Explorer in (2)](#post-the-azure-data-explorer-ingestion-message-to-the-azure-data-explorer-ingestion-queue)**
1. [Abrufen eines Fehlers, der vom Dienst während der Erfassung gefunden wurde](#check-for-error-messages-from-the-azure-queue)

```csharp
// A container class for ingestion resources we are going to obtain from Azure Data Explorer
internal class IngestionResourcesSnapshot
{
    public IList<string> IngestionQueues { get; set; } = new List<string>();
    public IList<string> TempStorageContainers { get; set; } = new List<string>();

    public string FailureNotificationsQueue { get; set; } = string.Empty;
    public string SuccessNotificationsQueue { get; set; } = string.Empty;
}

public static void IngestSingleFile(string file, string db, string table, string ingestionMappingRef)
{
    // Your Azure Data Explorer ingestion service URI, typically ingest-<your cluster name>.kusto.windows.net
    string DmServiceBaseUri = @"https://ingest-{serviceNameAndRegion}.kusto.windows.net";

    // 1. Authenticate the interactive user (or application) to access Kusto ingestion service
    string bearerToken = AuthenticateInteractiveUser(DmServiceBaseUri);

    // 2a. Retrieve ingestion resources
    IngestionResourcesSnapshot ingestionResources = RetrieveIngestionResources(DmServiceBaseUri, bearerToken);

    // 2b. Retrieve Kusto identity token
    string identityToken = RetrieveKustoIdentityToken(DmServiceBaseUri, bearerToken);

    // 3. Upload file to one of the blob containers we got from Azure Data Explorer.
    // This example uses the first one, but when working with multiple blobs,
    // one should round-robin the containers in order to prevent throttling
    long blobSizeBytes = 0;
    string blobName = $"TestData{DateTime.UtcNow.ToString("yyyy-MM-dd_HH-mm-ss.FFF")}";
    string blobUriWithSas = UploadFileToBlobContainer(file, ingestionResources.TempStorageContainers.First(),
                                                            "temp001", blobName, out blobSizeBytes);

    // 4. Compose ingestion command
    string ingestionMessage = PrepareIngestionMessage(db, table, blobUriWithSas, blobSizeBytes, ingestionMappingRef, identityToken);

    // 5. Post ingestion command to one of the ingestion queues we got from Azure Data Explorer.
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

## <a name="using-queued-ingestion-to-azure-data-explorer-for-production-grade-pipelines"></a>Verwenden der in die Warteschlange eingereihten Erfassung in Azure Daten-Explorer für Pipelines auf Produktionsqualität

### <a name="obtain-authentication-evidence-from-azure-ad"></a>Abrufen von Authentifizierungs beweisen aus Azure AD

Hier wird Adal verwendet, um ein Azure AD Token für den Zugriff auf den Kusto-Datenverwaltung Dienst abzurufen und Eingabe Warteschlangen anzufordern.
Adal ist bei Bedarf auf [nicht-Windows-Plattformen](/azure/active-directory/develop/active-directory-authentication-libraries) verfügbar.

```csharp
// Authenticates the interactive user and retrieves Azure AD Access token for specified resource
internal static string AuthenticateInteractiveUser(string resource)
{
    // Create Auth Context for MSFT Azure AD:
    AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/{Azure AD Tenant ID or name}");

    // Acquire user token for the interactive user for Azure Data Explorer:
    AuthenticationResult result =
        authContext.AcquireTokenAsync(resource, "<your client app ID>", new Uri(@"<your client app URI>"),
                                        new PlatformParameters(PromptBehavior.Auto), UserIdentifier.AnyUser, "prompt=select_account").Result;
    return result.AccessToken;
}
```

### <a name="retrieve-azure-data-explorer-ingestion-resources"></a>Abrufen von Azure Daten-Explorer Erfassungs Ressourcen

Erstellen Sie manuell eine HTTP POST-Anforderung an den Datenverwaltung-Dienst, um die Rückgabe der Erfassungs Ressourcen anzufordern. Zu diesen Ressourcen zählen Warteschlangen, die der DM-Dienst überwacht, und BLOB-Container für das Hochladen von Daten.
Der Datenverwaltung-Dienst verarbeitet alle Nachrichten, die Erfassungs Anforderungen enthalten, die in einer dieser Warteschlangen eintreffen.

```csharp
// Retrieve ingestion resources (queues and blob containers) with SAS from specified Azure Data Explorer Ingestion service using supplied Access token
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

### <a name="obtain-a-kusto-identity-token"></a>Abrufen eines Kusto-Identitäts Tokens

Erfassungs Nachrichten werden an Azure Daten-Explorer über einen nicht direkten Kanal (Azure-Warteschlange) übergeben, sodass die in-Band-Autorisierungs Überprüfung für den Zugriff auf den Azure Daten-Explorer-Erfassungs Dienst nicht möglich ist. Die Lösung besteht darin, ein Identitäts Token an jede Erfassungs Nachricht anzufügen. Das Token ermöglicht die Überprüfung der in-Band-Autorisierung. Dieses signierte Token kann dann vom Azure Daten-Explorer Service überprüft werden, wenn es die Erfassungs Nachricht empfängt.

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

### <a name="upload-data-to-the-azure-blob-container"></a>Hochladen von Daten in den Azure-BLOB-Container

In diesem Schritt wird eine lokale Datei in ein Azure-BLOB hochgeladen, das für die Erfassung übergeben wird. In diesem Code wird das Azure Storage SDK verwendet. Wenn keine Abhängigkeit möglich ist, kann dies mit der [Rest-API des Azure-BLOB-Dienstanbieter](/rest/api/storageservices/fileservices/blob-service-rest-api)erreicht werden

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

### <a name="compose-the-azure-data-explorer-ingestion-message"></a>Erstellen der Azure Daten-Explorer Erfassungs Nachricht

Die NewtonSoft.JSfür das Paket verfassen erneut eine gültige Erfassungs Anforderung, um die Zieldatenbank und die Ziel Tabelle zu identifizieren, und die auf das BLOB verweist.
Die Nachricht wird an die Azure-Warteschlange gesendet, an der der relevante Kusto-Datenverwaltung Dienst lauscht.

Hier sind einige Punkte zu beachten.

* Diese Anforderung ist das absolute Minimum für die Erfassungs Nachricht.

> [!NOTE]
> Das Identitäts Token ist obligatorisch und muss Teil des JSON-Objekts " **AdditionalProperties** " sein.

* Wenn dies erforderlich ist, müssen auch csvmapping-oder jsonmapping-Eigenschaften bereitgestellt werden.
* Weitere Informationen finden Sie im [Artikel zur vorab Erstellung der Erfassungs Zuordnung](../../management/create-ingestion-mapping-command.md).
* Die [interne Struktur](#ingestion-message-internal-structure) der Erfassungs Nachricht des Abschnitts enthält eine Erläuterung der Erfassungs Nachrichtenstruktur.

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
    message.Add("FlushImmediately", true);      // Do not aggregate
    message.Add("ReportLevel", 2);              // Report failures and successes (might incur perf overhead)
    message.Add("ReportMethod", 0);             // Failures are reported to an Azure Queue

    message.Add("AdditionalProperties", new JObject(
                                            new JProperty("authorizationContext", identityToken),
                                            new JProperty("jsonMappingReference", mappingRef),
                                            // Data is in JSON format
                                            new JProperty("format", "json")));
    return message.ToString();
}
```

### <a name="post-the-azure-data-explorer-ingestion-message-to-the-azure-data-explorer-ingestion-queue"></a>Veröffentlichen der Azure-Daten-Explorer Erfassungs Nachricht in der Warteschlange für die Azure-Daten-Explorer Erfassung

Veröffentlichen Sie schließlich die erstellte Nachricht in der ausgewählten Erfassungs Warteschlange, die Sie aus Azure Daten-Explorer abgerufen haben.

> [!NOTE]
> .NET-Speicher Client Versionen unter V12 Codieren die Nachricht standardmäßig in Base64, um weitere Informationen zu erhalten. Weitere Informationen finden Sie unter [Storage docs](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.encodemessage?view=azure-dotnet-legacy#Microsoft_WindowsAzure_Storage_Queue_CloudQueue_EncodeMessage). Wenn Sie .NET-Speicher Client Versionen oberhalb von V12 verwenden, müssen Sie den Nachrichten Inhalt ordnungsgemäß codieren.

```csharp
internal static void PostMessageToQueue(string queueUriWithSas, string message)
{
    CloudQueue queue = new CloudQueue(new Uri(queueUriWithSas));
    CloudQueueMessage queueMessage = new CloudQueueMessage(message);

    queue.AddMessage(queueMessage, null, null, null, null);
}
```

### <a name="check-for-error-messages-from-the-azure-queue"></a>Überprüfen auf Fehlermeldungen aus der Azure-Warteschlange

Nach der Erfassung überprüfen wir, ob Fehlermeldungen aus der relevanten Warteschlange auftreten, in die der Datenverwaltung schreibt. Weitere Informationen zur Fehler Meldungs Struktur finden Sie unter Erfassungs Fehler Meldungs [Struktur](#ingestion-failure-message-structure). 

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

## <a name="ingestion-messages---json-document-formats"></a>Erfassung von Nachrichten: JSON-Dokumentformate

### <a name="ingestion-message-internal-structure"></a>Interne Struktur der Erfassungs Nachricht

Die Meldung, dass der Kusto-Datenverwaltung-Dienst aus der Azure-Eingabe Warteschlange lesen soll, ist ein JSON-Dokument im folgenden Format.

```JSON
{
    "Id" : "<Id>",
    "BlobPath" : "https://<AccountName>.blob.core.windows.net/<ContainerName>/<PathToBlob>?<SasToken>",
    "RawDataSize" : "<RawDataSizeInBytes>",
    "DatabaseName": "<DatabaseName>",
    "TableName" : "<TableName>",
    "RetainBlobOnSuccess" : "<RetainBlobOnSuccess>",
    "FlushImmediately": "<true|false>",
    "ReportLevel" : <0-Failures, 1-None, 2-All>,
    "ReportMethod" : <0-Queue, 1-Table>,
    "AdditionalProperties" : { "<PropertyName>" : "<PropertyValue>" }
}
```

|Eigenschaft | BESCHREIBUNG |
|---------|-------------|
|Id |Nachrichten-ID (GUID) |
|BlobPath |Pfad (URI) zum BLOB, einschließlich des SAS-Schlüssels, dem Azure Daten-Explorer Berechtigungen zum Lesen/Schreiben/Löschen erteilt werden. Berechtigungen sind erforderlich, damit Azure Daten-Explorer das BLOB löschen kann, nachdem die Erfassung der Daten abgeschlossen ist.|
|Rawdatasize |Größe der nicht komprimierten Daten in Bytes. Durch die Angabe dieses Werts kann Azure Daten-Explorer die Erfassung optimieren, indem möglicherweise mehrere blobdaten aggregierte werden. Diese Eigenschaft ist optional. Wenn Sie jedoch nicht angegeben wird, greift Azure Daten-Explorer nur zum Abrufen der Größe auf das BLOB zu. |
|DatabaseName |Name der Zieldatenbank |
|TableName |Name der Ziel Tabelle |
|Retainblobonsuccess |Wenn der Wert auf festgelegt `true` ist, wird das BLOB nicht gelöscht, nachdem die Erfassung erfolgreich abgeschlossen wurde. Die Standardeinstellung ist `false`. |
|Flushimmediately |Wenn diese Einstellung auf festgelegt `true` ist, wird jede Aggregation übersprungen. Die Standardeinstellung ist `false`. |
|Beim |Erfolgs-/Fehlerberichterstattungs-Ebene: 0-Fehler, 1-keine, 2-alle |
|Report Method |Berichtsmechanismus: 0-Warteschlange, 1-Tabelle |
|AdditionalProperties |Zusätzliche Eigenschaften, z `format` `tags` . b., und `creationTime` . Weitere Informationen finden Sie unter [Eigenschaften](../../../ingestion-properties.md)für die Datenerfassung.|

### <a name="ingestion-failure-message-structure"></a>Struktur der Erfassungs Fehlermeldung

Die Meldung, die der Datenverwaltung erwartet, aus der Azure-Eingabe Warteschlange zu lesen, ist ein JSON-Dokument im folgenden Format.

|Eigenschaft | BESCHREIBUNG |
|---------|-------------
|OperationId |Vorgangs-ID (GUID), die zum Nachverfolgen des Vorgangs auf der Dienst Seite verwendet werden kann. |
|Datenbank |Name der Zieldatenbank |
|Tabelle |Name der Ziel Tabelle |
|Failedon |Fehler Zeitstempel |
|Ingestionsourceid |GUID, die den Datenblock identifiziert, den Azure Daten-Explorer nicht erfassen konnte |
|Ingestionsourcepath |Der Pfad (URI) zu dem Datenblock, den Azure Daten-Explorer nicht erfassen konnte. |
|Details |Fehlermeldung |
|ErrorCode |Azure Daten-Explorer-Fehlercode ( [hier](kusto-ingest-client-errors.md#ingestion-error-codes)finden Sie alle Fehlercodes). |
|Failurestatus |Gibt an, ob der Fehler permanent oder vorübergehend ist. |
|RootActivityId |Azure Daten-Explorer Korrelations-ID (GUID), die zum Nachverfolgen des Vorgangs auf der Dienst Seite verwendet werden kann |
|Originatesfromupdatepolicy |Gibt an, ob der Fehler durch eine fehlerhafte [Richtlinie für transaktionale Updates](../../management/updatepolicy.md) verursacht wurde. |
|Schultern | Gibt an, ob die Erfassung erfolgreich ist, wenn der Vorgang wiederholt wird. |