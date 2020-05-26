---
title: Azure Data Explorer-Datenvisualisierung
description: Erfahren Sie mehr über die verschiedenen Möglichkeiten, mit denen Sie Ihre Azure Data Explorer-Daten visualisieren können
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: b1d888471c93409826abe523ae6ae4df39e120c3
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83374258"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Datenvisualisierung mit Azure Data Explorer 

Azure Data Explorer ist ein schneller und hochgradig skalierbarer Dienst für die Untersuchung von Protokoll- und Telemetriedaten, die zum Erstellen von komplexen Analyselösungen für große Datenmengen verwendet werden. Azure Data Explorer wird in verschiedene Visualisierungstools integriert, damit Sie Ihre Daten visualisieren und die Ergebnisse unternehmensweit freigeben können. Diese Daten können in umfassende Einblicke hinsichtlich der Auswirkungen auf Ihr Unternehmen transformiert werden.

Datenvisualisierung und Berichterstellung ist ein wichtiger Schritt im Datenanalyseprozess. Azure Data Explorer unterstützt viele BI-Dienste, damit Sie denjenigen nutzen können, der für Ihr Szenario und Budget am besten geeignet ist.

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

Azure Data Explorer ermöglicht das Herstellen einer Verbindung mit [Kibana (Seite „Ermitteln“)](https://www.elastic.co/guide/en/kibana/6.8/discover.html) mithilfe des Open Source-Connectors K2Bridge. Sie [richten Azure Data Explorer als Datenquelle für Kibana ein und visualisieren anschließend die Daten](k2bridge.md).

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
