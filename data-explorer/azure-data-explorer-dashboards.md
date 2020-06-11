---
title: Visualisieren von Daten mit dem Azure Data Explorer-Dashboard
description: Erfahren Sie, wie Sie Daten mit dem Azure Data Explorer-Dashboard visualisieren.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/26/2020
ms.openlocfilehash: 46e3821514ca5b06852f7e8428b12cf9f80e29a4
ms.sourcegitcommit: a562ce255ac706ca1ca77d272a97b5975235729d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/26/2020
ms.locfileid: "83867797"
---
# <a name="visualize-data-with-azure-data-explorer-dashboards"></a>Visualisieren von Daten mit Azure Data Explorer-Dashboards

Azure-Daten-Explorer ist ein schneller und hochgradig skalierbarer Dienst zur Untersuchung von Daten (Protokoll- und Telemetriedaten). Azure Data Explorer bietet eine Webanwendung, mit der Sie Abfragen ausführen und Dashboards erstellen können. Dashboards sind in der eigenständigen Webanwendung, der [Webbenutzeroberfläche](web-query-data.md), verfügbar. Azure Data Explorer ist auch mit anderen Dashboarddiensten wie [Power BI](power-bi-connector.md) und [Grafana](grafana.md) integriert.

Azure Data Explorer-Dashboards bieten drei wichtige Vorteile:

* Natives Exportieren von Abfragen von der Webbenutzeroberfläche in Azure Data Explorer-Dashboards 
* Untersuchen der Daten auf der Webbenutzeroberfläche
* Optimieren der Dashboardrenderingleistung

In der folgenden Abbildung ist ein Azure Data Explorer-Dashboard dargestellt.

:::image type="content" source="media/adx-dashboards/dash.png" alt-text="Fertiges Dashboard":::

## <a name="create-a-dashboard"></a>Erstellen eines Dashboards

1. Wählen Sie auf der Navigationsleiste **Dashboards** und dann **Neues Dashboard** aus.

    :::image type="content" source="media/adx-dashboards/new-dashboard.png" alt-text="Neues Dashboard":::

1. Wählen Sie einen Dashboardnamen und dann **Erstellen** aus.

    :::image type="content" source="media/adx-dashboards/new-dashboard-popup.png" alt-text="Erstellen eines Dashboards":::

## <a name="add-data-source"></a>Hinzufügen einer Datenquelle

Fügen Sie die erforderlichen Datenquellen für die Dashboards hinzu.

1. Wählen Sie auf der oberen Leiste das Menüelement **Datenquellen** aus. Wählen Sie im Bereich **Datenquellen** die Schaltfläche **Neue Datenquelle** aus.

    :::image type="content" source="media/adx-dashboards/data-source.png" alt-text="Datenquelle":::

1. Im Bereich **Neue Datenquelle erstellen**:
    1. Geben Sie den **Cluster-URI** oder den partiellen Namen einschließlich der Region ein, und wählen Sie **Verbinden** aus. 
    1. Wählen Sie die **Datenbank** aus der Dropdownliste aus.
    1. Verwenden Sie den Standardwert, oder ändern Sie ggf. den **Datenquellennamen**. 
    1. Wählen Sie **Übernehmen**.

    :::image type="content" source="media/adx-dashboards/data-source-pane.png" alt-text="Bereich „Datenquelle“":::

## <a name="use-parameters"></a>Verwenden von Parametern

Parameter ermöglichen die Verwendung von Dashboardfiltern. Parameter verbessern die Renderingleistung von Dashboards erheblich und ermöglichen Ihnen, Filterwerte so früh wie möglich in der Abfrage zu verwenden.

1. Wählen Sie auf der oberen Leiste **Parameter** aus. Wählen Sie im Bereich **Parameter** die Schaltfläche **Neuer Parameter** aus.

    :::image type="content" source="media/adx-dashboards/parameters.png" alt-text="Auswählen von „Neuer Parameter“":::

