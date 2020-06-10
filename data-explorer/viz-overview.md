---
title: Azure Data Explorer-Datenvisualisierung
description: Erfahren Sie mehr über die verschiedenen Möglichkeiten, mit denen Sie Ihre Azure Data Explorer-Daten visualisieren können
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: b1351ceb9fe4b81a818ca41728a588dddfb4c5a2
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294677"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Datenvisualisierung mit Azure Data Explorer 

Azure Data Explorer ist ein schneller und hochgradig skalierbarer Dienst für die Untersuchung von Protokoll- und Telemetriedaten, die zum Erstellen von komplexen Analyselösungen für große Datenmengen verwendet werden. Azure Data Explorer wird in verschiedene Visualisierungstools integriert, damit Sie Ihre Daten visualisieren und die Ergebnisse unternehmensweit freigeben können. Diese Daten können in umfassende Einblicke hinsichtlich der Auswirkungen auf Ihr Unternehmen transformiert werden.

Datenvisualisierung und Berichterstellung ist ein wichtiger Schritt im Datenanalyseprozess. Azure Data Explorer unterstützt viele BI-Dienste, damit Sie denjenigen nutzen können, der für Ihr Szenario und Budget am besten geeignet ist.

## <a name="azure-data-explorer-dashboards"></a>Azure Data Explorer-Dashboards

Bei den Azure Data Explorer-Dashboards handelt es sich um eine Webanwendung, die Ihnen das Ausführen von Abfragen und Erstellen von Dashboards in der eigenständigen Webanwendung ([Webbenutzeroberfläche](web-query-data.md)) ermöglicht. Azure Data Explorer-Dashboards bieten drei wichtige Vorteile:

* Natives Exportieren von Abfragen aus der Webbenutzeroberfläche in Azure Data Explorer-Dashboards 
* Untersuchen der Daten auf der Webbenutzeroberfläche
* Optimieren der Dashboardrenderingleistung

Weitere Informationen finden Sie unter [Visualisieren von Daten mit Azure Data Explorer-Dashboards](azure-data-explorer-dashboards.md).

## <a name="kusto-query-language-visualizations"></a>Visualisierungen mit der Kusto-Abfragesprache

Das Element [`render operator`](kusto/query/renderoperator.md) der Kusto-Abfragesprache verfügt über verschiedene Visualisierungen, z. B. Tabellen, Kreisdiagramme und Balkendiagramme, für die Darstellung von Abfrageergebnissen. Abfragevisualisierungen sind hilfreich bei Anomalieerkennung und -vorhersage, maschinellem Lernen und vielem mehr.

## <a name="power-bi"></a>Power BI

Azure Data Explorer ermöglicht es, eine Verbindung mit [Power BI](https://powerbi.microsoft.com) mithilfe verschiedener Methoden herzustellen: 

  * [Integrierter nativer Power BI-Connector](power-bi-connector.md)

  * [Abfragenimport aus Azure Data Explorer in Power BI](power-bi-imported-query.md)
 
  * [SQL query](power-bi-sql-query.md)

## <a name="microsoft-excel"></a>Microsoft Excel

Azure Data Explorer ermöglicht es, eine Verbindung mit [Microsoft Excel](https://products.office.com/excel) über den [integrierten nativen Excel-Connector](excel-connector.md) herzustellen oder eine Abfrage aus Azure Data Explorer in Excel zu [importieren](excel-blank-query.md).

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) stellt ein Plug-In für Azure Data Explorer zur Verfügung, mit dem Sie Daten aus Azure Data Explorer visualisieren können. Sie [richten Azure Data Explorer als Datenquelle für Grafana ein und visualisieren anschließend die Daten](grafana.md). 

## <a name="kibana"></a>Kibana

Azure Data Explorer ermöglicht das Herstellen einer Verbindung mit [Kibana](https://www.elastic.co/guide/en/kibana/6.8/discover.html) mithilfe des Open Source-Connectors K2Bridge. Sie [richten Azure Data Explorer als Datenquelle für Kibana ein und visualisieren anschließend die Daten](k2bridge.md).

## <a name="odbc-connector"></a>ODBC-Connector

Azure Data Explorer verfügt über einen [ODBC-Connector (Open Database Connectivity)](connect-odbc.md), sodass alle Anwendungen mit ODBC-Unterstützung eine Verbindung damit herstellen können.

## <a name="tableau"></a>Tableau

Azure Data Explorer ermöglicht es, eine Verbindung mit [Tableau](https://www.tableau.com) über den [ODBC-Connector](connect-odbc.md) herzustellen und die [Daten dann in Tableau zu visualisieren](tableau.md).

## <a name="qlik"></a>Qlik

Azure Data Explorer ermöglicht das Herstellen einer Verbindung mit [Qlik](https://www.qlik.com) per [ODBC-Connector](connect-odbc.md) und das anschließende Erstellen von Qlik Sense-Dashboards und das Visualisieren der Daten. Im folgenden Video wird beschrieben, wie Sie Azure Data Explorer-Daten mit Qlik visualisieren. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Azure Data Explorer ermöglicht es, eine Verbindung mit [Sisense](https://www.sisense.com) über den JDBC-Connector herzustellen. Sie [richten Azure Data Explorer als Datenquelle für Sisense ein und visualisieren dann die Daten](sisense.md).

## <a name="redash"></a>Redash

Sie können [Redash](https://redash.io/) verwenden, um Dashboards zu erstellen und Daten zu visualisieren. [Richten Sie Azure Data Explorer als Datenquelle für Redash ein, und visualisieren Sie dann die Daten](redash.md).
