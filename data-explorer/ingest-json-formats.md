---
title: Erfassen von Daten im JSON-Format in Azure Data Explorer
description: Es wird beschrieben, wie Sie Daten im JSON-Format in Azure Data Explorer erfassen.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 36c7616ee3d79cebaea96ca67787466cbd401cab
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663178"
---
# <a name="ingest-json-formatted-sample-data-into-azure-data-explorer"></a>Erfassen von Beispieldaten im JSON-Format in Azure Data Explorer

In diesem Artikel wird das Erfassen von Daten im JSON-Format in einer Azure Data Explorer-Datenbank veranschaulicht. Sie beginnen mit einfachen Beispielen für unformatierten und zugeordneten JSON-Code, fahren dann mit mehrzeiligem JSON-Code fort und beschäftigen sich schließlich mit komplexeren JSON-Schemas, die Arrays und Wörterbücher enthalten.  Anhand der Beispiele wird der Prozess zur Erfassung von Daten im JSON-Format per Kusto-Abfragesprache (KQL), C# oder Python ausführlich beschrieben. Die `ingest`-Steuerbefehle der Kusto-Abfragesprache werden direkt für den Endpunkt der Engine ausgeführt. In Produktionsszenarien wird die Erfassung für den Datenverwaltungsdienst ausgeführt, indem Clientbibliotheken oder Datenverbindungen verwendet werden. Informationen zur exemplarischen Vorgehensweise bei der Erfassung von Daten mit diesen Clientbibliotheken finden Sie unter [Erfassen von Daten mit der Azure Data Explorer-Bibliothek für Python](/azure/data-explorer/python-ingest-data) und [Erfassen von Daten mit dem .NET Standard SDK für Azure Data Explorer](/azure/data-explorer/net-standard-ingest-data).

## <a name="prerequisites"></a>Voraussetzungen

[Einen Testcluster und eine Testdatenbank](create-cluster-database-portal.md)

## <a name="the-json-format"></a>JSON-Format

Azure Data Explorer unterstützt zwei JSON-Dateiformate:
* `json`: JSON-Code mit Trennung nach Zeilen. Jede Zeile in den Eingabedaten enthält exakt einen JSON-Datensatz.
* `multijson`: Mehrzeiliger JSON-Code. Der Parser ignoriert die Zeilentrennungen und liest einen Datensatz von der vorherigen Position bis zum Ende eines gültigen JSON-Codes.

### <a name="ingest-and-map-json-formatted-data"></a>Erfassen und Zuordnen von Daten im JSON-Format

Für die Erfassung von Daten im JSON-Format müssen Sie das *Format* angeben, indem Sie die [Erfassungseigenschaft](ingestion-properties.md) verwenden. Für die Erfassung von JSON-Daten ist eine [Zuordnung](kusto/management/mappings.md) erforderlich, bei der ein JSON-Quelleintrag der Zielspalte zugeordnet wird. Verwenden Sie beim Erfassen von Daten die vordefinierte `jsonMappingReference`-Erfassungseigenschaft, oder geben Sie die `jsonMapping`-Erfassungseigenschaft an. In diesem Artikel wird die `jsonMappingReference`-Erfassungseigenschaft verwendet, die in der für die Erfassung verwendeten Tabelle vordefiniert ist. In den folgenden Beispielen erfassen wir zunächst JSON-Datensätze als Rohdaten in einer einzelnen Tabellenspalte. Anschließend verwenden wir die Zuordnung, um jede Eigenschaft in ihrer zugeordneten Spalte zu erfassen. 

### <a name="simple-json-example"></a>Einfaches JSON-Beispiel

Das folgende Beispiel zeigt einfachen JSON-Code mit einer flachen Struktur. Die Daten enthalten Informationen zur Temperatur und Luftfeuchtigkeit, die von mehreren Geräten gesammelt werden. Jeder Datensatz ist mit einer ID und einem Zeitstempel gekennzeichnet.

```json
{
    "timestamp": "2019-05-02 15:23:50.0369439",
    "deviceId": "2945c8aa-f13e-4c48-4473-b81440bb5ca2",
    "messageId": "7f316225-839a-4593-92b5-1812949279b3",
    "temperature": 31.0301639051317,
    "humidity": 62.0791099602725
}
```

## <a name="ingest-raw-json-records"></a>Erfassen von unformatierten JSON-Datensätzen 

