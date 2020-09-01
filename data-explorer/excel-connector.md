---
title: Visualisieren von Daten mithilfe des Azure Data Explorer-Connectors für Microsoft Excel
description: In diesem Artikel erfahren Sie, wie Sie den Excel-Connector für Azure Data Explorer verwenden.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/30/2019
ms.openlocfilehash: d74409b8ec24e02458e2fed4a135b248ba970388
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874595"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-excel"></a>Visualisieren von Daten mithilfe des Azure Data Explorer-Connectors für Excel

Azure Data Explorer bietet zwei Optionen für die Verbindungsherstellung mit Daten in Excel: Verwenden des nativen Connectors oder Importieren einer Abfrage aus Azure Data Explorer. In diesem Artikel erfahren Sie, wie Sie den nativen Connector in Excel verwenden und eine Verbindung mit dem Azure Data Explorer-Cluster herstellen, um Daten abzurufen und zu visualisieren.

Der native Excel-Connector von Azure Data Explorer ermöglicht das Exportieren von Abfrageergebnissen nach Excel. Für zusätzliche Berechnungen oder Visualisierungen kann auch eine KQL-Abfrage als Excel-Datenquelle hinzugefügt werden.

## <a name="define-kusto-query-as-an-excel-data-source-and-load-the-data-to-excel"></a>Definieren einer Kusto-Abfrage als Excel-Datenquelle und Laden der Daten in Excel

1. Öffnen Sie **Microsoft Excel**.
1. Wählen Sie auf der Registerkarte **Daten** Folgendes aus: **Daten abrufen** > **Aus Azure** > **Aus Azure Data Explorer**.

    ![Abrufen von Daten aus Azure Data Explorer](media/excel-connector/get-data-from-adx.png)

1. Füllen Sie im Fenster **Azure Data Explorer (Kusto)** die folgenden Felder aus, und wählen Sie anschließend **OK** aus:

    ![Fenster „Azure Data Explorer (Kusto)“](media/excel-connector/adx-connection-window.png)
    
    |Feld   |BESCHREIBUNG |
    |---------|---------|
    |**Cluster**   |   Name des Clusters (Pflichtfeld)      |    
    |**Datenbank**     |    Name der Datenbank      |    
    |**Tabellenname oder Azure Data Explorer-Abfrage**    |     Name der Tabelle oder Azure Data Explorer-Abfrage    | 
    
    **Erweiterte Optionen:**

     |Feld   |BESCHREIBUNG |
    |---------|---------|
    |**Datensatzanzahl für Abfrageergebnisse einschränken**     |     Begrenzt die Anzahl von Datensätzen, die in Excel geladen werden  |    
    |**Datengröße in Bytes für Abfrageergebnisse einschränken**    |    Begrenzt die Datengröße      |   
    |**Abschneiden von Resultsets deaktivieren**    |         |      
    |**Zusätzliche SET-Anweisungen (durch Semikolons getrennt)**    |    Fügen Sie `set`-Anweisungen hinzu, die auf die Datenquelle angewendet werden sollen     |   

1.  Navigieren Sie im Bereich **Navigator** zur korrekten Tabelle. Wählen Sie im Tabellenvorschaubereich **Daten transformieren** aus, um Ihre Daten zu ändern, oder wählen Sie **Laden** aus, um sie in Excel zu laden.

![Tabellenvorschaufenster](media/excel-connector/navigate-table-preview-window.png)

   > [!TIP]
   > Falls **Datenbank** und/oder **Tabellenname oder Azure Data Explorer-Abfrage** bereits angegeben sind, wird automatisch der korrekte Tabellenvorschaubereich geöffnet. 

## <a name="analyze-and-visualize-data-in-excel"></a>Analysieren und Visualisieren von Daten in Excel

Nachdem die Daten in Excel geladen wurden und in der Excel-Tabelle verfügbar sind, können Sie Beziehungen und visuelle Elemente erstellen, um die Daten zu analysieren, zusammenzufassen und zu visualisieren. 

1.  Wählen Sie auf der Registerkarte **Entwurf** die Option **Mit PivotTable zusammenfassen** aus. Wählen Sie im Fenster **PivotTable erstellen** die relevante Tabelle und anschließend **OK** aus.

    ![Erstellen einer PivotTable](media/excel-connector/create-pivot-table.png)

1. Wählen Sie im Bereich**PivotTable-Felder** die relevanten Tabellenspalten aus, um Zusammenfassungstabellen zu erstellen. Im folgenden Beispiel werden **EventId** und **State** ausgewählt.
    
    ![Auswählen von PivotTable-Feldern](media/excel-connector/pivot-table-pick-fields.png)

1. Wählen Sie auf der Registerkarte **PivotTable-Analyse** die Option **PivotChart** aus, um visuelle Elemente auf der Grundlage der Tabelle zu erstellen. 

    ![PivotChart](media/excel-connector/pivot-table-analyze-pivotchart.png)

1. Verwenden Sie im folgenden Beispiel **EventId**, **StartTime** und **EventType**, um zusätzliche Informationen zu den Wetterereignissen anzuzeigen.

    ![Visualisieren von Daten](media/excel-connector/visualize-excel-data.png)

1. Erstellen Sie umfassende Dashboards für die Datenüberwachung.

## <a name="next-steps"></a>Nächste Schritte

[Visualisieren von Daten mithilfe einer in Microsoft Excel importierten Azure Data Explorer-Kusto-Abfrage](excel-blank-query.md)