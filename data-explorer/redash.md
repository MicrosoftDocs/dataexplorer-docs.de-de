---
title: Visualisieren von Azure Data Explorer mit Redash
description: In diesem Artikel erfahren Sie, wie Sie Daten in Azure Data Explorer mit dem nativen Redash-Connector visualisieren.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 99d242358f5b4a54f7e1faa246a1cfc64d1414d3
ms.sourcegitcommit: 6e84f50efc8c5c3fe57080341ed3effe72197886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/30/2020
ms.locfileid: "87439866"
---
# <a name="visualize-data-from-azure-data-explorer-in-redash"></a>Visualisieren von Daten aus Azure Data Explorer in Redash

Über [Redash](https://redash.io/) können Sie Ihre Datenquellen verknüpfen und abfragen, Dashboards zum Visualisieren von Daten erstellen und diese für Kollegen freigeben. In diesem Artikel erfahren Sie, wie Sie den Azure Data Explorer als Datenquelle für Redash einrichten und dann Daten visualisieren.

## <a name="prerequisites"></a>Voraussetzungen

1. [Erstellen Sie einen Cluster und eine Datenbank](create-cluster-database-portal.md).
1. Erfassen Sie Beispieldaten, wie erläutert unter [Erfassen von Beispieldaten in Azure Data Explorer](ingest-sample-data.md). Weitere Erfassungsoptionen finden Sie unter [Übersicht über die Datenerfassung](ingest-data-overview.md).

[!INCLUDE [data-explorer-configure-data-source](includes/data-explorer-configure-data-source.md)]

## <a name="create-azure-data-explorer-connector-in-redash"></a>Erstellen des Azure Data Explorer-Connectors in Redash 

1. Melden Sie sich bei [Redash](https://redash.io/) an. Wählen Sie **Get Started** (Erste Schritte) aus, um ein Konto zu erstellen.
1. Wählen Sie unter **Let's get started** (Jetzt loslegen) die Option **Connect a Data Source** (Datenquelle verbinden) aus.

    ![Herstellen einer Verbindung mit einer Datenquelle](media/redash/connect-data-source.png)

1. Wählen Sie im Fenster **Create a New Data Source** (Neue Datenquelle erstellen) die Option **Azure Data Explorer (Kusto)** aus, und klicken Sie dann auf **Create** (Erstellen). 

    ![Auswählen einer Azure Data Explorer-Datenquelle](media/redash/select-adx-data-source.png)

1. Füllen Sie im Fenster **Azure Data Explorer (Kusto)** die folgenden Felder aus, und klicken Sie anschließend auf **Create** (Erstellen).

    ![Fenster mit Einstellungen für Datenquelle „Azure Data Explorer (Kusto)“](media/redash/adx-settings-window.png)

1. Wählen Sie im Fenster **Settings** (Einstellungen) die Optionen **Save** (Speichern) und **Test Connection** (Verbindung testen) aus, um Ihre Datenquelle vom Typ **Azure Data Explorer (Kusto)** zu testen.

## <a name="create-queries-in-redash"></a>Erstellen von Abfragen in Redash

1. Wählen Sie oben links in Redash **Create** > **Query** (Abfrage > Erstellen) aus. Klicken Sie auf **New Query** (Neue Abfrage), und benennen Sie die Abfrage um.

    ![Erstellen einer Abfrage](media/redash/create-query.png)

1. Geben Sie Ihre Abfrage im Bearbeitungsbereich oben ein, und wählen Sie **Save** (Speichern) und **Execute** (Ausführen) aus. Klicken Sie auf **Publish** (Veröffentlichen), um die Abfrage für eine spätere Verwendung zu veröffentlichen.

    ![Speichern und Ausführen der Abfrage](media/redash/save-and-execute-query.png)

    Das Dropdownmenü im linken Bereich zeigt den Name der Datenquellenverbindung (in unserem Flow **Github connector**) sowie die in der ausgewählten Datenbank enthaltenen Tabellen an. 

1. Die Abfrageergebnisse werden unten im Hauptbereich angezeigt. Erstellen Sie eine Visualisierung für die Abfrage, indem Sie auf die Schaltfläche **New Visualization** (Neue Visualisierung) klicken.

    ![Neue Visualisierung](media/redash/new-visualization.png)

1. Wählen Sie im Visualisierungsbildschirm den **Visualization Type** (Visualisierungstyp) und die relevanten Felder aus, z. B. **X Column** (X-Spalte) und **Y Column** (Y-Spalte). Klicken Sie auf **Save** (Speichern), um die Visualisierung zu speichern.

    ![Konfigurieren und Speichern der Visualisierung](media/redash/configure-visualization.png)

### <a name="create-a-query-using-a-parameter"></a>Erstellen einer Abfrage mithilfe eines Parameters

1. Klicken Sie auf **Create** > **Query** (Erstellen > Abfrage), um eine neue Abfrage zu erstellen. Fügen Sie der Abfrage mithilfe der geschweiften Klammern {{}} einen Parameter hinzu. Wählen Sie **{{}}** aus, um das Fenster **Add Parameter** (Parameter hinzufügen) zu öffnen. Sie können auch auf das *Zahnradsymbol* klicken, um die Attribute eines vorhandenen Parameters zu ändern und das Fenster **<Name_des_Parameters>** zu öffnen. 

    ![Einfügen eines Parameters](media/redash/insert-parameter.png)

1. Benennen Sie Ihren Parameter. Wählen Sie im Dropdownmenü **Type**: **Query Based Dropdown List** (Typ: Abfragebasierte Dropdownliste) aus. Klicken Sie auf **OK**.

    ![Abfragebasierte Dropdownliste](media/redash/query-based-dropdown-list.png)

    > [!NOTE]
    > Die Abfrage verwendet mehrere Werte, deshalb müssen Sie die folgende Syntax einschließen: `| where Type in ((split('{{Type}}', ',')))`. Weitere Informationen finden Sie unter [in-Operator](kusto/query/inoperator.md). Dies führt zu [mehreren Abfrageparameteroptionen in der Redash-App](https://redash.io/help/user-guide/querying/query-parameters#Serialized-Multi-Select-Query-Parametersredash.io).

## <a name="create-a-dashboard-in-redash"></a>Erstellen eines Dashboards in Redash

1. Um ein Dashboard zu erstellen, klicken Sie auf **Create** > **Dashboard** (Erstellen > Dashboard). Alternativ können Sie ein vorhandenes Dashboard auswählen, indem Sie auf **Dashboards** klicken und ein Dashboard aus der Liste auswählen.

    ![Erstellen von Dashboards](media/redash/create-dashboard.png)

1. Benennen Sie im Fenster **New Dashboard** (Neues Dashboard) Ihr Dashboard, und klicken Sie auf **Save** (Speichern). Wählen Sie im Fenster **<Name_des_Dashboards>** die Option **Add Widget** (Widget hinzufügen), um ein neues Widget zu erstellen. 

1. Wählen Sie im Fenster **Add Widget** (Widget hinzufügen) den Abfragenamen aus, klicken Sie auf **Choose Visualization** (Visualisierung auswählen) und dann auf **Parameters** (Parameter). Klicken Sie auf **Add to Dashboard** (Zum Dashboard hinzufügen).

   ![Auswählen und Hinzufügen von Visualisierungen zum Dashboard](media/redash/add-widget-window.png)

1. Klicken Sie auf **Done Editing** (Bearbeitung abgeschlossen), um die Erstellung des Dashboards abzuschließen.

1.  Wählen Sie im Dashboardbearbeitungsmodus **Use Dashboard Level Filters** (Filter auf Dashboardebene verwenden) aus, um den zuvor definierten **Type**-Parameter zu verwenden.

    ![Abschließen der Dashboarderstellung](media/redash/complete-dashboard.png)

## <a name="next-steps"></a>Nächste Schritte

* [Schreiben von Abfragen für den Azure-Daten-Explorer](write-queries.md)


