---
title: Microsoft Azure Data Explorer-Flow-Connector (Vorschau)
description: Erfahren Sie mehr über die Verwendung des Microsoft Flow-Connectors zur Erstellung von Flows von automatisch geplanten oder ausgelösten Aufgaben.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 95b5e60c0993d3e364dc54ca7c06e06eaae876cb
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108285"
---
# <a name="microsoft-flow-connector-preview"></a>Microsoft Flow-Connector (Vorschau)

Der Azure Data Explorer-Flow-Connector ermöglicht es Azure Data Explorer, die [Flow-Funktionen von Microsoft Power Automate](https://flow.microsoft.com/) zu nutzen, um Kusto-Abfragen und -Befehle automatisch als Teil einer geplanten oder ausgelösten Aufgabe auszuführen.

Allgemeine Verwendungsszenarien:

* Senden von täglichen Berichten mit Tabellen und Diagrammen
* Festlegen von Benachrichtigungen basierend auf Abfrageergebnissen
* Planen von Steuerungsbefehlen auf Clustern
* Exportieren und Importieren von Daten zwischen Azure Data Explorer und anderen Datenbanken 

Weitere Informationen finden Sie unter [Verwendungsbeispiele für den Microsoft Flow-Connector](flow-usage.md).

##  <a name="log-in"></a>Anmelden 

1. Melden Sie sich bei [Microsoft Power Automate](https://flow.microsoft.com/) an.

1. Wenn Sie zum ersten Mal eine Verbindung herstellen, werden Sie aufgefordert, sich anzumelden.

1. Wählen Sie **Anmelden**, und geben Sie Ihre Anmeldeinformationen ein.

![Anmeldedialogfeld](./media/flow/flow-signin.png)

## <a name="authentication"></a>Authentifizierung

Sie können sich mit Benutzeranmeldeinformationen oder einer AAD-Anwendung authentifizieren.

> [!Note]
> Stellen Sie sicher, dass Ihre Anwendung eine [AAD-Anwendung](kusto/management/access-control/how-to-provision-aad-app.md) und zudem berechtigt ist, Abfragen in Ihrem Cluster auszuführen.

1. Wählen Sie die drei Punkte oben rechts im Microsoft Flow-Connector aus: ![Verbindung hinzufügen](./media/flow/flow-addconnection.png)

1. Wählen Sie **Neue Verbindung hinzufügen** und dann **Mit Dienstprinzipal verbinden** aus.
![Anmeldedialogfeld](./media/flow/flow-signin.png)

1. Geben Sie die erforderlichen Informationen ein:
    * Verbindungsname: Ein beschreibender und aussagekräftiger Name für die neue Verbindung.
    * Client-ID: Ihre Anwendungs-ID.
    * Geheimer Clientschlüssel: Ihr Anwendungsschlüssel
    * Mandant: Die ID des AAD-Verzeichnisses, in dem Sie die Anwendung erstellt haben. Die Mandanten-ID von Microsoft lautet z. B.: 72f988bf-86f1-41af-91ab-2d7cd011db47

![Anwendungsauthentifizierung](./media/flow/flow-appauth.png)

Wenn die Authentifizierung abgeschlossen ist, werden Sie sehen, dass Ihr Flow die neu hinzugefügte Verbindung verwendet.

![Abgeschlossene Anwendungsauthentifizierung](./media/flow/flow-appauthcomplete.png)

Ab jetzt wird dieser Flow unter Verwendung dieser Anwendungsanmeldeinformationen ausgeführt.

## <a name="find-the-azure-kusto-connector"></a>Suchen des Azure Kusto-Connectors

Um den Microsoft Flow-Connector zu verwenden, müssen Sie zunächst einen Trigger hinzufügen. Ein Trigger kann auf der Grundlage eines wiederkehrenden Zeitraums oder als Reaktion auf eine vorherige Flow-Aktion definiert werden.

1. [Erstellen Sie einen neuen Flow](https://flow.microsoft.com/manage/flows/new), oder wählen Sie auf der Startseite die Aktion **Meine Flows** und dann **+ Neu** aus.

    ![Erstellen eines neuen Flows](./media/flow/flow-newflow.png)

1. Fügen Sie „Geplant – ohne Vorlage“ hinzu.

    ![Neuer geplanter Flow](./media/flow/flow-scheduled-from-blank.png)

1. Geben Sie die erforderlichen Informationen auf der Seite „Geplanten Flow erstellen“ ein.
    ![Geplanten Flow erstellen](./media/flow/flow-build-scheduled-flow.png)
1. Klicken Sie auf **Erstellen**.
1. Wählen Sie **+ Neuer Schritt**aus.
1. Geben Sie in das Suchfeld „Kusto“ ein.

    ![Auswählen von Flow-Aktionen](./media/flow/flow-actions.png)

1. Wählen Sie **Azure Data Explorer** aus.

## <a name="flow-actions"></a>Flow-Aktionen

Wenn Sie den Azure Data Explorer-Connector öffnen, können Sie dem Flow drei mögliche Aktionen hinzufügen.

In diesem Abschnitt werden die Funktionen und Parameter für die einzelnen Microsoft Flow-Aktionen beschrieben.

![Azure Data Explorer-Flow-Aktionen](./media/flow/flow-adx-actions.png)

### <a name="run-control-command-and-visualize-results"></a>Steuerungsbefehl ausführen und Ergebnisse visualisieren

Verwenden Sie die Aktion „Steuerungsbefehl ausführen und Ergebnisse visualisieren“, um einen [Steuerungsbefehl](kusto/management/index.md) auszuführen.

1. Geben Sie die Cluster-URL an. Zum Beispiel, `https://clusterName.eastus.kusto.windows.net`
1. Geben Sie den Namen der Datenbank ein.
1. Geben Sie den Steuerungsbefehl an:
    * Wählen Sie dynamischen Inhalt aus den im Flow verwendeten Apps und Connectors aus.
    * Fügen Sie einen Ausdruck hinzu, um auf Werte zuzugreifen, sie zu konvertieren und zu vergleichen.
1. Geben Sie zum Senden der Ergebnisse dieser Aktion per E-Mail in Form einer Tabelle oder eines Diagramms den Diagrammtyp an, der Folgendes sein kann:
    * Eine HTML-Tabelle
    * Ein Kreisdiagramm
    * Ein Zeitdiagramm
    * Ein Balkendiagramm

![Ausführen eines Flow-Steuerungsbefehls](./media/flow/flow-runcontrolcommand.png)

> [!IMPORTANT]
> Geben Sie in das Feld *Clustername* die Cluster-URL ein.

### <a name="run-query-and-list-results"></a>Abfrage ausführen und Ergebnisse auflisten

> [!Note]
> Wenn Ihre Abfrage mit einem Punkt beginnt (d. h. es handelt sich um einen [Steuerungsbefehl](kusto/management/index.md)), verwenden Sie [Steuerungsbefehl ausführen und Ergebnisse visualisieren](#run-control-command-and-visualize-results).

Diese Aktion sendet eine Abfrage an den Kusto-Cluster. Die Aktionen, die später hinzugefügt werden, durchlaufen jede Zeile der Ergebnisse der Abfrage.

Im folgenden Beispiel wird jede Minute eine Abfrage ausgelöst und basierend auf den Abfrageergebnissen eine E-Mail gesendet. Die Abfrage prüft die Anzahl der Zeilen in der Datenbank und sendet dann nur dann eine E-Mail, wenn die Zeilenanzahl größer als 0 (null) ist. 

![Abfragelistenergebnisse ausführen](./media/flow/flow-runquerylistresults-2.png)

> [!Note]
> Wenn die Spalte mehrere Zeilen enthält, wird der Connector für jede Zeile in der Spalte ausgeführt.

### <a name="run-query-and-visualize-results"></a>Abfrage ausführen und Ergebnisse visualisieren
        
> [!Note]
> Wenn Ihre Abfrage mit einem Punkt beginnt (d. h. es handelt sich um einen [Steuerungsbefehl](kusto/management/index.md)), verwenden Sie [Steuerungsbefehl ausführen und Ergebnisse visualisieren](#run-control-command-and-visualize-results).
        
Verwenden Sie die Aktion „Abfrage ausführen und Ergebnisse visualisieren“, um das Kusto-Abfrageergebnis als Tabelle oder Diagramm zu visualisieren. Verwenden Sie diesen Flow z. B., um tägliche ICM-Berichte per E-Mail zu erhalten. 
    
In diesem Beispiel werden die Ergebnisse der Abfrage als HTML-Tabelle zurückgegeben.
            
![Abfrage ausführen und Ergebnisse visualisieren](./media/flow/flow-runquery.png)

> [!IMPORTANT]
> Geben Sie in das Feld *Clustername* die Cluster-URL ein.

## <a name="email-kusto-query-results"></a>Kusto-Abfrageergebnisse per E-Mail senden

Sie können einen Schritt in einen beliebigen Flow einbeziehen, um Berichte per E-Mail an eine beliebige E-Mail-Adresse zu senden. 

1. Wählen Sie **+ Neuer Schritt** aus, um einen neuen Schritt zu Ihrem Flow hinzuzufügen.
1. Geben Sie in das Suchfeld „Office 365“ ein, und wählen Sie **Office 365 Outlook** aus.
1. Wählen Sie **E-Mail senden (V2)** aus.
1. Geben Sie die E-Mail-Adresse ein, an die der E-Mail-Bericht gesendet werden soll.
1. Geben Sie den Betreff der E-Mail ein.
1. Wählen Sie **Codeansicht** aus.
1. Platzieren Sie den Cursor in das Feld *Text*, und wählen Sie **Dynamischen Inhalt hinzufügen** aus.
1. Wählen Sie **BodyHtml** aus.
    ![Senden von E-Mails](./media/flow/flow-send-email.png)
1. Wählen Sie **Erweiterte Optionen anzeigen** aus.
1. Wählen Sie im Feld *Anlagenname -1* die Option **Anlagenname** aus.
1. Wählen Sie im Feld *Anlageninhalt* die Option **Anlageninhalt** aus.
1. Fügen Sie bei Bedarf weitere Anlagen hinzu. 
1. Legen Sie, falls erforderlich, die Wichtigkeitsstufe fest.
1. Wählen Sie **Speichern** aus.

![Senden von E-Mail](./media/flow/flow-add-attachments.png)

## <a name="check-if-your-flow-succeeded"></a>Prüfen Sie, ob Ihr Flow erfolgreich war.

Um zu überprüfen, ob Ihr Flow erfolgreich war, betrachten Sie sich den Verlauf des Flows:
1. Gehen Sie zur [Microsoft Flow-Homepage](https://flow.microsoft.com/).
1. Wählen Sie im Hauptmenü [Meine Flows](https://flow.microsoft.com/manage/flows) aus.
    ![Seite „Meine Flows“](./media/flow/flow-myflows.png)
1. Wählen Sie in der Zeile des Flows, den Sie untersuchen möchten, das Symbol für weitere Befehle und dann **Ausführungsverlauf** aus.

    ![Menü „Ausführungsverlauf“](./media/flow//flow-runhistory.png)

    Alle Flow-Ausführungen werden mit Startzeit, Dauer und Status aufgelistet.
    ![Seite mit Ergebnissen für den Ausführungsverlauf](./media/flow/flow-runhistoryresults.png)

    Wählen Sie zum Abrufen der vollständigen Details zum Flow auf der Seite [Meine Flows](https://flow.microsoft.com/manage/flows) den Flow aus, den Sie untersuchen möchten.

    ![Seite mit den vollständigen Ergebnissen für den Ausführungsverlauf](./media/flow/flow-fulldetails.png) 

Wählen Sie die Startzeit der Ausführung aus, um zu prüfen, warum bei der Ausführung ein Fehler aufgetreten ist. Der Flow wird angezeigt, und der fehlerhafte Schritt des Flows wird durch ein rotes Ausrufezeichen gekennzeichnet. Erweitern Sie den fehlerhaften Schritt, um seine Details anzuzeigen. Der rechte Bereich enthält Informationen über den Fehler, damit Sie ihn beheben können.

![Flow-Fehler](./media/flow/flow-error.png)

## <a name="timeout-exceptions"></a>Timeoutausnahmen

Ihr Flow kann einen Fehler aufweisen und eine „RequestTimeout“-Ausnahme zurückgeben, wenn er länger als sieben Minuten ausgeführt wird.

Erfahren Sie mehr über [Microsoft Flow-Einschränkungen](#limitations).
    
Dieselbe Abfrage kann erfolgreich im Azure Data Explorer ausgeführt werden, wo die Zeit nicht begrenzt ist und geändert werden kann.
            
Die Ausnahme „RequestTimeout“ ist in der Abbildung unten dargestellt:
    
![Fehler durch Ausnahmefehler bei der Flow-Anforderung](./media/flow/flow-requesttimeout.png)
    
Wenn Sie ein Timeoutproblem beheben möchten, versuchen Sie, Ihre Abfrage effizienter zu gestalten, damit sie schneller ausgeführt wird, oder unterteilen Sie sie in Blöcke. Jeder Block kann in einem anderen Teil der Abfrage ausgeführt werden.

Weitere Informationen finden Sie unter [Bewährte Methoden für Abfragen](kusto/query/best-practices.md).

## <a name="limitations"></a>Einschränkungen

* An den Client zurückgegebene Ergebnisse sind auf 500.000 Datensätze begrenzt. Der gesamte Arbeitsspeicher für diese Datensätze darf 64 MB und eine Ausführungszeit von sieben Minuten nicht überschreiten.
* Der Connector unterstützt die Operatoren [fork](kusto/query/forkoperator.md) und [facet](kusto/query/facetoperator.md) nicht.
* Flow funktioniert am besten mit Microsoft Edge und Chrome.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über den [Microsoft Azure Explorer-Logic-App-Connector](kusto/tools/logicapps.md), der eine weitere Möglichkeit darstellt, Kusto-Abfragen und -Befehle automatisch als Teil einer geplanten oder ausgelösten Aufgabe auszuführen.
