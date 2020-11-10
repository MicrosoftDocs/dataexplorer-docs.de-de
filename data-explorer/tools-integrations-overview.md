---
title: Übersicht über Azure Data Explorer-Tools und -Integrationen – Azure Data Explorer
description: In diesem Artikel werden Tools und Integrationen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: olgolden
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 71f00cf921609432860a94061be7411e484fd0c9
ms.sourcegitcommit: 455d902bad0aae3e3d72269798c754f51442270e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349323"
---
# <a name="azure-data-explorer-tools-and-integrations-overview"></a>Übersicht über Azure Data Explorer-Tools und -Integrationen

Azure Data Explorer ist ein schneller, vollständig verwalteter Datenanalysedienst für Echtzeitanalysen großer Datenmengen, die von Anwendungen, Websites, IoT-Geräten usw. gestreamt werden. Azure Data Explorer sammelt, speichert und analysiert verschiedene Daten, um Produkte zu verbessern, die Benutzerfreundlichkeit zu optimieren, Geräte zu überwachen und Vorgänge zu beschleunigen. 

Azure Data Explorer bietet verschiedene Tools und Integrationen für die Datenerfassung, Abfragen, Visualisierung, Orchestrierung und vieles mehr. Neben den systemeigenen Diensten bietet Azure Data Explorer Benutzern die einfache Integration in verschiedene Produkte und Plattformen, die Ermöglichung verschiedener Kundenanwendungsfälle, die Optimierung des Geschäftsprozesses durch effizientere Workflows und die Senkung von Kosten. 

Dieser Artikel enthält eine Liste der Azure Data Explorer-Tools, -Connectors und -Integrationen mit Links zu relevanten Dokumenten, um weitere Informationen zu erhalten.

## <a name="ingest-data"></a>Erfassen von Daten 

Die Datenerfassung ist der Prozess, mit dem Datensätze aus einer oder mehreren Quellen in Azure Data Explorer geladen werden. Nach der Erfassung sind die Daten für Abfragen verfügbar. Azure Data Explorer bietet verschiedene Tools und Connectors für die Datenerfassung. 

### <a name="azure-data-explorer-ingestion-tools"></a>Datenerfassungstools von Azure Data Explorer

* [LightIngest](lightingest.md): Hilfsprogramm für die Ad-hoc-Datenerfassung in Azure Data Explorer
* 1-Klick-Erfassung: [Übersicht](ingest-data-one-click.md) Erfassen von Daten [aus einem Container in einer neuen Tabelle](one-click-ingestion-new-table.md) oder [aus einer lokalen Datei in einer vorhandenen Tabelle](one-click-ingestion-existing-table.md)

### <a name="ingestion-integrations"></a>Erfassungsintegrationen

