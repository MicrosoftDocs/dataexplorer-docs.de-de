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
ms.openlocfilehash: ce7751d7ac60d23f9ffa0fc84992050fe1036131
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83370486"
---
# <a name="azure-data-explorer-tools"></a>Azure Data Explorer-Tools

## <a name="ad-hoc-query-tools"></a>Ad-hoc-Abfragetools


* [Kusto.Explorer](./kusto-explorer.md): Das primäre Desktoptool zum Abfragen und Steuern von Kusto
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

## <a name="orchestration-tools"></a>Orchestrierungstools


* Microsoft Flow
    * [Microsoft Flow-Connector](../../flow.md)
    * [Verwendungsbeispiele für den Microsoft Flow-Connector](../../flow-usage.md)
* [Azure Logic Apps](./logicapps.md): Ermöglicht das automatische Ausführen von Kusto-Abfragen im Rahmen von [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)



## <a name="data-ingestion-tools"></a>Datenerfassungstools


* [LightIngest](../../lightingest.md): Hilfsprogramm für die Ad-hoc-Datenerfassung in Azure Data Explorer
 



## <a name="source-control-integration-tools"></a>Tools für die Integration der Quellcodeverwaltung

* [Azure Pipelines](../../devops.md): Ermöglicht das Aufrufen von Steuerungsbefehlen im Rahmen Ihrer Pipeline
* [Sync Kusto](./synckusto.md): Ermöglicht das Synchronisieren gespeicherter Kusto-Funktionen mit/aus Git