In diesem Beispiel erfassen Sie JSON-Datensätze als Rohdaten in einer Tabelle mit nur einer Spalte. Vorgänge wie die Datenbearbeitung, Verwendung von Abfragen und Aktualisierung von Richtlinien werden nach dem Erfassen der Daten durchgeführt.

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

Verwenden Sie die Kusto-Abfragesprache (KQL), um Daten im JSON-Rohformat zu erfassen.

1. Melden Sie sich bei [https://dataexplorer.azure.com](https://dataexplorer.azure.com) an.

1. Wählen Sie **Cluster hinzufügen** aus.

1. Geben Sie im Dialogfeld **Add cluster** (Cluster hinzufügen) Ihre Cluster-URL im Format `https://<ClusterName>.<Region>.kusto.windows.net/` ein, und wählen Sie dann **Hinzufügen** aus.

1. Fügen Sie den folgenden Befehl ein, und wählen Sie **Ausführen** aus, um die Tabelle zu erstellen.

    ```kusto
    .create table RawEvents (Event: dynamic)
    ```

    Mit dieser Abfrage wird eine Tabelle mit nur einer `Event`-Spalte und dem Datentyp [Dynamisch](kusto/query/scalar-data-types/dynamic.md) erstellt.

1. Erstellen Sie die JSON-Zuordnung.

    ```kusto
    .create table RawEvents ingestion json mapping 'RawEventMapping' '[{"column":"Event","path":"$"}]'
    ```

    Mit diesem Befehl wird eine Zuordnung erstellt und der JSON-Stammpfad `$` der Spalte `Event` zugeordnet.

1. Erfassen Sie Daten in der Tabelle `RawEvents`.

    ```kusto
    .ingest into table RawEvents h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=RawEventMapping)
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

Verwenden Sie C#, um Daten im JSON-Rohformat zu erfassen.

1. Erstellen Sie die Tabelle `RawEvents`.

    ```csharp
    var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
    var kustoConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder);

    var table = "RawEvents";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Events", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Erstellen Sie die JSON-Zuordnung.
    
    ```csharp
    var tableMapping = "RawEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                new JsonColumnMapping {ColumnName = "Events", JsonPath = "$"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```
    Mit diesem Befehl wird eine Zuordnung erstellt und der JSON-Stammpfad `$` der Spalte `Event` zugeordnet.

1. Erfassen Sie Daten in der Tabelle `RawEvents`.

    ```csharp
    var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var ingestConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder);
    
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

> [!NOTE]
> Die Daten werden gemäß der [Richtlinie für die Batchverarbeitung](kusto/management/batchingpolicy.md) aggregiert. Dies führt zu einer Latenz von einigen Minuten.

# <a name="python"></a>[Python](#tab/python)

Verwenden Sie Python, um Daten im JSON-Rohformat zu erfassen.

1. Erstellen Sie die Tabelle `RawEvents`.

    ```python
    KUSTO_URI = "https://<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_DATA = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_URI, AAD_TENANT_ID)
    KUSTO_CLIENT = KustoClient(KCSB_DATA)
    TABLE = "RawEvents"

    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Events: dynamic)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Erstellen Sie die JSON-Zuordnung.

    ```python
    MAPPING = "RawEventMapping"
    CREATE_MAPPING_COMMAND = ".create table " + TABLE + " ingestion json mapping '" + MAPPING + """' '[{"column":"Event","path":"$"}]'"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Erfassen Sie Daten in der Tabelle `RawEvents`.

    ```python
    INGEST_URI = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_INGEST = KustoConnectionStringBuilder.with_aad_device_authentication(INGEST_URI, AAD_TENANT_ID)
    INGESTION_CLIENT = KustoIngestClient(KCSB_INGEST)
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    > [!NOTE]
    > Die Daten werden gemäß der [Richtlinie für die Batchverarbeitung](kusto/management/batchingpolicy.md) aggregiert. Dies führt zu einer Latenz von einigen Minuten.

---

## <a name="ingest-mapped-json-records"></a>Erfassen von zugeordneten JSON-Datensätzen

In diesem Beispiel erfassen Sie die Daten von JSON-Datensätzen. Jede JSON-Eigenschaft wird einer Spalte der Tabelle zugeordnet. 

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. Erstellen Sie eine neue Tabelle mit einem ähnlichen Schema wie für die JSON-Eingabedaten. Wir verwenden diese Tabelle für alle folgenden Beispiele und Erfassungsbefehle. 

    ```kusto
    .create table Events (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)
    ```

