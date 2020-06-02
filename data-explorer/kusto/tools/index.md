---
title: Azure Data Explorer-Tools – Azure Data Explorer | Microsoft-Dokumentation
description: In diesem Artikel werden die Azure Data Explorer-Tools beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: b6bc95158c1dd161a17572342c6a99bdf9d37235
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257908"
---
# <a name="azure-data-explorer-tools"></a>Azure Data Explorer-Tools

## <a name="ad-hoc-query-tools"></a>Ad-hoc-Abfragetools

* Kusto.Explorer
   * [Installation und Benutzeroberfläche von Kusto.Explorer:](./kusto-explorer.md) Das primäre Desktoptool zum Abfragen und Steuern von Kusto
   * [Verwenden von Kusto.Explorer](./kusto-explorer-using.md)
   * [Problembehandlung für Kusto.Explorer](kusto-explorer-troubleshooting.md)
* [Webbenutzeroberfläche](../../web-query-data.md): Webbenutzeroberfläche zum Abfragen von Kusto

## <a name="visualizations-dashboards-and-reporting-tools"></a>Visualisierungen, Dashboards und Berichterstellungstools


* [Azure Notebooks](../../azure-notebooks.md): Verwenden Sie Azure Notebooks zum Analysieren von Daten in Azure Data Explorer.
* Excel
    * [Leere Excel-Abfrage](../../excel-blank-query.md): Ermöglicht das Hinzufügen einer Kusto-Abfrage als Excel-Datenquelle
    * [Excel-Connector](../../excel-connector.md): Ein Excel-Connector für Azure Data Explorer 

* PowerBI

   * [Bewährte Methoden für Power BI](../../power-bi-best-practices.md)
   * [PowerBI-Connector](../../power-bi-connector.md)
   * [Importierte Power BI-Abfrage](../../power-bi-imported-query.md) 
   * [Power BI-SQL-Abfrage](../../power-bi-sql-query.md)

* [Grafana](../../grafana.md)
* [Open-Source-Connector K2Bridge:](../../k2bridge.md) Visualisieren von Daten aus Azure Data Explorer in Kibana

## <a name="orchestration-tools"></a>Orchestrierungstools


* Microsoft Flow
    * [Microsoft Flow-Connector](../../flow.md)
    * [Verwendungsbeispiele für den Microsoft Flow-Connector](../../flow-usage.md)
* [Azure Logic Apps](./logicapps.md): Ermöglicht das automatische Ausführen von Kusto-Abfragen im Rahmen von [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)



## <a name="data-ingestion-tools"></a>Datenerfassungstools


* [LightIngest](../../lightingest.md): Hilfsprogramm für die Ad-hoc-Datenerfassung in Azure Data Explorer
* [1-Klick-Erfassung:](../../ingest-data-one-click.md): Tool zum schnellen Erfassen von Daten und automatischen Vorschlagen von Tabellen und Zuordnungsstrukturen
* [Azure Data Factory](azure-data-factory.md)


## <a name="source-control-integration-tools"></a>Tools für die Integration der Quellcodeverwaltung

* [Azure Pipelines](../../devops.md): Ermöglicht das Aufrufen von Steuerungsbefehlen im Rahmen Ihrer Pipeline
* [Sync Kusto](./synckusto.md): Ermöglicht das Synchronisieren gespeicherter Kusto-Funktionen mit/aus Git
