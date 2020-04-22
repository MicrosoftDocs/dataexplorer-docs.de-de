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
ms.openlocfilehash: e9b274ae129f7cd15ba30edb24f8432c738f55ab
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490649"
---
# <a name="azure-data-explorer-tools"></a>Azure Data Explorer-Tools

## <a name="ad-hoc-query-tools"></a>Ad-hoc-Abfragetools


* [Kusto.Explorer](./kusto-explorer.md): Das primäre Desktoptool zum Abfragen und Steuern von Kusto
* [Webbenutzeroberfläche](https://docs.microsoft.com/azure/data-explorer/web-query-data): Webbenutzeroberfläche zum Abfragen von Kusto

## <a name="visualizations-dashboards-and-reporting-tools"></a>Visualisierungen, Dashboards und Berichterstellungstools


* [Azure Notebooks](https://docs.microsoft.com/azure/data-explorer/azure-notebooks): Verwenden Sie Azure Notebooks zum Analysieren von Daten in Azure Data Explorer.
* Excel
    * [Leere Excel-Abfrage](https://docs.microsoft.com/azure/data-explorer/excel-blank-query): Ermöglicht das Hinzufügen einer Kusto-Abfrage als Excel-Datenquelle
    * [Excel-Connector](https://docs.microsoft.com/azure/data-explorer/excel-connector): Ein Excel-Connector für Azure Data Explorer 

* PowerBI

   * [Bewährte Methoden für Power BI](https://docs.microsoft.com/azure/data-explorer/power-bi-best-practices)
   * [PowerBI-Connector](https://docs.microsoft.com/azure/data-explorer/power-bi-connector)
   * [Importierte Power BI-Abfrage](https://docs.microsoft.com/azure/data-explorer/power-bi-imported-query) 
   * [Power BI-SQL-Abfrage](https://docs.microsoft.com/azure/data-explorer/power-bi-sql-query)

* [Grafana](https://docs.microsoft.com/azure/data-explorer/grafana)

## <a name="orchestration-tools"></a>Orchestrierungstools


* Microsoft Flow
    * [Microsoft Flow-Connector](https://docs.microsoft.com/azure/data-explorer/flow)
    * [Verwendungsbeispiele für den Microsoft Flow-Connector](https://docs.microsoft.com/azure/data-explorer/flow-usage)
* [Azure Logic Apps](./logicapps.md): Ermöglicht das automatische Ausführen von Kusto-Abfragen im Rahmen von [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)



## <a name="data-ingestion-tools"></a>Datenerfassungstools


* [LightIngest](https://docs.microsoft.com/azure/data-explorer/lightingest): Hilfsprogramm für die Ad-hoc-Datenerfassung in Azure Data Explorer
 



## <a name="source-control-integration-tools"></a>Tools für die Integration der Quellcodeverwaltung

* [Azure Pipelines](https://docs.microsoft.com/azure/data-explorer/devops): Ermöglicht das Aufrufen von Steuerungsbefehlen im Rahmen Ihrer Pipeline
* [Sync Kusto](./synckusto.md): Ermöglicht das Synchronisieren gespeicherter Kusto-Funktionen mit/aus Git
