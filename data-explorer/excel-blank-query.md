---
title: Visualisieren von Daten mithilfe einer in Microsoft Excel importierten Azure Data Explorer-Kusto-Abfrage
description: In diesem Artikel erfahren Sie, wie Sie eine Azure Data Explorer-Kusto-Abfrage in Microsoft Excel importieren.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/30/2019
ms.openlocfilehash: 0c1900b1feb0af150e25fbc85c231f31a30663b0
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874629"
---
# <a name="visualize-data-using-an-azure-data-explorer-kusto-query-imported-into-microsoft-excel"></a>Visualisieren von Daten mithilfe einer in Microsoft Excel importierten Azure Data Explorer-Kusto-Abfrage

Azure Data Explorer bietet zwei Optionen für die Verbindungsherstellung mit Daten in Excel: Verwenden des nativen Connectors oder Importieren einer Abfrage aus Azure Data Explorer. In diesem Artikel erfahren Sie, wie Sie eine Abfrage aus Azure Data Explorer in Excel importieren, um Daten zu visualisieren. Fügen Sie eine Kusto-Abfrage als Excel-Datenquelle hinzu, um zusätzliche Berechnungen oder Visualisierungen für die Daten durchzuführen.

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Ein Organisations-E-Mail-Konto, das zu Azure Active Directory gehört, um eine Verbindung mit dem [Azure Data Explorer-Hilfecluster](https://dataexplorer.azure.com/clusters/help/databases/Samples) herstellen zu können. 
<br>oder</br>
* Erstellen Sie [einen Testcluster und eine Testdatenbank](create-cluster-database-portal.md), und melden Sie sich bei der [Azure Data Explorer-Webbenutzeroberfläche](https://dataexplorer.azure.com/) an.

## <a name="define-kusto-query-as-an-excel-data-source"></a>Definieren einer Kusto-Abfrage als Excel-Datenquelle

1. Führen Sie die Abfrage auf der [Azure Data Explorer-Webbenutzeroberfläche](https://dataexplorer.azure.com/clusters/help/databases/Samples) aus, und überprüfen Sie die Ergebnisse.

1. Wählen Sie die Registerkarte **Teilen** und anschließend **Query to Power BI** (Abfrage mit Power BI) aus.

    ![Webbenutzeroberflächen-Abfrage für Power BI](media/excel-blank-query/web-ui-query-to-powerbi.png)

1. Daraufhin wird ein Fenster mit der folgenden Benachrichtigung angezeigt:

    ![Exportieren der Abfrage in die Zwischenablage](media/excel-blank-query/query-exported-to-clipboard.png)

1. Öffnen Sie **Microsoft Excel**.

1. Wählen Sie auf der Registerkarte **Daten** Folgendes aus: **Daten abrufen** > **Aus anderen Quellen** > **Leere Abfrage**.

    ![Abrufen von Daten und Auswählen einer leeren Abfrage](media/excel-blank-query/get-data-blank-query.png)

1. Das Fenster **Power Query-Editor** wird geöffnet. Wählen Sie in diesem Fenster **Erweiterter Editor** aus.

    ![Fenster des Power Query-Editors](media/excel-blank-query/power-query-editor.png)

1. Fügen Sie im Fenster **Erweiterter Editor** die Abfrage ein, die Sie in die Zwischenablage exportiert haben, und wählen Sie anschließend **Fertig** aus.

    ![Abfrage im erweiterten Editor](media/excel-blank-query/advanced-editor-query.png)    

1. Wählen Sie zum Authentifizieren die Option **Anmeldeinformationen bearbeiten** aus.

    ![Anmeldeinformationen bearbeiten](media/excel-blank-query/edit-credentials.png)

1. Wählen Sie **Organisationskonto** und anschließend **Anmelden** aus. Schließen Sie den Anmeldevorgang ab, und wählen Sie anschließend **Verbinden** aus.

    ![Abschließen der Anmeldung](media/excel-blank-query/complete-sign-in.png)

    Wiederholen Sie die vorherigen Schritte, um weitere Abfragen hinzuzufügen. Sie können die Abfragen mit aussagekräftigeren Namen versehen.

1. Wählen Sie die Schaltfläche**Schließen & laden** aus, um Ihre Daten in Excel zu übertragen.

    ![Auswählen von „Schließen & laden“](media/excel-blank-query/close-and-load.png)

1. Ihre Daten befinden sich nun in Excel. Aktualisieren Sie die Abfrage mithilfe der Schaltfläche **Aktualisieren**.

    ![Anzeigen der Daten in Excel](media/excel-blank-query/data-in-excel.png)

## <a name="next-steps"></a>Nächste Schritte

[Visualisieren von Daten mithilfe des Azure Data Explorer-Connectors für Excel](excel-connector.md)