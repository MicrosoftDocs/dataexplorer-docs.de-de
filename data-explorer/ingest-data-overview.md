---
title: Übersicht über die Datenerfassung in Azure Data Explorer
description: Erfahren Sie mehr über die verschiedenen Möglichkeiten, wie Sie Daten im Azure-Daten-Explorer erfassen (laden) können.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/18/2020
ms.localizationpriority: high
ms.openlocfilehash: 5304d2fcce23d6143faebb9326a6ab960a964f22
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512399"
---
# <a name="azure-data-explorer-data-ingestion-overview"></a>Übersicht über die Datenerfassung in Azure Data Explorer 

Die Datenerfassung ist der Prozess, mit dem Datensätze aus einer oder mehreren Quellen geladen werden, um Daten in eine Tabelle in Azure Data Explorer zu importieren. Nach der Erfassung sind die Daten für Abfragen verfügbar.

Das folgende Diagramm zeigt den vollständigen Flow für die Arbeit in Azure Data Explorer, einschließlich der unterschiedlichen Erfassungsmethoden.

:::image type="content" source="media/data-ingestion-overview/data-management-and-ingestion-overview.png" alt-text="Übersichtsschema der Datenerfassung und -verwaltung":::

Der Datenverwaltungsdienst von Azure Data Explorer, der für die Datenerfassung zuständig ist, implementiert die folgenden Prozesse:

Azure Data Explorer pullt Daten aus einer externen Quelle und liest Anforderungen aus einer ausstehenden Azure-Warteschlange. Die Daten werden in einem Batch oder Stream an Data Manager übermittelt. Batchdaten, die an dieselbe Datenbank und Tabelle gesendet werden, sind für den Durchsatz der Datenerfassung optimiert. Azure Data Explorer führt eine erste Überprüfung der Daten durch und konvertiert ggf. Datenformate. Die weitere Datenbearbeitung umfasst den Schemaabgleich sowie die Organisation, Indizierung, Codierung und Komprimierung der Daten. Die Daten werden gemäß der festgelegten Aufbewahrungsrichtlinie im Speicher gespeichert. Der Data Manager committet anschließend die Datenerfassung in der-Engine, wo sie dann für Abfragen bereitstehen. 

## <a name="supported-data-formats-properties-and-permissions"></a>Unterstützte Datenformate, Eigenschaften und Berechtigungen

* **[Unterstützte Datenformate](ingestion-supported-formats.md)** 

* **[Erfassungseigenschaften:](ingestion-properties.md)** Eigenschaften, die Einfluss auf die Datenerfassung haben (z. B. Tagging, Zuordnung, Erstellungszeit).

* **Berechtigungen**: Zum Erfassen von Daten benötigt der Prozess [Berechtigungen auf Ebene der Datenbankerfassung](kusto/management/access-control/role-based-authorization.md). Für andere Aktionen wie z. B. Abfragen sind möglicherweise die Berechtigungen „Datenbankadministrator“, „Datenbankbenutzer“ oder „Tabellenadministrator“ erforderlich.

## <a name="batching-vs-streaming-ingestion"></a>Batcherfassung und Streamingerfassung

* Bei der Batcherfassung werden die Daten in Batches zusammengefasst und für einen hohen Erfassungsdurchsatz optimiert. Diese Methode stellt die bevorzugte und leistungsfähigste Art der Erfassung dar. Die Daten werden gemäß den Erfassungseigenschaften in Batches zusammengefasst. Anschließend werden kleine Datenbatches zusammengeführt und für schnelle Abfrageergebnisse optimiert. Die Richtlinie für die [Batcherfassung](kusto/management/batchingpolicy.md) kann für Datenbanken oder Tabellen festgelegt werden. Standardmäßig beträgt der Maximalwert für Batches 5 Minuten, 1.000 Elemente oder eine Gesamtgröße von 1 GB.

* Die [Streamingerfassung](ingest-data-streaming.md) erfolgt durch eine fortlaufende Datenerfassung aus einer Streamingquelle. Durch die Streamingerfassung sinkt die Latenz bei kleinen Datensätzen pro Tabelle auf nahezu Echtzeit. Die Daten werden anfänglich im Zeilenspeicher erfasst und dann in Spaltenspeichererweiterungen verschoben. Die Streamingerfassung kann mithilfe einer Azure Data Explorer-Clientbibliothek oder einer der unterstützten Datenpipelines erfolgen. 

