---
title: Anwendungsbeispiele für den Azure Data Explorer-Connector für Power Automate (Vorschauversion)
description: Hier finden Sie einige gängige Anwendungsbeispiele für den Azure Data Explorer-Connector für Power Automate.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/15/2020
ms.openlocfilehash: 03422de8987e125b5565b0625434ef660426b40a
ms.sourcegitcommit: c2ab3176db4dd55ac9ca8eee52bbd24096d1277f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2020
ms.locfileid: "90740268"
---
# <a name="usage-examples-for-azure-data-explorer-connector-to-power-automate-preview"></a>Anwendungsbeispiele für den Azure Data Explorer-Connector für Power Automate (Vorschauversion)

Der Azure Data Explorer-Connector für Power Automate (vormals Microsoft Flow) ermöglicht es Azure Data Explorer, die Flowfunktionen von [Microsoft Power Automate](https://flow.microsoft.com/) zu nutzen. Sie können Kusto-Abfragen und -Befehle automatisch als Teil einer geplanten oder ausgelösten Aufgabe ausführen. Dieser Artikel enthält einige allgemeine Beispiele für die Verwendung des Power Automate-Connectors.

Weitere Informationen finden Sie unter [Azure Data Explorer-Connector für Power Automate (Vorschauversion)](flow.md).

## <a name="power-automate-connector-and-your-sql-database"></a>Power Automate-Connector und SQL-Datenbank

Verwenden Sie den Power Automate-Connector, um Ihre Daten abzufragen und in einer SQL-Datenbank zu aggregieren.

> [!Note]
> Verwenden Sie den Power Automate-Connector nur für geringe Mengen von Ausgabedaten. Der SQL-Einfügevorgang wird für jede Zeile separat durchgeführt. 

![Screenshot: Abfragen von Daten mit dem Power Automate-Connector](./media/flow-usage/flow-sqlexample.png)

> [!IMPORTANT]
> Geben Sie in das Feld **Clustername** die Cluster-URL ein.

## <a name="push-data-to-a-microsoft-power-bi-dataset"></a>Übertragen von Daten an ein Microsoft Power BI-Dataset per Pushvorgang

Der Power Automate-Connector kann zusammen mit dem Power BI-Connector verwendet werden, um Daten von Kusto-Abfragen an Power BI-Streamingdatasets zu pushen.

1. Erstellen Sie eine neue Aktion für **Abfrage ausführen und Ergebnisse auflisten**.
1. Wählen Sie **Neuer Schritt** aus.
1. Wählen Sie **Aktion hinzufügen** aus, und suchen Sie nach „Power BI“.
1. Wählen Sie **Power BI** > **Zeilen einem Dataset hinzufügen** aus. 

    ![Screenshot: Power BI-Connector](./media/flow-usage/flow-powerbiconnector.png)

1. Geben Sie **Arbeitsbereich**, **Dataset** und **Tabelle** ein, in die die Daten gepusht werden sollen.
1. Fügen Sie im Dialogfeld für dynamische Inhalte eine **Nutzlast** hinzu, die Ihr Datasetschema und die relevanten Kusto-Abfrageergebnisse enthält.

    ![Screenshot: Power BI-Felder](./media/flow-usage/flow-powerbifields.png)

Der Flow wendet automatisch die Power BI-Aktion für jede Zeile der Kusto-Abfrageergebnistabelle an. 

![Screenshot: Power BI-Aktion für jede Zeile](./media/flow-usage/flow-powerbiforeach.png)

## <a name="conditional-queries"></a>Bedingte Abfragen

Sie können die Ergebnisse von Kusto-Abfragen als Eingabe oder Bedingungen für die nächsten Power Automate-Aktionen verwenden.

Im folgenden Beispiel wird Kusto nach Vorfällen abgefragt, die sich am letzten Tag ereignet haben. Für jeden behandelten Vorfall wird eine Slack-Nachricht gepostet und eine Pushbenachrichtigung erstellt.
Für jeden noch aktiven Vorfall wird Kusto nach weiteren Informationen zu ähnlichen Vorfällen abgefragt. Diese Informationen werden als E-Mail gesendet, und eine zugehörige Aufgabe wird in Azure DevOps Server geöffnet.

Gehen Sie wie folgt vor, um einen ähnlichen Flow zu erstellen:

1. Erstellen Sie eine neue Aktion für **Abfrage ausführen und Ergebnisse auflisten**.
1. Wählen Sie **Neuer Schritt** > **Bedingungssteuerelement** aus.
1. Wählen Sie im Fenster für dynamische Inhalte den Parameter aus, den Sie als Bedingung für die nächsten Aktionen verwenden möchten.
1. Wählen Sie den Typ für *Beziehung* und *Wert* aus, um eine bestimmte Bedingung für den jeweiligen Parameter festzulegen.

    :::image type="content" source="./media/flow-usage/flow-condition.png" alt-text="Verwenden von Flowbedingungen basierend auf Kusto-Abfrageergebnissen, um die nächste Flowaktion zu bestimmen, Azure Data Explorer" lightbox="./media/flow-usage/flow-condition.png#lightbox":::

    Der Flow wendet diese Bedingung auf jede Zeile der Abfrageergebnistabelle an.
1. Fügen Sie Aktionen hinzu, wenn die Bedingung wahr (true) und falsch (false) ist.

    :::image type="content" source="./media/flow-usage/flow-conditionactions.png" alt-text="Hinzufügen von Aktionen für den Fall, dass eine Bedingung wahr oder falsch ist, Flowbedingungen basierend auf Kusto-Abfrageergebnissen, Azure Data Explorer" lightbox="./media/flow-usage/flow-conditionactions.png#lightbox":::

Sie können die Ergebniswerte aus der Kusto-Abfrage als Eingabe für die nächsten Aktionen verwenden. Wählen Sie die Ergebniswerte aus dem Fenster für dynamische Inhalte aus.
Im folgenden Beispiel fügen Sie die Aktionen **Slack – Nachricht veröffentlichen** und **Visual Studio – Neues Arbeitselement erstellen** mit Daten aus der Kusto-Abfrage hinzu.

![Screenshot: Aktion „Slack – Nachricht veröffentlichen“](./media/flow-usage/flow-slack.png)

![Screenshot: Visual Studio-Aktion](./media/flow-usage/flow-visualstudio.png)

Wenn in diesem Beispiel noch ein Vorfall aktiv ist, fragen Sie Kusto erneut ab, um Informationen darüber zu erhalten, wie Vorfälle aus derselben Quelle in der Vergangenheit behoben wurden.

![Screenshot: Flow-Bedingungsabfrage](./media/flow-usage/flow-conditionquery.png)

> [!IMPORTANT]
> Geben Sie in das Feld **Clustername** die Cluster-URL ein.

Visualisieren Sie diese Informationen als Kreisdiagramm, und senden Sie sie per E-Mail an das Team.

![Screenshot: E-Mail mit Flow-Bedingung](./media/flow-usage/flow-conditionemail.png)

## <a name="email-multiple-azure-data-explorer-flow-charts"></a>Senden mehrerer Azure Data Explorer-Flow-Diagramme per E-Mail

1. Erstellen Sie einen neuen Flow mit dem Trigger „Serie“, und definieren Sie das Intervall und die Frequenz des Flows. 
1. Fügen Sie einen neuen Schritt mit einer oder mehreren Aktionen für **Kusto – Abfrage ausführen und Ergebnisse visualisieren** hinzu. 

    ![Screenshot: Ausführen mehrerer Abfragen in einem Flow](./media/flow-usage/flow-severalqueries.png)

1. Definieren Sie für jede Aktion für **Kusto – Abfrage ausführen und Ergebnisse visualisieren** die folgenden Felder:
    * Cluster-URL
    * Datenbankname
    * Abfrage- und Diagrammtyp (z. B. HTML-Tabelle, Kreisdiagramm, Zeitdiagramm, Balkendiagramm oder benutzerdefinierter Wert)

    ![Screenshot: Visualisieren von Ergebnissen mit mehreren Anlagen](./media/flow-usage/flow-visualizeresultsmultipleattachments.png)

1. Fügen Sie eine Aktion vom Typ **E-Mail senden (v2)** hinzu: 
    1. Wählen Sie im Hauptabschnitt das Symbol für die Codeansicht aus.
    1. Fügen Sie im Feld **Text** den erforderlichen **BodyHtml**-Code ein, sodass das visualisierte Ergebnis der Abfrage im Textkörper der E-Mail enthalten ist.
    1. Um der E-Mail eine Anlage hinzuzufügen, fügen Sie Werte für **Anlagenname** und **Anlageninhalt** hinzu.
    
    ![Screenshot: Senden mehrerer Anlagen per E-Mail](./media/flow-usage/flow-email-multiple-attachments.png)

    Weitere Informationen zum Erstellen einer E-Mail-Aktion finden Sie unter [Senden von Kusto-Abfrageergebnissen per E-Mail](flow.md#email-kusto-query-results). 

Ergebnisse:

:::image type="content" source="./media/flow-usage/flow-resultsmultipleattachments.png" alt-text="Ergebnisse mehrerer E-Mail-Anlagen, visualisiert als Kreis- und Balkendiagramm, Azure Data Explorer" lightbox="./media/flow-usage/flow-resultsmultipleattachments.png#lightbox":::

:::image type="content" source="./media/flow-usage/flow-resultsmultipleattachments2.png" alt-text="Ergebnisse mehrerer E-Mail-Anlagen, visualisiert als Zeitdiagramm, Azure Data Explorer" lightbox="./media/flow-usage/flow-resultsmultipleattachments2.png#lightbox":::

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über den [Azure Kusto-Logik-App-Connector](kusto/tools/logicapps.md), der eine weitere Möglichkeit darstellt, Kusto-Abfragen und -Befehle als Teil einer geplanten oder ausgelösten Aufgabe automatisch auszuführen.

