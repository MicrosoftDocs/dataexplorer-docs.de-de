---
title: Microsoft Flow Azure Kusto Connector (Vorschau) - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird Microsoft Flow Azure Kusto Connector (Vorschau) in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: c4e732afb9e6165f803b9dc7e801b4e2be4c2588
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504096"
---
# <a name="microsoft-flow-azure-kusto-connector-preview"></a>Microsoft Flow Azure Kusto-Connector (Vorschau)

Mit dem Microsoft Flow Azure Kusto-Connector können Benutzer Kusto-Abfragen und -Befehle automatisch als Teil einer geplanten oder ausgelösten Aufgabe mithilfe von [Microsoft Flow](https://flow.microsoft.com/)ausführen.

Allgemeine Verwendungsszenarien:

* Senden von täglichen Berichten mit Tabellen und Diagrammen
* Festlegen von Benachrichtigungen basierend auf Abfrageergebnissen
* Planen von Steuerungsbefehlen auf Clustern
* Exportieren und Importieren von Daten zwischen Azure Data Explorer und anderen Datenbanken 

## <a name="limitations"></a>Einschränkungen

1. Die an den Client zurückgegebenen Ergebnisse sind auf 500.000 Datensätze beschränkt. Der Gesamtspeicher für diese Datensätze darf 64 MB und 7 Minuten Ausführungszeit nicht überschreiten.
2. Derzeit unterstützt der Connector die [Gabel-](../query/forkoperator.md) und [Facettenoperatoren](../query/facetoperator.md) nicht.
3. Flow funktioniert am besten auf Internet Explorer und Chrome.

##  <a name="login"></a>Anmeldename 

1. Melden Sie sich bei [Microsoft Flow](https://flow.microsoft.com/)an.

1. Wenn Sie zum ersten Mal eine Verbindung mit dem Azure Kusto-Connector herstellen, werden Sie aufgefordert, sich anzumelden.

1. Klicken Sie auf die Schaltfläche **Anmelden,** und geben Sie Ihre Anmeldeinformationen ein, um Azure Kusto Flow zu verwenden.

![alt text](./Images/KustoTools-Flow/flow-signin.png "flow-signin")

## <a name="authentication"></a>Authentifizierung

Die Authentifizierung für den Azure Kusto Flow-Connector kann mithilfe von Benutzeranmeldeinformationen oder einer AAD-Anwendung durchgeführt werden.



### <a name="aad-application-authentication"></a>AAD-Anwendungsauthentifizierung

Sie können sich mit den folgenden Schritten bei Azure Kusto Flow mit einer AAD-Anwendung authentifizieren:

> Hinweis: Stellen Sie sicher, dass es sich bei Ihrer Anwendung um eine [AAD-Anwendung](../management/access-control/how-to-provision-aad-app.md) handelt und dass sie berechtigt ist, Abfragen in Ihrem Cluster auszuführen.

1. Klicken Sie auf die drei Punkte oben rechts im Azure Kusto-Connector: ![Alt-Text](./Images/KustoTools-Flow/flow-addconnection.png "flow-addconnection")

1. Wählen Sie "Neue Verbindung hinzufügen" und klicken Sie dann auf "Mit Service Principal verbinden".
![alt text](./Images/KustoTools-Flow/flow-signin.png "flow-signin")

1. Geben Sie die Anwendungs-ID, den Anwendungsschlüssel und die Mandanten-ID ein.

Die Microsoft-Mandanten-ID lautet beispielsweise: 72f988bf-86f1-41af-91ab-2d7cd011db47.
Der Verbindungsname-Wert ist eine Zeichenfolge Ihrer Wahl, die zum Erkennen der neuen Verbindung hinzugefügt wird.
![alt text](./Images/KustoTools-Flow/flow-appauth.png "flow-appauth")

Nach Abschluss der Authentifizierung können Sie sehen, dass Ihr Flow die neue hinzugefügte Verbindung verwendet.
![alt text](./Images/KustoTools-Flow/flow-appauthcomplete.png "flow-appauthcomplete")

Von nun an wird dieser Flow mit den Anmeldeinformationen der Anwendung ausgeführt.

## <a name="find-the-azure-kusto-connector"></a>Suchen des Azure Kusto-Connectors

Um den Azure Kusto-Connector verwenden zu können, müssen Sie zunächst einen Trigger hinzufügen. Ein Trigger kann basierend auf einem wiederkehrenden Zeitraum oder als Antwort auf eine vorherige Flussaktion definiert werden.

1. [Erstellen Sie einen neuen Flow.](https://flow.microsoft.com/manage/flows/new)
2. Fügen Sie als ersten Schritt 'Schedule - Recurrence' hinzu.
3. Geben Sie "Azure Kusto" in das Suchfeld des zweiten Schritts ein.

Jetzt sollten Sie in der Lage sein, "Azure Kusto" zu sehen, wie in der Abbildung unten zu sehen.

![alt text](./Images/KustoTools-Flow/flow-actions.png "Flow-Aktionen")

## <a name="azure-kusto-flow-actions"></a>Azure Kusto Flow-Aktionen

Bei der Suche nach dem Azure Kusto-Connector in Flow werden 3 mögliche Aktionen angezeigt, die Sie Ihrem Flow hinzufügen können.

Im folgenden Abschnitt werden die Funktionen und Parameter für jede Azure Kusto Flow-Aktion beschrieben.

![alt text](./Images/KustoTools-Flow/flow-actions.png "Flow-Aktionen")

### <a name="azure-kusto---run-query-and-visualize-results"></a>Azure Kusto - Abfrage ausführen und Ergebnisse visualisieren

> Hinweis: Wenn Ihre Abfrage mit einem Punkt beginnt (d. h. es handelt sich um einen [Steuerbefehl](../management/index.md)), verwenden Sie bitte den [Befehl "Steuerung ausführen" und visualisieren Sie die Ergebnisse](./flow.md#azure-kusto---run-control-command-and-visualize-results)

Um das Kusto-Abfrageergebnis als Tabelle oder Diagramm zu visualisieren, können Sie die Aktion "Azure Kusto - Abfrage ausführen und Ergebnisse visualisieren" verwenden. Die Ergebnisse dieser Aktion können später per E-Mail gesendet werden. Sie können diesen Flow z. B. verwenden, wenn Sie tägliche ICM-Berichte erhalten möchten. 

![alt text](./Images/KustoTools-Flow/flow-runquery.png "Flow-Runquery")

In diesem Beispiel werden die Ergebnisse der Abfrage als HTML-Tabelle zurückgegeben.

### <a name="azure-kusto---run-control-command-and-visualize-results"></a>Azure Kusto - Befehl "Steuerung ausführen" und Ergebnisse visualisieren

Ähnlich wie bei der Aktion "Azure Kusto - Abfrage ausführen und Ergebnisse visualisieren" können Sie auch einen [Steuerelementbefehl](../management/index.md) mit der Aktion "Azure Kusto - Control command and visualize results" ausführen.
Die Ergebnisse dieser Aktion können später per E-Mail als Tabelle oder Diagramm gesendet werden.

![alt text](./Images/KustoTools-Flow/flow-runcontrolcommand.png "flow-runcontrolcommand")

In diesem Beispiel werden die Ergebnisse des Befehls "Steuerelement" als Kreisdiagramm gerendert.

### <a name="azure-kusto---run-query-and-list-results"></a>Azure Kusto - Abfrage- und Listenergebnisse ausführen

> Hinweis: Falls Ihre Abfrage mit einem Punkt beginnt (d. h. es handelt sich um einen [Steuerbefehl](../management/index.md)), verwenden Sie bitte den [Befehl "Steuerung ausführen" und visualisieren Sie die Ergebnisse](./flow.md#azure-kusto---run-control-command-and-visualize-results)

Diese Aktion sendet eine Abfrage an den Kusto-Cluster. Die Aktionen, die anschließend hinzugefügt werden, iterieren über jede Zeile der Ergebnisse der Abfrage.

Im folgenden Beispiel wird jede Minute eine Abfrage ausgelöst und eine E-Mail basierend auf den Abfrageergebnissen gesendet. Die Abfrage überprüft die Anzahl der Zeilen in der Datenbank und sendet dann eine E-Mail nur, wenn die Anzahl der Zeilen größer als 0 ist. 

![alt text](./Images/KustoTools-Flow/flow-runquerylistresults.png "flow-runquerylistresults")

Beachten Sie, dass für den Fall, dass die Spalte mehrere Zeilen hat, der folgende Connector für jede Zeile in der Spalte ausgeführt wird.

## <a name="email-kusto-query-results"></a>Daten zu Kusto-Abfrageergebnissen per E-Mail senden

Gehen Sie wie folgt vor, um E-Mail-Berichte zu senden: 

![alt text](./Images/KustoTools-Flow/flow-sendemail.png "flow-sendemail")

1. Klicken Sie auf '+ Neuer Schritt', dann auf 'Aktion hinzufügen'.
2. Geben Sie im Suchfeld "Office 365 Outlook - Senden einer E-Mail" ein.
3. Legen Sie das "An" auf Ihre E-Mail-Adresse, den "Betreff" auf einen Text fest, und fügen Sie dem Feld "Körper" "Körper" von dynamischem Inhalt hinzu.
4. Klicken Sie auf 'Erweiterte Optionen' fügen Sie 'Attachment Name' zum Feld 'Attachments Name', 'Attachment Content' zum Feld 'Attachments Content' hinzu, und stellen Sie sicher, dass 'Ist HTML' auf 'Ja' gesetzt ist.
5. Legen Sie in der oberen Leiste den 'Flow-Namen' für diesen Flow fest.
6. Klicken Sie auf 'Flow erstellen' und Sie sind fertig!

## <a name="how-to-make-sure-flow-succeeded"></a>Wie kann sichergestellt werden, dass der Fluss erfolgreich ist?

Gehen Sie zu [Microsoft Flow Home Page](https://flow.microsoft.com/), klicken Sie auf Meine [Flows](https://flow.microsoft.com/manage/flows) und dann auf die i-Schaltfläche.

![alt text](./Images/KustoTools-Flow/flow-myflows.png "Flow-Myflows")

Alle Flow-Läufe werden mit Status, Startzeit und Dauer aufgelistet.

![alt text](./Images/KustoTools-Flow/flow-runs.png "Flow-Runs")

Wenn beim Öffnen des letzten Durchlaufs des Flows alle Schritte des Flows mit einem grünen V markiert sind, wurde der Flow erfolgreich beendet.
Erweitern Sie andernfalls den Schritt, der mit einem roten Ausrufezeichen markiert wurde, um Fehlerdetails anzuzeigen.

![alt text](./Images/KustoTools-Flow/flow-failed.png "Flow-failed")

Einige Fehler können einfach selbst behoben werden, z. B. Abfragesyntaxfehler:

![alt text](./Images/KustoTools-Flow/flow-syntaxerror.png "Flow-Syntaxerror")



## <a name="having-a-timeout-exception"></a>Haben Sie eine Timeout-Ausnahme?

Ihr Flow kann fehlschlagen und die Ausnahme "RequestTimeout" zurückgeben, wenn er mehr als 7 Minuten läuft.

Beachten Sie, dass 7 Minuten die maximale Zeitflussabfrage ist, die ausgeführt werden kann, bevor eine Timeoutausnahme angezeigt wird.

[Klicken Sie hier,](#limitations) um die Einschränkungen von Microsoft Flow anzuzeigen.

Dieselbe Abfrage kann erfolgreich im Kusto Explorer ausgeführt werden, wo die Zeit nicht begrenzt ist und geändert werden kann.

Die Ausnahme "RequestTimeout" ist in der Folgenden Abbildung dargestellt:

![alt text](./Images/KustoTools-Flow/flow-requesttimeout.png "Flow-Requesttimeout")

Um das Problem zu beheben, können Sie die folgenden Schritte ausführen:
1. Lesen Sie mehr über [Best Practices für Abfragen](../query/best-practices.md).
2. Versuchen Sie, die Abfrage effizienter zu gestalten, um sie schneller laufen zu lassen, oder trennen Sie sie in Abschnitte, wobei jeder Teilabschnitt auf einem anderen Teil der Abfrage ausgeführt werden kann.

## <a name="usage-examples"></a>Anwendungsbeispiele

Dieser Abschnitt enthält mehrere häufige Beispiele für die Verwendung des Azure Kusto Flow-Connectors.

### <a name="example-1---azure-kusto-flow-and-sql"></a>Beispiel 1 - Azure Kusto Flow und SQL

Sie können Azure Kusto flow verwenden, um die Daten abzufragen und sie dann in einer SQL-DB anzusammeln. 
> Hinweis: SQL-Einfügung erfolgt separat pro Zeile, bitte verwenden Sie dies nur für geringe Mengen an Ausgabedaten. 

![alt text](./Images/KustoTools-Flow/flow-sqlexample.png "flow-sqlbeispiel")

### <a name="example-2---push-data-to-power-bi-dataset"></a>Beispiel 2 - Übertragen von Daten in Power BI-Dataset

Der Azure Kusto Flow-Connector kann zusammen mit dem Power BI-Connector verwendet werden, um Daten aus Kusto-Abfragen an Power BI-Streaming-Datasets zu übertragen.

Erstellen Sie zunächst eine neue Aktion "Kusto - Abfrage ausführen und Ergebnisse auflisten".

![alt text](./Images/KustoTools-Flow/flow-listresults.png "Flow-Listenergebnisse")

Klicken Sie auf 'Neuer Schritt', wählen Sie 'Aktion hinzufügen' und suchen Sie nach 'Power BI'. Klicken Sie auf 'Power BI - Hinzufügen von Zeilen zu einem Dataset'. 

![alt text](./Images/KustoTools-Flow/flow-powerbiconnector.png "Flow-Powerbiconnector")

Geben Sie den Arbeitsbereich, das Dataset und die Tabelle ein, an die Daten übertragen werden sollen.
Fügen Sie eine Nutzlast hinzu, die Ihr Dataset-Schema und die relevanten Kusto-Abfrageergebnisse aus dem dynamischen Inhaltsfenster enthält. 

![alt text](./Images/KustoTools-Flow/flow-powerbifields.png "Flow-Powerbifields")

Beachten Sie, dass Flow automatisch die Power BI-Aktion für jede Zeile der Kusto-Abfrageergebnistabelle anwendet. 

![alt text](./Images/KustoTools-Flow/flow-powerbiforeach.png "Flow-Powerbiforeach")

### <a name="example-3---conditional-queries"></a>Beispiel 3 - Bedingte Abfragen

Die Ergebnisse von Kusto-Abfragen können als Eingabe oder Bedingungen für die nächsten Flow-Aktionen verwendet werden.

Im folgenden Beispiel fragen wir Kusto nach Vorfällen, die am letzten Tag aufgetreten sind. Für jeden behobenen Vorfall veröffentlichen wir eine Puffernachricht darüber und erstellen eine Pushbenachrichtigung.
Für jeden noch aktiven Vorfall fragen wir Kusto nach weiteren Informationen zu ähnlichen Vorfällen, senden diese Informationen als E-Mail und öffnen eine zugehörige TFS-Aufgabe.

Befolgen Sie diese Anweisungen, um einen ähnlichen Flow zu erstellen.

Erstellen Sie eine neue Aktion "Kusto - Abfrage ausführen und Ergebnisse auflisten".
Klicken Sie auf 'Neuer Schritt' und wählen Sie 'Bedingung hinzufügen' 

![alt text](./Images/KustoTools-Flow/flow-listresults.png "Flow-Listenergebnisse")

Wählen Sie aus dem Fenster "Dynamischer Inhalt" den Parameter aus, den Sie als Bedingung für die nächsten Aktionen verwenden möchten.
Wählen Sie den Typ von Beziehung und Wert aus, um eine bestimmte Bedingung für den gegebenen Parameter festzulegen.

![alt text](./Images/KustoTools-Flow/flow-condition.png "Durchflussbedingung")

Beachten Sie, dass Flow diese Bedingung auf jede Zeile der Abfrageergebnistabelle anwendet.

Fügen Sie Aktionen hinzu, wenn die Bedingung wahr (true) und falsch (false) ist.

![alt text](./Images/KustoTools-Flow/flow-conditionactions.png "Flow-Condition-Aktionen")

Sie können Ergebniswerte aus der Kusto-Abfrage als Eingabe für die nächsten Aktionen verwenden, indem Sie sie aus dem Fenster "Dynamischer Inhalt" auswählen.
Im Folgenden haben wir die Aktion 'Slack - Post Message' und 'Visual Studio - Create a new work work work' hinzugefügt, die Daten aus der Kusto-Abfrage enthält.

![alt text](./Images/KustoTools-Flow/flow-slack.png "Flow-Slack")

![alt text](./Images/KustoTools-Flow/flow-visualstudio.png "flow-visualstudio")

Wenn in diesem Beispiel ein Vorfall noch aktiv ist, werden wir Kusto erneut abfragen, um Informationen darüber zu erhalten, wie Vorfälle aus derselben Quelle in der Vergangenheit gelöst wurden.

![alt text](./Images/KustoTools-Flow/flow-conditionquery.png "Flow-Conditionquery")

Wir visualisieren diese Informationen als Kreisdiagramm und senden sie per E-Mail an unser Team.

![alt text](./Images/KustoTools-Flow/flow-conditionemail.png "Flow-Conditionemail")

### <a name="example-4---email-multiple-azure-kusto-flow-charts"></a>Beispiel 4 - E-Mail mehreren Azure Kusto Flow-Diagrammen

Erstellen Sie einen neuen Flow mit dem Trigger "Recurrence", und definieren Sie das Intervall des Flows und der Frequenz. 

Fügen Sie einen neuen Schritt mit einer oder mehreren Aktionen "Kusto - Abfrage ausführen und Visualisierung der Ergebnisse" hinzu. 

![alt text](./Images/KustoTools-Flow/flow-severalqueries.png "flow-severalqueries")

Für jedes 'Kusto - Abfrage ausführen und Ergebnis visualisieren' definieren Sie die folgenden Felder:

Clustername, Datenbankname, Abfrage und Diagrammtyp (Html-Tabelle/ Kreisdiagramm/ Zeitdiagramm/ Balkendiagramm/ Benutzerdefinierter Wert eingeben).

![alt text](./Images/KustoTools-Flow/flow-visualizeresultsmultipleattachments.png "flow-visualizeresultsmultipleattachments")

Nachdem Sie die Aktionen "Kusto - Abfrage ausführen und Ergebnis visualisieren" abgeschlossen haben, fügen Sie die Aktion "E-Mail senden" hinzu. 

Stellen Sie sicher, dass Sie im Feld "Körper" den erforderlichen Text einfügen, um das Visualisierungsergebnis der Abfrage an den Text der E-Mail anzuhängen.
Um der E-Mail eine Anlage hinzuzufügen, fügen Sie außerdem "Anhangsname" und "Anfügeinhalt" hinzu, um eine Anlage hinzuzufügen.

Stellen Sie sicher, dass Sie unter dem Feld "ist HTML" "Ja" auswählen.

![alt text](./Images/KustoTools-Flow/flow-emailmultipleattachments.png "Flow-EmailmultipleAttachments")

Ergebnisse:

![alt text](./Images/KustoTools-Flow/flow-resultsmultipleattachments.png "flow-resultsmultipleattachments")

![alt text](./Images/KustoTools-Flow/flow-resultsmultipleattachments2.png "flow-resultsmultipleattachments2")

### <a name="example-5---send-a-different-email-to-different-contacts"></a>Beispiel 5 - Senden einer anderen E-Mail an verschiedene Kontakte

Sie können Azure Kusto Flow nutzen, um verschiedene benutzerdefinierte E-Mails an verschiedene Kontakte zu senden. Die E-Mail-Adressen sowie der E-Mail-Inhalt sind das Ergebnis einer Kusto-Abfrage.

Siehe das unten stehende Beispiel:

![alt text](./Images/KustoTools-Flow/flow-dynamicemailkusto.png "flow-dynamicemailkusto")

![alt text](./Images/KustoTools-Flow/flow-dynamicemail.png "flow-dynamicemail")

### <a name="example-6---create-custom-html-table"></a>Beispiel 6 - Erstellen einer benutzerdefinierten HTML-Tabelle

Sie können Azure Kusto Flow nutzen, um benutzerdefinierte HTML-Elemente wie eine benutzerdefinierte HTML-Tabelle zu erstellen und zu verwenden.

Im folgenden Beispiel wird veranschaulicht, wie Eine benutzerdefinierte HTML-Tabelle erstellt wird. Die HTML-Tabelle hat ihre Zeilen nach Protokollebene eingefärbt (wie im Kusto Explorer).

Gehen Sie wie folgt vor, um einen ähnlichen Flow zu erstellen:

Erstellen Sie eine neue Aktion "Kusto - Abfrage ausführen und Ergebnisse auflisten".

![alt text](./Images/KustoTools-Flow/flow-listresultforhtmltable.png "flow-listresultforhtmltable")

Durchlaufen Sie die Abfrageergebnisse, und erstellen Sie den HTML-Tabellentext. Erstellen Sie zunächst eine Variable, die die HTML-Zeichenfolge enthält - klicken Sie auf "Neuer Schritt", wählen Sie 'Aktion hinzufügen' und suchen Sie nach 'Variablen'. Klicken Sie auf 'Variablen - Variable initialisieren'. Initialisieren Sie eine Zeichenfolgenvariable wie folgt:

![alt text](./Images/KustoTools-Flow/flow-initializevariable.png "Flow-Initializevariable")

Schleife über die Ergebnisse - klicken Sie auf 'Neuer Schritt' und wählen Sie 'Aktion hinzufügen'. Suchen Sie nach 'Variablen'. Wählen Sie 'Variables - An string variable anhängen'. Wählen Sie den Variablennamen aus, den Sie zuvor initialisiert haben, und erstellen Sie ihre HTML-Tabellenzeilen mithilfe der Abfrageergebnisse. Bei der Auswahl der Abfrageergebnisse wird "Auf jeden anwenden" automatisch hinzugefügt.

Im folgenden Beispiel wird der folgende Ausdruck verwendet, um den Stil jeder Zeile zu definieren:

if(equals(items('Apply_to_each')?[' Level'], 'Warnung'), 'Gelb', if(equals(items('Apply_to_each')?[' Level'], 'Error'), 'rot', 'weiß'))

![alt text](./Images/KustoTools-Flow/flow-createhtmltableloopcontent.png "flow-createhtmltableloopcontent")

Erstellen Sie schließlich den vollständigen HTML-Inhalt. Fügen Sie eine neue Aktion außerhalb von "Auf jeden anwenden" hinzu. Im folgenden Beispiel wird die Aktion "E-Mail senden" verwendet. Definieren Sie Ihre HTML-Tabelle mit der Variablen aus den vorherigen Schritten. Wenn Sie eine E-Mail senden, klicken Sie auf "Erweiterte Optionen anzeigen" und wählen Sie "Ja" unter "Ist HTML"

![alt text](./Images/KustoTools-Flow/flow-customhtmltablemail.png "flow-customhtmltablemail")

und hier ist das Ergebnis:

![alt text](./Images/KustoTools-Flow/flow-customhtmltableresult.png "flow-customhtmltableresult")






