---
title: Verwendungsbeispiele für den Microsoft Azure Data Explorer-Flow-Connector (Vorschau)
description: Lernen Sie einige gängige Verwendungsbeispiele für den Microsoft Flow-Connector kennen.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 05ff0e41347d56cc1e204defa8d5f250a881c84f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81493446"
---
# <a name="microsoft-flow-connector-preview-usage-examples"></a>Verwendungsbeispiele für den Microsoft Flow-Connector (Vorschau)

Der Azure Data Explorer-Flow-Connector ermöglicht es Azure Data Explorer, die [Flow-Funktionen von Microsoft Power Automate](https://flow.microsoft.com/) zu nutzen, um Kusto-Abfragen und -Befehle automatisch als Teil einer geplanten oder ausgelösten Aufgabe auszuführen. Dieses Dokument enthält einige gängige Beispiele zur Verwendung des Microsoft Flow-Connectors.

Weitere Informationen finden Sie unter [Microsoft Flow-Connector (Vorschau)](flow.md).

* [Microsoft Flow-Connector und SQL](#microsoft-flow-connector-and-sql)
* [Push-Übertragung von Daten an ein Power BI-Dataset](#push-data-to-power-bi-dataset)
* [Bedingte Abfragen](#conditional-queries)
* [Mehrere Azure Data Explorer-Flow-Diagramme per E-Mail senden](#email-multiple-azure-data-explorer-flow-charts)

## <a name="microsoft-flow-connector-and-sql"></a>Microsoft Flow-Connector und SQL

Verwenden Sie den Microsoft Flow-Connector, um Ihre Daten abzufragen und in einer SQL-Datenbank zu aggregieren.

> [!Note]
> Die SQL-Einfügung wird für jede Zeile separat durchgeführt. Verwenden Sie den Microsoft Flow-Connector nur für geringe Mengen von Ausgabedaten. 

![](./media/flow-usage/flow-sqlexample.png)

> [!IMPORTANT]
> Geben Sie in das Feld *Clustername* die Cluster-URL ein.

## <a name="push-data-to-power-bi-dataset"></a>Push-Übertragung von Daten an ein Power BI-Dataset

Der Microsoft Flow-Connector kann zusammen mit dem Power BI-Connector verwendet werden, um Daten von Kusto-Abfragen an Power BI-Streamingdatasets zu pushen.

1. Erstellen Sie eine neue Aktion für „Abfrage ausführen und Ergebnisse auflisten“.
1. Wählen Sie **Neuer Schritt** aus.
1. Wählen Sie **Aktion hinzufügen** aus, und suchen Sie nach „Power BI“.
1. Wählen Sie **Power BI**aus.
1. Wählen Sie **Zeilen einem Dataset hinzufügen** aus. 

    ![Flow Power BI-Connector](./media/flow-usage/flow-powerbiconnector.png)
1. Geben Sie **Arbeitsbereich**, **Dataset** und **Tabelle** ein, in die die Daten gepusht werden sollen.
1. Fügen Sie im Dialogfeld für dynamische Inhalte eine Nutzlast hinzu, die Ihr Datasetschema und die relevanten Kusto-Abfrageergebnisse enthält.

    ![Felder für Flow Power BI](./media/flow-usage/flow-powerbifields.png)

Flow wendet automatisch die Power BI-Aktion für jede Zeile der Kusto-Abfrageergebnistabelle an. 

![Flow Power BI-Aktion für die einzelnen Zeilen](./media/flow-usage/flow-powerbiforeach.png)

## <a name="conditional-queries"></a>Bedingte Abfragen

Die Ergebnisse von Kusto-Abfragen können als Eingabe oder Bedingungen für die nächsten Flow-Aktionen verwendet werden.

Im folgenden Beispiel wird Kusto nach Vorfällen abgefragt, die sich am letzten Tag ereignet haben. Für jeden behandelten Vorfall wird eine Slack-Nachricht gepostet und eine Pushbenachrichtigung erstellt.
Für jeden noch aktiven Vorfall wird Kusto nach weiteren Informationen zu ähnlichen Vorfällen abgefragt. Diese Informationen werden als E-Mail gesendet und eine zugehörige TFS-Aufgabe geöffnet.

Gehen Sie wie folgt vor, um einen ähnlichen Flow zu erstellen:

1. Erstellen Sie eine neue Aktion für „Abfrage ausführen und Ergebnisse auflisten“.
1. Wählen Sie **Neuer Schritt** aus.
1. Wählen Sie **Bedingungssteuerelement** aus.
1. Wählen Sie im Fenster für dynamische Inhalte den Parameter aus, den Sie als Bedingung für die nächsten Aktionen verwenden möchten.
1. Wählen Sie den Typ von *Beziehung* und *Wert* aus, um eine bestimmte Bedingung für den gegebenen Parameter festzulegen.

    [![](./media/flow-usage/flow-condition.png "Flow conditions")](./media/flow-usage/flow-condition.png#lightbox)

    Flow wendet diese Bedingung auf jede Zeile der Abfrageergebnistabelle an.
1. Fügen Sie Aktionen hinzu, wenn die Bedingung wahr (true) und falsch (false) ist.

    [![](./media/flow-usage/flow-conditionactions.png "Flow condition actions")](./media/flow-usage/flow-conditionactions.png#lightbox)

Sie können die Ergebniswerte aus der Kusto-Abfrage als Eingabe für die nächsten Aktionen verwenden. Wählen Sie die Ergebniswerte aus dem Fenster für dynamische Inhalte aus.
Im folgenden Beispiel wurden die Aktionen „Slack – Nachricht veröffentlichen“ und „Visual Studio – Neues Arbeitselement erstellen“ mit Daten aus der Kusto-Abfrage hinzugefügt.

![Aktion „Slack – Nachricht veröffentlichen“](./media/flow-usage/flow-slack.png)

![Visual Studio-Aktion](./media/flow-usage/flow-visualstudio.png)

Wenn in diesem Beispiel noch ein Vorfall aktiv ist, fragen Sie Kusto erneut ab, um Informationen darüber zu erhalten, wie Vorfälle aus derselben Quelle in der Vergangenheit behoben wurden.

![Flow-Bedingungsabfrage](./media/flow-usage/flow-conditionquery.png)

> [!IMPORTANT]
> Geben Sie in das Feld *Clustername* die Cluster-URL ein.

Visualisieren Sie diese Informationen als Kreisdiagramm, und senden Sie sie per E-Mail an das Team.

![E-Mail zur Flow-Bedingung](./media/flow-usage/flow-conditionemail.png)

## <a name="email-multiple-azure-data-explorer-flow-charts"></a>Mehrere Azure Data Explorer-Flow-Diagramme per E-Mail senden

1. Erstellen Sie einen neuen Flow mit dem Trigger „Serie“, und definieren Sie das Intervall des Flows und die Frequenz. 
1. Fügen Sie einen neuen Schritt hinzu, mit einer oder mehreren Aktionen für „Kusto – Abfrage ausführen und Ergebnisse visualisieren“. 

    ![Ausführen mehrerer Abfragen in einem Flow](./media/flow-usage/flow-severalqueries.png)
1. Definieren Sie für jede Aktion für „Kusto – Abfrage ausführen und Ergebnisse visualisieren“ die folgenden Felder:
    * Cluster-URL
    * Datenbankname
    * Abfrage- und Diagrammtyp (HTML-Tabelle, Kreisdiagramm, Zeitdiagramm, Balkendiagramm oder Eingabe eines benutzerdefinierten Werts).

    ![Visualisieren von Ergebnissen mit mehreren Anlagen](./media/flow-usage/flow-visualizeresultsmultipleattachments.png)

1. Fügen Sie eine Aktion „E-Mail senden (v2)“ hinzu: 
    1. Wählen Sie im Hauptabschnitt das Symbol für die Codeansicht aus.
    1. Fügen Sie im Feld **Text** den erforderlichen BodyHtml-Code ein, sodass das visualisierte Ergebnis der Abfrage im Textkörper der E-Mail enthalten ist.
    1. Um der E-Mail eine Anlage hinzuzufügen, fügen Sie den Namen und den Inhalt der Anlage hinzu.
    
    ![Mehrere Anlagen per E-Mail senden](./media/flow-usage/flow-email-multiple-attachments.png)

    Vollständige Anweisungen zum Erstellen einer E-Mail-Aktion finden Sie unter [Senden von Kusto-Abfrageergebnisse per E-Mail](flow.md#email-kusto-query-results). 

Ergebnisse:

[![](./media/flow-usage/flow-resultsmultipleattachments.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments.png#lightbox)

[![](./media/flow-usage/flow-resultsmultipleattachments2.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments2.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über den [Microsoft Azure Explorer-Logic-App-Connector](kusto/tools/logicapps.md), der eine weitere Möglichkeit darstellt, Kusto-Abfragen und -Befehle automatisch als Teil einer geplanten oder ausgelösten Aufgabe auszuführen.