1. Erstellen Sie die JSON-Zuordnung.

    ```kusto
    .create table Events ingestion json mapping 'FlatEventMapping' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'
    ```

    In dieser Zuordnung werden die `timestamp`-Einträge – wie im Tabellenschema definiert – in der Spalte `Time` mit dem Datentyp `datetime` erfasst.

1. Erfassen Sie Daten in der Tabelle `Events`.

    ```kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=FlatEventMapping)
    ```

    Die Datei „simple.json“ enthält einige JSON-Datensätze, die nach Zeilen getrennt sind. Das Format lautet `json`, und im Erfassungsbefehl wird das von Ihnen erstellte `FlatEventMapping`-Element für die Zuordnung genutzt.

# <a name="c"></a>[C#](#tab/c-sharp)

1. Erstellen Sie eine neue Tabelle mit einem ähnlichen Schema wie für die JSON-Eingabedaten. Wir verwenden diese Tabelle für alle folgenden Beispiele und Erfassungsbefehle. 

    ```csharp
    var table = "Events";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Time", "System.DateTime"),
                Tuple.Create("Device", "System.String"),
                Tuple.Create("MessageId", "System.String"),
                Tuple.Create("Temperature", "System.Double"),
                Tuple.Create("Humidity", "System.Double"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Erstellen Sie die JSON-Zuordnung.

    ```csharp
    var tableMapping = "FlatEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                        new JsonColumnMapping {ColumnName = "Time", JsonPath = "$.timestamp"},
                        new JsonColumnMapping {ColumnName = "Device", JsonPath = "$.deviceId"},
                        new JsonColumnMapping {ColumnName = "MessageId", JsonPath = "$.messageId"},
                        new JsonColumnMapping {ColumnName = "Temperature", JsonPath = "$.temperature"},
                        new JsonColumnMapping {ColumnName = "Humidity", JsonPath = "$.humidity"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```

    In dieser Zuordnung werden die `timestamp`-Einträge – wie im Tabellenschema definiert – in der Spalte `Time` mit dem Datentyp `datetime` erfasst.    

1. Erfassen Sie Daten in der Tabelle `Events`.

    ```csharp
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

    Die Datei „simple.json“ enthält einige JSON-Datensätze, die nach Zeilen getrennt sind. Das Format lautet `json`, und im Erfassungsbefehl wird das von Ihnen erstellte `FlatEventMapping`-Element für die Zuordnung genutzt.

# <a name="python"></a>[Python](#tab/python)

1. Erstellen Sie eine neue Tabelle mit einem ähnlichen Schema wie für die JSON-Eingabedaten. Wir verwenden diese Tabelle für alle folgenden Beispiele und Erfassungsbefehle. 

    ```python
    TABLE = "RawEvents"
    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Erstellen Sie die JSON-Zuordnung.

    ```python
    MAPPING = "FlatEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Erfassen Sie Daten in der Tabelle `Events`.

    ```python
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    Die Datei „simple.json“ enthält einige JSON-Datensätze, die nach Zeilen getrennt sind. Das Format lautet `json`, und im Erfassungsbefehl wird das von Ihnen erstellte `FlatEventMapping`-Element für die Zuordnung genutzt.    
---

## <a name="ingest-multi-lined-json-records"></a>Erfassen von mehrzeiligen JSON-Datensätzen

In diesem Beispiel erfassen Sie mehrzeilige JSON-Datensätze. Jede JSON-Eigenschaft wird einer Spalte der Tabelle zugeordnet. Die Datei „multilined.json“ enthält einige eingezogene JSON-Datensätze. Mit dem Format `multijson` wird die Engine angewiesen, die Datensätze anhand der JSON-Struktur zu lesen.

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

Erfassen Sie Daten in der Tabelle `Events`.

```kusto
.ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=FlatEventMapping)
```

# <a name="c"></a>[C#](#tab/c-sharp)

Erfassen Sie Daten in der Tabelle `Events`.

```csharp
var tableMapping = "FlatEventMapping";
var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
var properties =
    new KustoQueuedIngestionProperties(database, table)
    {
        Format = DataSourceFormat.multijson,
        IngestionMappingReference = tableMapping
    };

ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
```

# <a name="python"></a>[Python](#tab/python)

Erfassen Sie Daten in der Tabelle `Events`.

```python
MAPPING = "FlatEventMapping"
BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.JSON?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
INGESTION_CLIENT.ingest_from_blob(
    BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
```

---

## <a name="ingest-json-records-containing-arrays"></a>Erfassen von JSON-Datensätzen mit Arrays

Arraydatentypen sind eine geordnete Sammlung von Werten. Die Erfassung eines JSON-Arrays erfolgt über eine [Updaterichtlinie](kusto/management/update-policy.md). Der JSON-Code wird ohne Änderungen in einer Zwischentabelle erfasst. Bei allen Updaterichtlinien wird für die Tabelle `RawEvents` eine vordefinierte Funktion ausgeführt, und die Ergebnisse werden wieder in der Zieltabelle erfasst. Wir erfassen Daten mit der folgenden Struktur:

```json
{
    "records": 
    [
        {
            "timestamp": "2019-05-02 15:23:50.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "7f316225-839a-4593-92b5-1812949279b3",
            "temperature": 31.0301639051317,
            "humidity": 62.0791099602725
        },
        {
            "timestamp": "2019-05-02 15:23:51.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "57de2821-7581-40e4-861e-ea3bde102364",
            "temperature": 33.7529423105311,
            "humidity": 75.4787976739364
        }
    ]
}
```

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. Erstellen Sie eine `update policy`-Funktion, mit der die Sammlung von `records` so erweitert wird, dass jeder Wert der Sammlung über den Operator `mv-expand` in einer separaten Zeile angeordnet wird. Wir nutzen die Tabelle `RawEvents` als Quelltabelle und `Events` als Zieltabelle.

    ```kusto
    .create function EventRecordsExpand() {
        RawEvents
        | mv-expand records = Event
        | project
            Time = todatetime(records["timestamp"]),
            Device = tostring(records["deviceId"]),
            MessageId = tostring(records["messageId"]),
            Temperature = todouble(records["temperature"]),
            Humidity = todouble(records["humidity"])
    }
    ```

1. Das von der Funktion empfangene Schema muss mit dem Schema der Zieltabelle übereinstimmen. Verwenden Sie den Operator `getschema`, um das Schema zu überprüfen.

    ```kusto
    EventRecordsExpand() | getschema
    ```

1. Fügen Sie die Updaterichtlinie der Zieltabelle hinzu. Diese Richtlinie führt die Abfrage automatisch für alle neu erfassten Daten in der Zwischentabelle `RawEvents` aus und erfasst die Ergebnisse in der Tabelle `Events`. Definieren Sie eine Richtlinie mit einem Aufbewahrungszeitraum von Null, um das beständige Speichern der Zwischentabelle zu vermeiden.

    ```kusto
    .alter table Events policy update @'[{"Source": "RawEvents", "Query": "EventRecordsExpand()", "IsEnabled": "True"}]'
    ```

1. Erfassen Sie Daten in der Tabelle `RawEvents`.

    ```kusto
    .ingest into table RawEvents h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=RawEventMapping)
    ```

1. Sehen Sie sich die Daten in der Tabelle `Events` an.

    ```kusto
    Events
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

