---
title: Visualisieren von Daten über Azure Data Explorer mit einer importierten Abfrage in Power BI
description: 'In diesem Artikel erfahren Sie, wie Sie eine der drei Optionen zum Visualisieren von Daten in Power BI verwenden: das Importieren einer Abfrage aus Azure Data Explorer.'
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: how-to
ms.date: 07/10/2019
ms.openlocfilehash: 4ab51eaef43596da0f2b511a4a617afaade939cf
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875071"
---
# <a name="visualize-data-using-a-query-imported-into-power-bi"></a>Visualisieren von Daten mithilfe einer in Power BI importierten Abfrage

Azure-Daten-Explorer ist ein schneller und hochgradig skalierbarer Dienst zur Untersuchung von Daten (Protokoll- und Telemetriedaten). Power BI ist eine Business Analytics-Lösung, mit der Sie Ihre Daten visualisieren und die Ergebnisse in Ihrer Organisation teilen können.

Azure Data Explorer bietet drei Optionen für die Verbindungsherstellung mit Daten in Power BI: Verwenden des integrierten Connectors, Importieren einer Abfrage aus Azure Data Explorer oder Verwenden einer SQL-Abfrage. In diesem Artikel wird erläutert, wie Sie eine Abfrage importieren, um Daten abzurufen und in einem Power BI-Bericht zu visualisieren.

Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um diesen Artikel durchzuarbeiten:

* Ein Organisations-E-Mail-Konto, das Azure Active Directory angehört, um eine Verbindung mit dem [Azure Data Explorer-Hilfecluster](https://dataexplorer.azure.com/clusters/help/databases/samples) herstellen zu können.

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (wählen Sie **KOSTENLOS HERUNTERLADEN** aus)

* [Azure Data Explorer-Desktop-App](kusto/tools/kusto-explorer.md)

## <a name="get-data-from-azure-data-explorer"></a>Abrufen von Daten aus Azure Data Explorer

Sie erstellen zunächst in der Azure Data Explorer-Desktop-App eine Abfrage, die Sie zur Verwendung in Power BI exportieren. Anschließend stellen Sie eine Verbindung mit dem Azure Data Explorer-Hilfecluster her, und Sie fügen eine Teilmenge der Daten aus der Tabelle *StormEvents* ein. [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

1. Navigieren Sie in einem Browser zu [https://help.kusto.windows.net/](https://help.kusto.windows.net/), um die Azure Data Explorer-Desktop-App zu starten.

1. Kopieren Sie in der Desktop-App die folgende Abfrage in das obere rechte Abfragefenster, und führen Sie sie aus.

    ```Kusto
    StormEvents
    | sort by DamageCrops desc
    | take 1000
    ```

    Die ersten Zeilen des Resultsets sollten in etwa wie die Zeilen in der folgenden Abbildung aussehen.

    ![Abfrageergebnisse](media/power-bi-imported-query/query-results.png)

1. Klicken Sie auf der Registerkarte **Tools** auf **Query to Power BI** (Abfrage in Power BI) und dann auf **OK**.

    ![Exportieren einer Abfrage](media/power-bi-imported-query/export-query.png)

1. Wählen Sie in Power BI Desktop auf der Registerkarte **Start** die Option **Daten abrufen** und anschließend **Leere Abfrage** aus.

    ![Datensammlung](media/power-bi-imported-query/get-data.png)

1. Wählen Sie im Power Query-Editor auf der Registerkarte **Start** die Option **Erweiterter Editor** aus.

1. Fügen Sie im Fenster **Erweiterter Editor** die Abfrage ein, die Sie exportiert haben, und klicken Sie dann auf **Fertig**.

    ![Einfügen der Abfrage](media/power-bi-imported-query/paste-query.png)

1. Wählen Sie im Hauptfenster des Power Query-Editors **Anmeldeinformationen bearbeiten** aus. Wählen Sie **Organisationskonto** aus, melden Sie sich an, und klicken Sie dann auf **Verbinden**.

    ![Anmeldeinformationen bearbeiten](media/power-bi-imported-query/edit-credentials.png)

1. Klicken Sie auf der Registerkarte **Start** auf **Schließen und übernehmen**.

    ![Schließen und übernehmen](media/power-bi-imported-query/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Visualisieren von Daten in einem Bericht

[!INCLUDE [data-explorer-power-bi-visualize-basic](includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den Bericht, den Sie für diesen Artikel erstellt haben, nicht mehr benötigen, löschen Sie die Power BI Desktop-Datei (PBIX-Datei).

## <a name="next-steps"></a>Nächste Schritte

[Visualisieren von Daten mithilfe des Azure Data Explorer-Connectors für Power BI](power-bi-connector.md)