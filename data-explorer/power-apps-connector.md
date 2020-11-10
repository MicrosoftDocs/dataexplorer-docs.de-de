---
title: Erstellen von Power Apps-Anwendungen zum Abfragen von Daten in Azure Data Explorer
description: Hier erfahren Sie, wie Sie eine Anwendung in Power Apps erstellen, die auf Daten in Azure Data Explorer basiert.
author: orspod
ms.author: orspodek
ms.reviewer: olgolden
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/20/2020
ms.openlocfilehash: 17ee68efa1f7c43814c4c86f9e5881cd0ba9af61
ms.sourcegitcommit: 455d902bad0aae3e3d72269798c754f51442270e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349544"
---
# <a name="create-power-apps-application-to-query-data-in-azure-data-explorer-preview"></a>Erstellen von Power Apps-Anwendungen zum Abfragen von Daten in Azure Data Explorer (Vorschau)

Azure Data Explorer ist ein schneller, vollständig verwalteter Datenanalysedienst für Echtzeitanalysen großer Datenmengen, die von Anwendungen, Websites, IoT-Geräten usw. gestreamt werden.

Bei Power Apps handelt es sich um eine Suite von Apps, Diensten, Connectors und Datenplattformen, die eine Umgebung für die schnelle Anwendungsentwicklung zum Erstellen benutzerdefinierter Apps bereitstellt, die Ihre Geschäftsdaten verbinden. Der Power Apps-Connector ist besonders nützlich, wenn Sie über eine große und wachsende Sammlung von Streamingdaten in Azure Data Explorer verfügen und eine funktionsreiche App mit wenig Code erstellen möchten, um diese Daten nutzen zu können. In diesem Artikel erstellen Sie eine Power Apps-Anwendung zum Abfragen von Azure Data Explorer-Daten. Während dieses Vorgangs werden die Schritte zum Parametrisieren, Abrufen und Darstellen von Daten gezeigt.

## <a name="prerequisites"></a>Voraussetzungen