1. Erstellen Sie eine Updatefunktion, mit der die Sammlung von `records` so erweitert wird, dass jeder Wert der Sammlung über den Operator `mv-expand` in einer separaten Zeile angeordnet wird. Wir nutzen die Tabelle `RawEvents` als Quelltabelle und `Events` als Zieltabelle.   

    ```csharp
    var command =
        CslCommandGenerator.GenerateCreateFunctionCommand(
            "EventRecordsExpand",
            "UpdateFunctions",
            string.Empty,
            null,
            @"RawEvents
                | mv-expand records = Event
                | project
                    Time = todatetime(records['timestamp']),
                    Device = tostring(records['deviceId']),
                    MessageId = tostring(records['messageId']),
                    Temperature = todouble(records['temperature']),
                    Humidity = todouble(records['humidity'])",
            ifNotExists: false);

    kustoClient.ExecuteControlCommand(command);
    ```

    > [!NOTE]
    > Das von der Funktion empfangene Schema muss mit dem Schema der Zieltabelle übereinstimmen.

1. Fügen Sie die Updaterichtlinie der Zieltabelle hinzu. Diese Richtlinie führt die Abfrage automatisch für alle neu erfassten Daten in der Zwischentabelle `RawEvents` aus und erfasst die Ergebnisse in der Tabelle `Events`. Definieren Sie eine Richtlinie mit einem Aufbewahrungszeitraum von Null, um das beständige Speichern der Zwischentabelle zu vermeiden.

    ```csharp
    var command =
        ".alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]";

    kustoClient.ExecuteControlCommand(command);
    ```