* Event Hub: [Übersicht über die Erfassung aus Event Hub](ingest-data-event-hub-overview.md) und Verwendung von [Azure-Portal](ingest-data-event-hub.md), [C#](data-connection-event-hub-csharp.md), [Python](data-connection-event-hub-python.md) oder einer [Azure Resource Manager-Vorlage](data-connection-event-hub-resource-manager.md)
* Event Grid: [Übersicht über die Erfassung aus Event Grid](ingest-data-event-grid-overview.md) und Verwendung von [Azure-Portal](ingest-data-event-grid.md), [C#](data-connection-event-grid-csharp.md), [Python](data-connection-event-grid-python.md) oder einer [Azure Resource Manager-Vorlage](data-connection-event-grid-resource-manager.md)
* IoT Hub: [Übersicht über die Erfassung aus IoT Hub](ingest-data-iot-hub-overview.md) und Verwendung von [Azure-Portal](ingest-data-iot-hub.md), [C#](data-connection-iot-hub-csharp.md), [Python](data-connection-iot-hub-python.md) oder einer [Azure Resource Manager-Vorlage](data-connection-iot-hub-resource-manager.md)
* [Logstash](ingest-data-logstash.md)
* Azure Data Factory: [Übersicht über die Integration](data-factory-integration.md), [Kopieren von Daten](data-factory-load-data.md), [Massenkopieren mit der Azure Data Factory-Vorlage](data-factory-template.md) und [Ausführen von Azure Data Explorer-Steuerungsbefehlen per Azure Data Factory-Befehlsaktivität](data-factory-command-activity.md)
* [Verbinden mit Azure Data Explorer per Synapse Apache Spark](/azure/synapse-analytics/quickstart-connect-azure-data-explorer?context=%252fazure%252fdata-explorer%252fcontext%252fcontext)
* [Apache Spark](spark-connector.md)
* [Apache Kafka](ingest-data-kafka.md)
* [Cosmos DB](https://github.com/Azure/azure-kusto-labs/tree/master/cosmosdb-adx-integration)
* [Power Automate](flow.md)

## <a name="query-data"></a>Daten abfragen

### <a name="azure-data-explorer-query-tools"></a>Azure Data Explorer-Abfragetools

Zum Ausführen von Abfragen in Azure Data Explorer stehen verschiedene Tools zur Verfügung.

* Kusto.Explorer
    * [Installation und Benutzeroberfläche](kusto/tools/kusto-explorer.md), [Verwenden von Kusto.Explorer](kusto/tools/kusto-explorer-using.md)
    * Zu den zusätzlichen Themen zählen [Optionen](kusto/tools/kusto-explorer-options.md), [Problembehandlung](kusto/tools/kusto-explorer-troubleshooting.md), [Tastenkombinationen](kusto/tools/kusto-explorer-shortcuts.md), [Code-Refactoring](kusto/tools/kusto-explorer-refactor.md), [Codenavigation](kusto/tools/kusto-explorer-codenav.md) und [Codeanalyse](kusto/tools/kusto-explorer-code-analyzer.md).
* [Web-Benutzeroberfläche](web-query-data.md)
* [Kusto.Cli](kusto/tools/kusto-cli.md)

### <a name="query-integrations"></a>Abfrageintegrationen

* [Azure Monitor](query-monitor-data.md)
* [Azure Data Lake](data-lake-query-data.md)
* [Verbinden mit Azure Data Explorer per Synapse Apache Spark](/azure/synapse-analytics/quickstart-connect-azure-data-explorer?context=%252fazure%252fdata-explorer%252fcontext%252fcontext)
* [Apache Spark](spark-connector.md)
* [Microsoft Power Apps](power-apps-connector.md)
* Azure Data Studio: [Übersicht über Kusto-Erweiterung](/sql/azure-data-studio/extensions/kusto-extension?context=%252fazure%252fdata-explorer%252fcontext%252fcontext), [Verwenden von Kusto](/sql/azure-data-studio/notebooks/notebooks-kusto-kernel?context=%252fazure%252fdata-explorer%252fcontext%252fcontext) und [Verwenden von Kqlmagic](/sql/azure-data-studio/notebooks-kqlmagic?context=%252fazure%252fdata-explorer%252fcontext%252fcontext)

## <a name="visualizations-dashboards-and-reporting"></a>Visualisierungen, Dashboards und Berichterstellung

Die Übersicht über die [Visualisierung](viz-overview.md) enthält Details zu Datenvisualisierung, Dashboards und Berichterstellungsoptionen. 

## <a name="notebook-connectivity"></a>Notebook-Konnektivität

* [Azure Notebooks](/sql/azure-data-studio/notebooks/notebooks-kqlmagic?context=%252fazure%252fdata-explorer%252fcontext%252fcontext%253fcontext%253d%252fazure%252fdata-explorer%252fcontext%252fcontext)
* [Jupyter-Notebooks](kqlmagic.md)
* Azure Data Studio: [Übersicht über Kusto-Erweiterung](/sql/azure-data-studio/extensions/kusto-extension?context=%252fazure%252fdata-explorer%252fcontext%252fcontext), [Verwenden von Kusto](/sql/azure-data-studio/notebooks/notebooks-kusto-kernel?context=%252fazure%252fdata-explorer%252fcontext%252fcontext) und [Verwenden von Kqlmagic](/sql/azure-data-studio/notebooks-kqlmagic?context=%252fazure%252fdata-explorer%252fcontext%252fcontext)

## <a name="orchestration"></a>Orchestrierung

* Power Automate
    * [Power Automate-Connector](flow.md)
    * [Verwendungsbeispiele für den Power Automate-Connector](flow-usage.md)
* [Microsoft-Logik-App](kusto/tools/logicapps.md) 
* [Azure Data Factory](data-factory-integration.md)

## <a name="share-data"></a>Freigeben von Daten

* [Azure Data Share](data-share.md)

## <a name="source-control-integration"></a>Integration der Quellcodeverwaltung

* [Azure Pipelines](devops.md) 
* [Synchronisieren von Kusto](kusto/tools/synckusto.md) 

<!--Open Source Tools-->