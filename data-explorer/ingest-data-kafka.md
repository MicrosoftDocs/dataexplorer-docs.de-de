---
title: Erfassen von Daten aus Kafka in Azure Data Explorer
description: In diesem Artikel erfahren Sie, wie Sie Daten aus Kafka in Azure Data Explorer erfassen (laden).
author: orspod
ms.author: orspodek
ms.reviewer: ankhanol
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: cc2f10570081fec3a5762ab3f2e23b9e22839063
ms.sourcegitcommit: c6cb2b1071048daa872e2fe5a1ac7024762c180e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96774655"
---
# <a name="ingest-data-from-apache-kafka-into-azure-data-explorer"></a>Erfassen von Daten aus Apache Kafka in Azure Data Explorer
 
Azure Data Explorer unterstützt die [Datenerfassung](ingest-data-overview.md) aus [Apache Kafka](http://kafka.apache.org/documentation/). Bei Apache Kafka handelt es sich um eine verteilte Streamingplattform zum Erstellen von Echzeitstreaming-Datenpipelines, mit denen Daten auf zuverlässige Weise zwischen Systemen oder Anwendungen verschoben werden. [Kafka Connect](https://docs.confluent.io/3.0.1/connect/intro.html#kafka-connect) ist ein Tool zum skalierbaren und zuverlässigen Streamen von Daten zwischen Apache Kafka und anderen Datensystemen. Die [Kafka-Senke](https://github.com/Azure/kafka-sink-azure-kusto/blob/master/README.md) von Azure Data Explorer dient als Connector für Kafka und erfordert keine Verwendung von Code. Laden Sie die Senkenconnector-JAR-Datei aus [diesem Git-Repository](https://github.com/Azure/kafka-sink-azure-kusto/releases) oder vom [Confluent-Connectorhub](https://www.confluent.io/hub/microsoftcorporation/kafka-sink-azure-kusto) herunter.

In diesem Artikel wird veranschaulicht, wie Sie Daten mit Kafka in Azure Data Explorer erfassen. Sie verwenden hierfür ein eigenständiges Docker-Setup, um die Einrichtung des Kafka-Clusters und Kafka-Connectorclusters zu vereinfachen. 

Weitere Informationen finden Sie im [Git-Repository](https://github.com/Azure/kafka-sink-azure-kusto/blob/master/README.md) und in den [Versionsangaben](https://github.com/Azure/kafka-sink-azure-kusto/blob/master/README.md#13-major-version-specifics) zum Connector.

## <a name="prerequisites"></a>Voraussetzungen

* Erstellen Sie ein [Microsoft Azure-Konto](/azure/).
* Installieren Sie die [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).
* Installieren Sie [Docker](https://docs.docker.com/get-docker/) und [Docker Compose](https://docs.docker.com/compose/install).
* [Erstellen Sie im Azure-Portal einen Azure Data Explorer-Cluster und eine Datenbank](create-cluster-database-portal.md), indem Sie die Standardrichtlinien für den Cache und die Aufbewahrung verwenden.

## <a name="create-an-azure-active-directory-service-principal"></a>Erstellen eines Azure Active Directory-Dienstprinzipals

Der Azure Active Directory-Dienstprinzipal kann über das [Azure-Portal](/azure/active-directory/develop/howto-create-service-principal-portal) oder programmgesteuert (wie im folgenden Beispiel) erstellt werden.

Dieser Dienstprinzipal ist die Identität, die vom Connector zum Schreiben in die Azure Data Explorer-Tabelle genutzt wird. Wir gewähren diesem Dienstprinzipal später noch Berechtigungen für den Zugriff auf Azure Data Explorer.

1. Melden Sie sich per Azure CLI an Ihrem Azure-Abonnement an. Führen Sie anschließend im Browser die Authentifizierung durch.

   ```azurecli-interactive
   az login
   ```


1. Wählen Sie das Abonnement aus, das Sie zum Ausführen des Labs verwenden möchten. Dieser Schritt ist erforderlich, wenn Sie über mehrere Abonnements verfügen.

   ```azurecli-interactive
   az account set --subscription YOUR_SUBSCRIPTION_GUID
   ```

1. Erstellen Sie den Dienstprinzipal. In diesem Beispiel wird der Dienstprinzipal als `kusto-kafka-spn` bezeichnet.

   ```azurecli-interactive
   az ad sp create-for-rbac -n "kusto-kafka-spn"
   ```

1. Sie erhalten eine JSON-Antwort (wie unten dargestellt). Kopieren Sie die Angaben für `appId`, `password` und `tenant`, da Sie sie in den späteren Schritten noch benötigen.

    ```json
    {
      "appId": "fe7280c7-5705-4789-b17f-71a472340429",
      "displayName": "kusto-kafka-spn",
      "name": "http://kusto-kafka-spn",
      "password": "29c719dd-f2b3-46de-b71c-4004fb6116ee",
      "tenant": "42f988bf-86f1-42af-91ab-2d7cd011db42"
    }
    ```

## <a name="create-a-target-table-in-azure-data-explorer"></a>Erstellen einer Zieltabelle im Azure-Daten-Explorer

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)

1. Navigieren Sie zu Ihrem Azure Data Explorer-Cluster.

1. Verwenden Sie den folgenden Befehl, um die Tabelle `Storms` zu erstellen:

    ```kusto
    .create table Storms (StartTime: datetime, EndTime: datetime, EventId: int, State: string, EventType: string, Source: string)
    ```

    :::image type="content" source="media/ingest-data-kafka/create-table.png" alt-text="Erstellen einer Tabelle im Azure Data Explorer-Portal":::
    
1. Erstellen Sie mit dem folgenden Befehl die entsprechende Tabellenzuordnung `Storms_CSV_Mapping` für erfasste Daten:
    
    ```kusto
    .create table Storms ingestion csv mapping 'Storms_CSV_Mapping' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EventId","datatype":"int","Ordinal":2},{"Name":"State","datatype":"string","Ordinal":3},{"Name":"EventType","datatype":"string","Ordinal":4},{"Name":"Source","datatype":"string","Ordinal":5}]'
    ```    

1. Erstellen Sie eine Batcherfassungsrichtlinie in der Tabelle für die konfigurierbare Erfassungslatenz.

    > [!TIP]
    > Bei der [Richtlinie für die Batcherfassung](kusto/management/batchingpolicy.md) handelt es sich um eine Leistungsoptimierung mit drei Parametern. Die erste erfüllte Bedingung löst die Erfassung in der Azure Data Explorer-Tabelle aus.

    ```kusto
    .alter table Storms policy ingestionbatching @'{"MaximumBatchingTimeSpan":"00:00:15", "MaximumNumberOfItems": 100, "MaximumRawDataSizeMB": 300}'
    ```

1. Verwenden Sie den Dienstprinzipal aus [Erstellen eines Azure Active Directory-Dienstprinzipals](#create-an-azure-active-directory-service-principal), um die Berechtigung zum Verwenden der Datenbank zu gewähren.

    ```kusto
    .add database YOUR_DATABASE_NAME admins  ('aadapp=YOUR_APP_ID;YOUR_TENANT_ID') 'AAD App'
    ```

## <a name="run-the-lab"></a>Ausführen des Labs

Im folgenden Lab können Sie ausprobieren, wie Sie mit der Erstellung von Daten beginnen, den Kafka-Connector einrichten und diese Daten über den Connector an Azure Data Explorer streamen. Sie können die erfassten Daten dann anzeigen.

### <a name="clone-the-git-repo"></a>Klonen des Git-Repositorys

Klonen Sie das [Git-Repository](https://github.com/Azure/azure-kusto-labs) des Labs.

1. Erstellen Sie auf Ihrem Computer ein lokales Verzeichnis.

    ```
    mkdir ~/kafka-kusto-hol
    cd ~/kafka-kusto-hol
    ```

1. Klonen Sie das Repository.

    ```shell
    cd ~/kafka-kusto-hol
    git clone https://github.com/Azure/azure-kusto-labs
    cd azure-kusto-labs/kafka-integration/dockerized-quickstart
    ```

#### <a name="contents-of-the-cloned-repo"></a>Inhalt des geklonten Repositorys

Führen Sie den folgenden Befehl aus, um den Inhalt des geklonten Repositorys aufzulisten:

```
cd ~/kafka-kusto-hol/azure-kusto-labs/kafka-integration/dockerized-quickstart
tree
```

Das Ergebnis dieser Suche lautet wie folgt:

```
├── README.md
├── adx-query.png
├── adx-sink-config.json
├── connector
│   └── Dockerfile
├── docker-compose.yaml
└── storm-events-producer
    ├── Dockerfile
    ├── StormEvents.csv
    ├── go.mod
    ├── go.sum
    ├── kafka
    │   └── kafka.go
    └── main.go
 ```

### <a name="review-the-files-in-the-cloned-repo"></a>Überprüfen der Dateien im geklonten Repository

In den folgenden Abschnitten werden die wichtigen Teile der Dateien in der obigen Dateistruktur beschrieben.

#### <a name="adx-sink-configjson"></a>adx-sink-config.json

Diese Datei enthält die Eigenschaftendatei der Kusto-Senke, in der Sie bestimmte Konfigurationsdetails aktualisieren:
 
```json
{
    "name": "storm",
    "config": {
        "connector.class": "com.microsoft.azure.kusto.kafka.connect.sink.KustoSinkConnector",
        "flush.size.bytes": 10000,
        "flush.interval.ms": 10000,
        "tasks.max": 1,
        "topics": "storm-events",
        "kusto.tables.topics.mapping": "[{'topic': 'storm-events','db': '<enter database name>', 'table': 'Storms','format': 'csv', 'mapping':'Storms_CSV_Mapping'}]",
        "aad.auth.authority": "<enter tenant ID>",
        "aad.auth.appid": "<enter application ID>",
        "aad.auth.appkey": "<enter client secret>",
        "kusto.url": "https://ingest-<name of cluster>.<region>.kusto.windows.net",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "value.converter": "org.apache.kafka.connect.storage.StringConverter"
    }
}
```

Ersetzen Sie die Werte für die folgenden Attribute gemäß Ihrer Azure Data Explorer-Einrichtung: `aad.auth.authority`, `aad.auth.appid`, `aad.auth.appkey`, `kusto.tables.topics.mapping` (Datenbankname) und `kusto.url`.

#### <a name="connector---dockerfile"></a>Connector: Dockerfile

Diese Datei enthält die Befehle zum Generieren des Docker-Images für die Connectorinstanz.  Sie enthält den Download des Connectors aus dem Releaseverzeichnis des Git-Repositorys.

#### <a name="storm-events-producer-directory"></a>Verzeichnis „Storm-events-producer“

Dieses Verzeichnis enthält ein Go-Programm, mit dem die lokale Datei „StormEvents.csv“ gelesen wird und die Daten in einem Kafka-Thema veröffentlicht werden.

#### <a name="docker-composeyaml"></a>docker-compose.yaml

```yaml
version: "2"
services:
  zookeeper:
    image: debezium/zookeeper:1.2
    ports:
      - 2181:2181
  kafka:
    image: debezium/kafka:1.2
    ports:
      - 9092:9092
    links:
      - zookeeper
    depends_on:
      - zookeeper
    environment:
      - ZOOKEEPER_CONNECT=zookeeper:2181
      - KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092
  kusto-connect:
    build:
      context: ./connector
      args:
        KUSTO_KAFKA_SINK_VERSION: 1.0.1
    ports:
      - 8083:8083
    links:
      - kafka
    depends_on:
      - kafka
    environment:
      - BOOTSTRAP_SERVERS=kafka:9092
      - GROUP_ID=adx
      - CONFIG_STORAGE_TOPIC=my_connect_configs
      - OFFSET_STORAGE_TOPIC=my_connect_offsets
      - STATUS_STORAGE_TOPIC=my_connect_statuses
  events-producer:
    build:
      context: ./storm-events-producer
    links:
      - kafka
    depends_on:
      - kafka
    environment:
      - KAFKA_BOOTSTRAP_SERVER=kafka:9092
      - KAFKA_TOPIC=storm-events
      - SOURCE_FILE=StormEvents.csv
```

### <a name="start-the-containers"></a>Starten der Container

1. Starten Sie die Container in einem Terminal:
    
    ```shell
    docker-compose up
    ```

    Die Produceranwendung beginnt mit dem Senden von Ereignissen an das Thema `storm-events`. 
    Es sollten Protokolle angezeigt werden, die den folgenden Protokollen ähneln:

    ```shell
    ....
    events-producer_1  | sent message to partition 0 offset 0
    events-producer_1  | event  2007-01-01 00:00:00.0000000,2007-01-01 00:00:00.0000000,13208,NORTH CAROLINA,Thunderstorm Wind,Public
    events-producer_1  | 
    events-producer_1  | sent message to partition 0 offset 1
    events-producer_1  | event  2007-01-01 00:00:00.0000000,2007-01-01 05:00:00.0000000,23358,WISCONSIN,Winter Storm,COOP Observer
    ....
    ```
    
1. Führen Sie den folgenden Befehl in einem separaten Terminal aus, um die Protokolle zu überprüfen:

    ```shell
    docker-compose logs -f | grep kusto-connect
    ```
    
### <a name="start-the-connector"></a>Starten des Connectors

Verwenden Sie einen Kafka Connect-REST-Aufruf, um den Connector zu starten.

1. Starten Sie den Senkentask in einem separaten Terminal, indem Sie den folgenden Befehl ausführen:

    ```shell
    curl -X POST -H "Content-Type: application/json" --data @adx-sink-config.json http://localhost:8083/connectors
    ```
    
1. Führen Sie zum Überprüfen des Status den folgenden Befehl in einem separaten Terminal aus:
    
    ```shell
    curl http://localhost:8083/connectors/storm/status
    ```

Der Connector beginnt damit, Erfassungsprozesse für Azure Data Explorer in die Warteschlange einzureihen.

> [!NOTE]
> [Erstellen Sie eine Problemmeldung](https://github.com/Azure/kafka-sink-azure-kusto/issues), falls bei Ihnen Probleme mit dem Protokollconnector auftreten.

## <a name="query-and-review-data"></a>Abfragen und Überprüfen von Daten

### <a name="confirm-data-ingestion"></a>Bestätigen der Datenerfassung

1. Warten Sie, bis Daten in der Tabelle `Storms` angezeigt werden. Überprüfen Sie die Zeilenanzahl, um die Übertragung der Daten zu bestätigen:
    
    ```kusto
    Storms | count
    ```

1. Vergewissern Sie sich, dass der Erfassungsprozess keine Fehler aufweist:

    ```kusto
    .show ingestion failures
    ```
    
    Probieren Sie einige Abfragen aus, nachdem Daten angezeigt werden. 

### <a name="query-the-data"></a>Abfragen der Daten

1. Führen Sie die folgende [Abfrage](write-queries.md) aus, um alle Datensätze anzuzeigen:
    
    ```kusto
    Storms
    ```

1. Verwenden Sie `where` und `project`, um die spezifischen Daten zu filtern:
    
    ```kusto
    Storms
    | where EventType == 'Drought' and State == 'TEXAS'
    | project StartTime, EndTime, Source, EventId
    ```
    
1. Verwenden Sie den Operator [`summarize`](./write-queries.md#summarize):

    ```kusto
    Storms
    | summarize event_count=count() by State
    | where event_count > 10
    | project State, event_count
    | render columnchart
    ```
    
    :::image type="content" source="media/ingest-data-kafka/kusto-query.png" alt-text="Diagramm: Ergebnisse der Kafka-Abfragespalte in Azure Data Explorer":::

Weitere Abfragebeispiele und eine Anleitung finden Sie unter [Schreiben von Abfragen für Azure Data Explorer](write-queries.md) und in der [Dokumentation zur Kusto-Abfragesprache](./kusto/query/index.md).

## <a name="reset"></a>Reset

Führen Sie zum Zurücksetzen die folgenden Schritte aus:

1. Anhalten der Container (`docker-compose down -v`)
1. Löschen (`drop table Storms`)
1. Neuerstellen der Tabelle `Storms`
1. Neuerstellen der Tabellenzuordnung
1. Neustarten von Containern (`docker-compose up`)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Verwenden Sie zum Löschen der Azure Data Explorer-Ressourcen [az cluster delete](/cli/azure/kusto/cluster#az-kusto-cluster-delete) oder [az Kusto database delete](/cli/azure/kusto/database#az-kusto-database-delete):

```azurecli-interactive
az kusto cluster delete -n <cluster name> -g <resource group name>
az kusto database delete -n <database name> --cluster-name <cluster name> -g <resource group name>
```

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über die [Big Data-Architektur](/azure/architecture/solution-ideas/articles/big-data-azure-data-explorer).
* Informieren Sie sich über das [Erfassen von Beispieldaten im JSON-Format in Azure Data Explorer](./ingest-json-formats.md?tabs=kusto-query-language).
* Für zusätzliche Kafka-Labs:
   * [Praxislab zur Erfassung aus Confluent Cloud Kafka im verteilten Modus](https://github.com/Azure/azure-kusto-labs/blob/master/kafka-integration/confluent-cloud/README.md)
   * [Praxislab zur Erfassung aus HDInsight Kafka im verteilten Modus](https://github.com/Azure/azure-kusto-labs/tree/master/kafka-integration/distributed-mode/hdinsight-kafka)
   * [Praxislab zur Erfassung aus Confluent IaaS Kafka in AKS im verteilten Modus](https://github.com/Azure/azure-kusto-labs/blob/master/kafka-integration/distributed-mode/confluent-kafka/README.md)