1. Erfassen Sie Daten in der Tabelle `RawEvents`.

    ```csharp
    var table = "RawEvents";
    var tableMapping = "RawEventMapping";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```
    
1. Sehen Sie sich die Daten in der Tabelle `Events` an.

# <a name="python"></a>[Python](#tab/python)

1. Erstellen Sie eine Updatefunktion, mit der die Sammlung von `records` so erweitert wird, dass jeder Wert der Sammlung über den Operator `mv-expand` in einer separaten Zeile angeordnet wird. Wir nutzen die Tabelle `RawEvents` als Quelltabelle und `Events` als Zieltabelle.   

    ```python
    CREATE_FUNCTION_COMMAND = 
        '''.create function EventRecordsExpand() { 
            RawEvents
            | mv-expand records = Event
            | project
                Time = todatetime(records["timestamp"]),
                Device = tostring(records["deviceId"]),
                MessageId = tostring(records["messageId"]),
                Temperature = todouble(records["temperature"]),
                Humidity = todouble(records["humidity"])
            }'''
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_FUNCTION_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

    > [!NOTE]
    > Das von der Funktion empfangene Schema muss mit dem Schema der Zieltabelle übereinstimmen.

1. Fügen Sie die Updaterichtlinie der Zieltabelle hinzu. Diese Richtlinie führt die Abfrage automatisch für alle neu erfassten Daten in der Zwischentabelle `RawEvents` aus und erfasst die Ergebnisse in der Tabelle `Events`. Definieren Sie eine Richtlinie mit einem Aufbewahrungszeitraum von Null, um das beständige Speichern der Zwischentabelle zu vermeiden.

    ```python
    CREATE_UPDATE_POLICY_COMMAND = 
        """.alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_UPDATE_POLICY_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Erfassen Sie Daten in der Tabelle `RawEvents`.

    ```python
    TABLE = "RawEvents"
    MAPPING = "RawEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

1. Sehen Sie sich die Daten in der Tabelle `Events` an.

---    

## <a name="ingest-json-records-containing-dictionaries"></a>Erfassen von JSON-Datensätzen mit Wörterbüchern

JSON-Code mit einer Wörterbuchstruktur enthält Schlüssel-Wert-Paare. Für JSON-Datensätze wird eine Erfassungszuordnung durchgeführt, indem in `JsonPath` ein logischer Ausdruck verwendet wird. Sie können Daten mit der folgenden Struktur erfassen:

```json
{
    "event": 
    [
        {
            "Key": "timestamp",
            "Value": "2019-05-02 15:23:50.0000000"
        },
        {
            "Key": "deviceId",
            "Value": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71"
        },
        {
            "Key": "messageId",
            "Value": "7f316225-839a-4593-92b5-1812949279b3"
        },
        {
            "Key": "temperature",
            "Value": 31.0301639051317
        },
        {
            "Key": "humidity",
            "Value": 62.0791099602725
        }
    ]
}
```

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. Erstellen Sie eine JSON-Zuordnung.

    ```kusto
    .create table Events ingestion json mapping 'KeyValueEventMapping' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'
    ```

1. Erfassen Sie Daten in der Tabelle `Events`.

    ```kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=KeyValueEventMapping)
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

1. Erstellen Sie eine JSON-Zuordnung.

    ```csharp
    var tableName = "Events";
    var tableMapping = "KeyValueEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                        new JsonColumnMapping {ColumnName = "Time", JsonPath = "$.event[?(@.Key == 'timestamp')]"},
                        new JsonColumnMapping {ColumnName = "Device", JsonPath = "$.event[?(@.Key == 'deviceId')]"},
                        new JsonColumnMapping {ColumnName = "MessageId", JsonPath = "$.event[?(@.Key == 'messageId')]"},
                        new JsonColumnMapping {ColumnName = "Temperature", JsonPath = "$.event[?(@.Key == 'temperature')]"},
                        new JsonColumnMapping {ColumnName = "Humidity", JsonPath = "$.event[?(@.Key == 'humidity')]"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Erfassen Sie Daten in der Tabelle `Events`.

    ```csharp
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

# <a name="python"></a>[Python](#tab/python)

1. Erstellen Sie eine JSON-Zuordnung.

    ```python
    MAPPING = "KeyValueEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Erfassen Sie Daten in der Tabelle `Events`.

     ```python
    MAPPING = "KeyValueEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)u
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

---    

## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über die Datenerfassung](ingest-data-overview.md)
* [Write queries](write-queries.md) (Schreiben von Abfragen)