* Power Platform-Lizenz. Erste Schritte finden Sie unter [https://powerapps.microsoft.com](https://powerapps.microsoft.com).
* Erfahrung mit der [Power Apps-Suite](/powerapps/powerapps-overview)

## <a name="connect-to-azure-data-explorer-connector"></a>Herstellen einer Verbindung mit dem Azure Data Explorer-Connector

1. Navigieren Sie zu [https://make.preview.powerapps.com/](https://make.preview.powerapps.com/), und melden Sie sich an.

1. Wählen Sie im Menü auf der linken Seite **Verbindungen** aus.
1. Wählen Sie **+ Neue Verbindung** aus.

    :::image type="content" source="media/power-apps-connector/new-connection.png" alt-text="Erstellen einer neuen Verbindung in Power Apps":::

1. Suchen Sie über die Suchleiste nach **Azure Data Explorer**. Wählen Sie aus den angezeigten Optionen **Azure Data Explorer** aus.

    :::image type="content" source="media/power-apps-connector/search-adx.png" alt-text="Suchen nach und Auswählen der Azure Data Explorer-Verbindung in Power Apps":::

1. Wählen Sie im Popup **Azure Data Explorer** die Option **Erstellen** aus. Geben Sie Anmeldeinformationen nach Bedarf an.

    :::image type="content" source="media/power-apps-connector/create-connector.png" alt-text="Erstellen eines Connector für Azure Data Explorer: Popupfenster":::

## <a name="create-app"></a>Erstellen einer App

1. Navigieren Sie zu Power Apps, und wählen Sie im Menü auf der linken Seite **Apps** aus.
1. Wählen Sie auf der Menüleiste **+ Neue App** aus.
1. Wählen Sie in der angezeigten Dropdownliste **Canvas** aus.

    :::image type="content" source="media/power-apps-connector/create-new-app.png" alt-text="Erstellen einer neuen App und Canvas: Power Apps-Connector für Azure Data Explorer":::

1. Wählen Sie im Abschnitt **Leere App** die Option **Tabletlayout** aus.

    :::image type="content" source="media/power-apps-connector/blank-canvas.png" alt-text="Beginnen mit einer leeren Canvas im Tabletlayout: Power Apps-Connector mit Azure Data Explorer":::

### <a name="add-connector"></a>Hinzufügen eines Connectors

1. Klicken Sie im linken Navigationsbereich auf das Symbol **Daten**. 
1. Erweitern Sie **Connectors**.
1. Wählen Sie aus den angezeigten Optionen **Azure Data Explorer** aus.

    :::image type="content" source="media/power-apps-connector/data-connectors-adx.png" alt-text="Hinzufügen eines Connectors zu Azure Data Explorer in Power Apps":::

Ein neuer Bereich namens **In Ihrer App** wird angezeigt, der nun **Azure Data Explorer** enthält.

   :::image type="content" source="media/power-apps-connector/adx-appears.png" alt-text="Azure Data Explorer wird nun im Bereich „In Ihrer App“ in Power Apps angezeigt.":::

### <a name="save-your-app"></a>Speichern Ihrer App

1. Wählen Sie auf der Menüleiste **Datei** aus. 
1. Wählen Sie im linken Navigationsbereich **Speichern** aus.

    :::image type="content" source="media/power-apps-connector/save-app.png" alt-text="Speichern Ihrer App in Power Apps":::

1. Geben Sie einen aussagekräftigen Namen für Ihre App ein. Klicken Sie unten rechts auf die Schaltfläche **Speichern**.

### <a name="advanced-settings"></a>Erweiterte Einstellungen

1. Wählen Sie im Menü auf der linken Seite die Option **Einstellungen** aus.
1. Wählen Sie **Erweiterte Einstellungen**.
1. Wählen Sie in den angezeigten Optionen **Dynamisches Schema** aus. Aktivieren Sie dieses Feature.

    :::image type="content" source="media/power-apps-connector/dynamic-schema.png" alt-text="Aktivieren der Einstellung für das dynamische Schema in Power Apps: Verbindung mit Azure Data Explorer":::

1. Suchen Sie nach der Einstellung **Grenzwert für Datenzeilen für nicht delegierbare Abfragen**. Legen Sie einen Grenzwert für zurückgegebene Datensätze fest.

    :::image type="content" source="media/power-apps-connector/set-limit.png" alt-text="Festlegen des Grenzwerts für zurückgegebene Ergebnisse in Power Apps: Azure Data Explorer":::

    > [!NOTE]
    > Der Standardgrenzwert ist 500 mit maximal 2.000 zurückgegebenen Datensätzen.

> [!IMPORTANT]
> Speichern Sie Ihre App erneut, und starten Sie sie bei Bedarf neu.

### <a name="add-dropdown"></a>Hinzufügen eines Dropdownmenüs

1. Wählen Sie auf der Menüleiste **Einfügen** aus. 
1. Wählen Sie auf der angezeigten Untermenüleiste **Eingabe** aus. 
1. Wählen Sie im angezeigten Dropdownmenü die Option **Dropdown** aus.
1. Klicken Sie im rechten Popoutfenster auf die Registerkarte **Erweitert**.
1. Geben Sie in das Eingabefeld **Elemente** Folgendes ein: ["CALIFORNIA","MICHIGAN"]

    :::image type="content" source="media/power-apps-connector/populate-dropdown.png" alt-text="Eingeben von Elementen im Dropdownmenü" lightbox="media/power-apps-connector/populate-dropdown.png":::

1. Lassen Sie das **Dropdownmenü** ausgewählt, und wählen Sie auf der Bearbeitungsleiste im Dropdownmenü **Eigenschaft** die Option **OnChange** aus.

1. Geben Sie die folgende Formel ein:

    ```kusto
    ClearCollect(
    Results,
    AzureDataExplorer.listKustoResultsPost(
    "https://help.kusto.windows.net",
    "Samples",
    "StormEvents | where State == '" & Dropdown1.SelectedText.Value & "' | take 15"
    ).value
    )
    ```
    
1. Klicken Sie auf die Schaltfläche **Schema erfassen**. Planen Sie Zeit für die Verarbeitung ein.

    :::image type="content" source="media/power-apps-connector/capture-schema.png" alt-text="Auswählen der Schaltfläche „Schema erfassen“ im Dropdownmenü":::

### <a name="add-data-table"></a>Hinzufügen einer Datentabelle

1. Wählen Sie auf der Menüleiste **Einfügen** aus. 
1. Wählen Sie auf der angezeigten Untermenüleiste **Datentabelle** aus.
1. Ändern Sie die Position der Datentabelle, und fügen Sie aus Gründen der Sichtbarkeit ggf. einen Rahmen hinzu.
1. Wählen Sie im Popoutfenster auf der rechten Seite die Registerkarte **Eigenschaften** aus. Wählen Sie im Dropdownmenü **Datenquelle** die Option „Ergebnisse“ aus.
1. Wählen Sie den Link **Felder bearbeiten** aus. 
1. Wählen Sie im angezeigten Popoutfenster die Option **+ Feld hinzufügen** aus. 
    
    :::image type="content" source="media/power-apps-connector/insert-data-table-small.png" alt-text="Ändern der Position der Tabelle und Hinzufügen eines Rahmens" lightbox="media/power-apps-connector/insert-data-table.png":::

1. Wählen Sie die gewünschten Felder aus, und klicken Sie auf die Schaltfläche **Hinzufügen**. Eine Vorschau der ausgewählten Datentabelle wird angezeigt.

    :::image type="content" source="media/power-apps-connector/preview-table.png" alt-text="Vorschau der Tabelle mit aufgefüllten Daten":::

### <a name="validate"></a>Überprüfen

1. Klicken Sie in der oberen rechten Ecke des Bildschirms auf die Schaltfläche **Vorschau der App**.
1. Probieren Sie das Dropdownmenü aus, scrollen Sie durch die Datentabelle, und überprüfen Sie, ob die Daten erfolgreich abgerufen und dargestellt werden.

    :::image type="content" source="media/power-apps-connector/preview-app.png" alt-text="Anzeigen einer Vorschau der neuen App in Power Apps mit Daten aus Azure Data Explorer":::

### <a name="limitations"></a>Einschränkungen

* In Power Apps gilt ein Grenzwert von bis zu 2.000 Ergebnisdatensätzen, die an den Client zurückgegeben werden. Der gesamte Arbeitsspeicher für diese Datensätze darf 64 MB und eine Ausführungszeit von sieben Minuten nicht überschreiten.
* Der Connector unterstützt die Operatoren [fork](./kusto/query/forkoperator.md) und [facet](./kusto/query/facetoperator.md) nicht.
* **Timeoutausnahmen:** Für den Connector gilt eine Timeoutbeschränkung von sieben Minuten. Um ein potenzielles Timeoutproblem zu vermeiden, gestalten Sie Ihre Abfrage effizienter, damit sie schneller ausgeführt wird, oder unterteilen Sie sie in Blöcke. Jeder Block kann in einem anderen Teil der Abfrage ausgeführt werden. Weitere Informationen finden Sie unter [Bewährte Methoden für Abfragen](./kusto/query/best-practices.md).

## <a name="next-steps"></a>Nächste Schritte