## <a name="ingestion-methods-and-tools"></a>Erfassungsmethoden und -tools

Azure Data Explorer unterstützt mehrere Erfassungsmethoden mit jeweils eigenen Zielszenarien. Diese Methoden umfassen Erfassungstools, Connectors und Plug-Ins für verschiedene Dienste, verwaltete Pipelines, die programmgesteuerte Erfassung mithilfe von SDKs und Direktzugriff auf die Erfassung.

### <a name="ingestion-using-managed-pipelines"></a>Erfassung mit verwalteten Pipelines

Für Organisationen, die eine Verwaltung (Drosselung, Wiederholungen, Überwachungen, Warnungen usw.) über einen externen Dienst durchführen möchten, stellt die Verwendung eines Connectors wahrscheinlich die geeignetste Lösung dar. Die Erfassung in der Warteschlange eignet sich für große Datenmengen. Azure Data Explorer unterstützt die folgenden Azure Pipelines:

* **[Event Grid:](https://azure.microsoft.com/services/event-grid/)** Eine Pipeline, die auf Azure Storage lauscht und Azure Data Explorer aktualisiert, um Informationen zu pullen, wenn abonnierte Ereignisse auftreten. Weitere Informationen finden Sie unter [Erfassen von Azure-Blobs in Azure Data Explorer](ingest-data-event-grid.md).

* **[Event Hub:](https://azure.microsoft.com/services/event-hubs/)** Eine Pipeline, die Ereignisse von Diensten an Azure Data Explorer überträgt. Weitere Informationen finden Sie unter [Erfassen von Daten aus Event Hub in Azure Data Explorer](ingest-data-event-hub.md).

* **[IoT Hub](https://azure.microsoft.com/services/iot-hub/)** : Eine Pipeline, die für die Übertragung von Daten von unterstützten IoT-Geräten an Azure Data Explorer verwendet wird. Weitere Informationen finden Sie unter [Erfassen von IoT Hub](ingest-data-iot-hub.md).

* **Azure Data Factory (ADF):** Ein vollständig verwalteter Datenintegrationsdienst für analytische Workloads in Azure. Azure Data Factory stellt eine Verbindung mit über 90 unterstützten Quellen her, um eine effiziente und resiliente Datenübertragung zu ermöglichen. ADF führt die Vorbereitung, Transformation und Anreicherung der Daten durch, um Erkenntnisse zu erhalten, die auf unterschiedliche Art und Weise überwacht werden können. Dieser Dienst kann als einmalige Lösung verwendet, periodisch ausgeführt oder durch bestimmte Ereignisse ausgelöst werden. 
  * [Integrieren von Azure Data Explorer und Azure Data Factory](data-factory-integration.md)
  * [Kopieren von Daten aus unterstützten Quellen in Azure Data Explorer mithilfe von Azure Data Factory](./data-factory-load-data.md)
  * [Massenkopieren aus einer Datenbank in Azure Data Explorer mithilfe der Azure Data Factory-Vorlage](data-factory-template.md)
  * [Verwenden der Azure Data Factory-Befehlsaktivität zum Ausführen von Azure Data Explorer-Steuerungsbefehlen](data-factory-command-activity.md)

### <a name="ingestion-using-connectors-and-plugins"></a>Erfassung mit Konnektoren und Plug-Ins

* **Logstash-Plug-In**, siehe [Erfassen von Daten aus Logstash in Azure Data Explorer](ingest-data-logstash.md).

* **Kafka-Connector**, siehe [Erfassen von Daten aus Kafka in Azure Data Explorer](ingest-data-kafka.md).

* **[Power Automate:](https://flow.microsoft.com/)** Eine automatisierte Workflow-Pipeline zu Azure Data Explorer. Mit Power Automate können Sie eine Abfrage ausführen und vordefinierte Aktionen ausführen, für die Sie die Abfrageergebnisse als Trigger verwenden. Weitere Informationen finden Sie unter [Azure Data Explorer-Connector für Power Automate (Vorschauversion)](flow.md).

* **Apache Spark-Connector:**  Ein Open-Source-Projekt, das in jedem Spark-Cluster ausgeführt werden kann. Er implementiert Datenquellen und Datensenken zum Verschieben von Daten zwischen Azure Data Explorer und Spark-Clustern. Sie können damit schnelle und skalierbare Anwendungen für datengesteuerte Szenarien erstellen. Weitere Informationen finden Sie unter [Azure Data Explorer-Connector für Apache Spark](spark-connector.md).

### <a name="programmatic-ingestion-using-sdks"></a>Programmgesteuerte Erfassung mit SDKs

Azure-Daten-Explorer bietet SDKs, die für Abfragen und die Datenerfassung verwendet werden können. Die programmgesteuerte Erfassung ist dafür optimiert, die Erfassungskosten (COGs) zu senken, indem Speichertransaktionen während und nach dem Erfassungsprozess minimiert werden.

**Verfügbare SDKs und Open-Source-Projekte**

* [Python SDK](kusto/api/python/kusto-python-client-library.md)

* [.NET SDK](kusto/api/netfx/about-the-sdk.md)

* [Java SDK](kusto/api/java/kusto-java-client-library.md)

* [Node SDK](kusto/api/node/kusto-node-client-library.md)

* [REST-API](kusto/api/netfx/kusto-ingest-client-rest.md)

* [GO-API](kusto/api/golang/kusto-golang-client-library.md)

### <a name="tools"></a>Tools

* **[1-Klick-Erfassung:](ingest-data-one-click.md)** Ermöglicht das schnelle Erfassen von Daten durch Erstellen und Anpassen von Tabellen aus einer breiten Palette von Quelltypen. Bei der 1-Klick-Erfassung werden basierend auf der Datenquelle in Azure Data Explorer automatisch Tabellen und Zuordnungsstrukturen vorschlagen. Sie können die 1-Klick-Erfassung für die einmalige Erfassung verwenden oder zum Definieren einer kontinuierlichen Erfassung über Event Grid in dem Container, in dem die Daten erfasst wurden.

* **[LightIngest:](lightingest.md)** Ein Befehlszeilen-Hilfsprogramm für die Ad-hoc-Datenerfassung in Azure Data Explorer. Das Hilfsprogramm kann Quelldaten aus einem lokalen Ordner oder aus einem Azure Blob Storage-Container abrufen.

### <a name="kusto-query-language-ingest-control-commands"></a>Befehle zur Steuerung der Erfassung in der Kusto-Abfragesprache

Es gibt eine Reihe von Methoden, mit denen Daten mithilfe von KQL-Befehlen (Kusto Query Language) direkt in der Engine erfasst werden können. Da diese Methode die Datenverwaltungsdienste umgeht, eignet sie sich nur für die Erkundung und Prototyperstellung. Verwenden Sie diese Methode nicht in Produktionsszenarien oder Szenarien mit hohem Volumen.

  * **Inlineerfassung:**  An die Engine wird ein Steuerungsbefehl ([.ingest inline](kusto/management/data-ingestion/ingest-inline.md)) gesendet, und die zu erfassenden Daten werden direkt im Befehlstext angegeben. Diese Methode ist für improvisierte Testzwecke vorgesehen.

  * **Erfassung aus einer Abfrage:** An die Engine wird ein Steuerungsbefehl ([„.set“, „.append“, „.set-or-append“ oder „.set-or-replace“](kusto/management/data-ingestion/ingest-from-query.md)) gesendet, und die Daten werden indirekt als die Ergebnisse einer Abfrage oder eines Befehls angegeben.

  * **Erfassung aus dem Speicher (Pull):** An die Engine wird ein Steuerungsbefehl ([.ingest into](kusto/management/data-ingestion/ingest-from-storage.md)) gesendet, und die Daten sind in einem externen Speicher (beispielsweise in Azure Blob Storage) gespeichert, auf den die Engine zugreifen kann und auf den durch den Befehl verwiesen wird.

## <a name="comparing-ingestion-methods-and-tools"></a>Vergleich der Erfassungsmethoden und -tools

| Erfassungsname | Datentyp | Maximale Dateigröße | Streaming, Batch, direkt | Häufige Szenarien | Überlegungen |
| --- | --- | --- | --- | --- | --- |
| [**1-Klick-Erfassung**](ingest-data-one-click.md) | *sv, JSON | 1 GB unkomprimiert (siehe Hinweis)| Batcherfassung in Container, lokale Datei und Blob bei direkter Erfassung | Einmalig, Tabellenschema erstellen, kontinuierliche Erfassung mit Event Grid definieren, Massenerfassung mit Container (bis zu 10.000 Blobs) | 10.000 Blobs werden nach dem Zufallsprinzip aus dem Container ausgewählt|
| [**LightIngest**](lightingest.md) | Alle Formate unterstützt | 1 GB unkomprimiert (siehe Hinweis) | Batcherfassung über DM oder direkte Erfassung in Engine |  Datenmigration, Verlaufsdaten mit angepassten Erfassungszeitstempeln, Massenerfassung (keine Größenbeschränkung)| Beachtung von Groß-/Kleinschreibung, platzintensiv |
| [**ADX Kafka**](ingest-data-kafka.md) | | | | |
| [**ADX in Apache Spark**](spark-connector.md) | | | | |
| [**LogStash**](ingest-data-logstash.md) | | | | |
| [**Azure Data Factory**](./data-factory-integration.md) | [Unterstützte Datenformate](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | unbegrenzt *(nach ADF-Einschränkungen) | Batcherfassung oder mit ADF-Trigger | Unterstützt Formate, die in der Regel nicht unterstützt werden, große Dateien, kann von über 90 Quellen kopieren, von lokal in die Cloud | Zeit der Erfassung |
|[ **Azure Data Flow**](./flow.md) | | | | Erfassungsbefehle als Teil des Flows| Erfordert Antwortzeit mit hoher Leistung |
| [**IoT Hub**](ingest-data-iot-hub-overview.md) | [Unterstützte Datenformate](ingest-data-iot-hub-overview.md#data-format)  | – | Batcherfasung, Streaming | IoT-Nachrichten, IoT-Ereignisse, IoT-Eigenschaften | |
| [**Event Hub**](ingest-data-event-hub-overview.md) | [Unterstützte Datenformate](ingest-data-event-hub-overview.md#data-format) | – | Batcherfasung, Streaming | Nachrichten, Ereignisse | |
| [**Event Grid**](ingest-data-event-grid-overview.md) | [Unterstützte Datenformate](ingest-data-event-grid-overview.md#data-format) | 1 GB unkomprimiert | Batching | Kontinuierliche Erfassung aus Azure Storage, externe Daten in Azure Storage | 100 KB als optimale Dateigröße, für Blobumbenennung und -erstellung verwendet |
| [**.NET SDK**](./net-sdk-ingest-data.md) | Alle Formate unterstützt | 1 GB unkomprimiert (siehe Hinweis) | Batcherfassung, Streaming, direkt | Schreiben von eigenem Code nach Anforderungen der Organisation |
| [**Python**](python-ingest-data.md) | Alle Formate unterstützt | 1 GB unkomprimiert (siehe Hinweis) | Batcherfassung, Streaming, direkt | Schreiben von eigenem Code nach Anforderungen der Organisation |
| [**Node.js**](node-ingest-data.md) | Alle Formate unterstützt | 1 GB unkomprimiert (siehe Hinweis) | Batcherfassung, Streaming, direkt | Schreiben von eigenem Code nach Anforderungen der Organisation |
| [**Java**](kusto/api/java/kusto-java-client-library.md) | Alle Formate unterstützt | 1 GB unkomprimiert (siehe Hinweis) | Batcherfassung, Streaming, direkt | Schreiben von eigenem Code nach Anforderungen der Organisation |
| [**REST**](kusto/api/netfx/kusto-ingest-client-rest.md) | Alle Formate unterstützt | 1 GB unkomprimiert (siehe Hinweis) | Batcherfassung, Streaming, direkt| Schreiben von eigenem Code nach Anforderungen der Organisation |
| [**Go**](kusto/api/golang/kusto-golang-client-library.md) | Alle Formate unterstützt | 1 GB unkomprimiert (siehe Hinweis) | Batcherfassung, Streaming, direkt | Schreiben von eigenem Code nach Anforderungen der Organisation |

> [!Note] 
> Wenn in der obigen Tabelle darauf verwiesen wird, unterstützt die Erfassung eine maximale Dateigröße von 4 GB. Es wird empfohlen, Dateien zwischen 100 MB und 1 GB zu erfassen.

## <a name="ingestion-process"></a>Erfassungsprozess

Nachdem Sie die am besten geeignete Erfassungsmethode für Ihre Anforderungen ausgewählt haben, führen Sie die folgenden Schritte aus:

1. **Festlegen der Aufbewahrungsrichtlinie**

    Für Daten, die in einer Tabelle in Azure Data Explorer erfasst werden, gilt die effektive Aufbewahrungsrichtlinie der Tabelle. Wurde für eine Tabelle nicht explizit eine effektive Aufbewahrungsrichtlinie festgelegt, wird sie von der Aufbewahrungsrichtlinie der Datenbank abgeleitet. Die heiße Speicherebene für die Aufbewahrung ist eine Funktion aus der Clustergröße und Ihrer Aufbewahrungsrichtlinie. Wenn Sie mehr Daten erfassen, als Speicherplatz verfügbar ist, werden die zuerst erfassten Daten in die kalte Aufbewahrung überführt.
    
    Stellen Sie sicher, dass die Aufbewahrungsrichtlinie der Datenbank Ihren Anforderungen entspricht. Ist dies nicht der Fall, überschreiben Sie sie explizit auf Tabellenebene. Weitere Informationen finden Sie unter [Aufbewahrungsrichtlinie](kusto/management/retentionpolicy.md). 
    
1. **Erstellen einer Tabelle**

    Um Daten zu erfassen, muss vorab eine Tabelle erstellt werden. Nutzen Sie eine der folgenden Optionen:
   * Erstellen einer Tabelle [mit einem Befehl](kusto/management/create-table-command.md) 
   * Erstellen einer Tabelle mit der [1-Klick-Erfassung](one-click-ingestion-new-table.md)

    > [!Note]
    > Wenn ein Datensatz unvollständig ist oder ein Feld nicht als erforderlicher Datentyp analysiert werden kann, werden die entsprechenden Tabellenspalten mit Nullwerten gefüllt.

1. **Erstellen einer Schemazuordnung**

    Die [Schemazuordnung](kusto/management/mappings.md) hilft dabei, Quelldatenfelder an Spalten der Zieltabelle zu binden. Die Zuordnung ermöglicht Ihnen, Daten aus verschiedenen Quellen basierend auf den definierten Attributen in dieselbe Tabelle zu übernehmen. Es werden unterschiedliche Zuordnungstypen unterstützt – sowohl zeilenorientiert (CSV, JSON und Avro) als auch spaltenorientiert (Parquet). Bei den meisten Methoden können Zuordnungen auch [vorab in der Tabelle erstellt](kusto/management/create-ingestion-mapping-command.md) und vom Erfassungsbefehlsparameter referenziert werden.

1. **Einrichten einer Updaterichtlinie** (optional)

   Einige der Datenformatzuordnungen (Parquet, JSON und Avro) unterstützen einfache und nützliche Transformationen während der Erfassung. Wenn für das Szenario eine komplexere Verarbeitung zur Erfassungszeit erforderlich ist, verwenden Sie eine Updaterichtlinie, die eine einfache Verarbeitung mithilfe von Befehlen der Kusto-Abfragesprache ermöglicht. Die Updaterichtlinie führt automatisch Extraktionen und Transformationen der erfassten Daten in der ursprünglichen Tabelle aus und erfasst die resultierenden Daten in einer oder mehreren Zieltabellen. Legen Sie Ihre [Updaterichtlinie](kusto/management/update-policy.md) fest.



## <a name="next-steps"></a>Nächste Schritte

* [Unterstützte Datenformate](ingestion-supported-formats.md)
* [Unterstützte Erfassungseigenschaften](ingestion-properties.md)