1. Geben Sie die Werte für alle erforderlichen Felder ein, und wählen Sie **Fertig** aus.

    :::image type="content" source="media/adx-dashboards/parameter-pane.png" alt-text="Bereich „Parameter“":::

|Feld  |BESCHREIBUNG |
|---------|---------|
|**Parameter display name** (Anzeigename des Parameters)    |   Der Name des Parameters, der auf dem Dashboard oder der Bearbeitungskarte angezeigt wird.      |
|**Parametertyp**    |Einer der folgenden:<ul><li>**Single selection** (Einfachauswahl): Im Filter kann als Eingabe für den Parameter nur ein Wert ausgewählt werden.</li><li>**Mehrfachauswahl:** Im Filter können als Eingaben für den Parameter mehrere Werte ausgewählt werden.</li><li>**Zeitbereich**: Ermöglicht das Erstellen zusätzlicher Parameter, um die Abfragen und Dashboards basierend auf der Zeit zu filtern. Jedes Dashboard verfügt standardmäßig über eine Zeitbereichsauswahl.</li></ul>    |
|**Variablenname**     |   Der Name des Parameters, der in der Abfrage verwendet werden soll.      |
|**Datentyp**    |    Der Datentyp des Parameterwerts.     |
|**Pin as dashboard filter** (Als Dashboardfilter anheften)   |   Heften Sie den parameterbasierten Filter an das Dashboard an, oder lösen Sie ihn vom Dashboard.       |
|**Quelle**     |    Die Quelle der Parameterwerte: <ul><li>**Feste Werte:** Manuell eingefügte statische Filterwerte. </li><li>**Query** (Abfrage): Dynamisch über eine KQL-Abfrage eingefügte Werte.  </li></ul>    |
|**Add a „Select all“ value** („Alle auswählen“ hinzufügen)    |   Gilt nur für Parametertypen mit Einfach- und Mehrfachauswahl. Wird verwendet, um Daten für alle Parameterwerte abzurufen.      |

## <a name="add-query"></a>Hinzufügen einer Abfrage

Bei **Abfrage hinzufügen** werden Ausschnitte in der Kusto-Abfragesprache zum Abrufen von Daten und Rendern von visuellen Elementen verwendet. Jede Abfrage kann ein einzelnes visuelles Element unterstützen.

1. Wählen Sie auf der Dashboardcanvas oder auf der oberen Menüleiste **Abfrage hinzufügen** aus.

    :::image type="content" source="media/adx-dashboards/empty-dashboard-new-query.png" alt-text="Neue Abfrage":::

1. Im Bereich **Abfrage**: 
    1. Wählen Sie in der Dropdownliste eine Datenquelle aus.
    1. Geben Sie die Abfrage ein, und wählen Sie **Ausführen** aus. 
    1. Wählen Sie **Visual hinzufügen** aus.

    :::image type="content" source="media/adx-dashboards/initial-query.png" alt-text="Ausführen der Abfrage":::

1. Wählen Sie im Bereich **Visual formatting** (Formatierung des visuellen Elements) die Option **Diagrammtyp** aus, um den Typ des visuellen Elements auszuwählen. 
1. Benennen Sie das visuelle Element, und wählen Sie **Änderungen anwenden** aus, um das visuelle Element an das Dashboard anzuheften.

    :::image type="content" source="media/adx-dashboards/add-visual.png" alt-text="Hinzufügen eines visuellen Elements zu einer Abfrage":::

1. Sie können auch die Größe des visuellen Elements ändern. Wählen Sie anschließend **Änderungen speichern** aus, um das Dashboard zu speichern.

    :::image type="content" source="media/adx-dashboards/save-dashboard.png" alt-text="Speichern des Dashboards":::

## <a name="next-steps"></a>Nächste Schritte

* [Abfragen von Daten in Azure Data Explorer](web-query-data.md)