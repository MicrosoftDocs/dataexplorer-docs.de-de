---
title: Azure Data Explorer-Connector für Power Automate (Vorschauversion)
description: Hier erfahren Sie mehr über die Verwendung des Azure Data Explorer-Connectors für Power Automate zur Erstellung von Flows von automatisch geplanten oder ausgelösten Aufgaben.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: 72d092683b490c7b58335abc59fd5e3aea2f3e26
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342942"
---
# <a name="azure-data-explorer-connector-to-power-automate-preview"></a>Azure Data Explorer-Connector für Power Automate (Vorschauversion)

Der Azure Data Explorer-Connector für Power Automate (vormals Microsoft Flow) ermöglicht es Azure Data Explorer, die Flowfunktionen von [Microsoft Power Automate](https://flow.microsoft.com/) zu nutzen. Sie können Kusto-Abfragen und -Befehle automatisch als Teil einer geplanten oder ausgelösten Aufgabe ausführen.

Ihre Möglichkeiten:

* Senden von täglichen Berichten mit Tabellen und Diagrammen
* Festlegen von Benachrichtigungen basierend auf Abfrageergebnissen
* Planen von Steuerungsbefehlen für Cluster
* Exportieren und Importieren von Daten zwischen Azure Data Explorer und anderen Datenbanken 

Weitere Informationen finden Sie unter [Anwendungsbeispiele für den Azure Data Explorer-Connector für Power Automate (Vorschauversion)](flow-usage.md).

##  <a name="sign-in"></a>Anmelden 

1. Wenn Sie zum ersten Mal eine Verbindung herstellen, werden Sie aufgefordert, sich anzumelden.

1. Wählen Sie **Anmelden** aus, und geben Sie Ihre Anmeldeinformationen ein.

![Screenshot: Anmeldeaufforderung von Azure Data Explorer](./media/flow/flow-signin.png)

## <a name="authentication"></a>Authentifizierung

Sie können sich mit Benutzeranmeldeinformationen oder mit einer Azure Active Directory-Anwendung (Azure AD) authentifizieren.

> [!Note]
> Stellen Sie sicher, dass Ihre Anwendung eine [Azure AD-Anwendung](./provision-azure-ad-app.md) und zudem berechtigt ist, Abfragen in Ihrem Cluster auszuführen.

1. Wählen Sie unter **Steuerungsbefehl ausführen und Ergebnisse visualisieren** die drei Punkte oben rechts im Flow-Connector aus.

   ![Screenshot: Steuerungsbefehl ausführen und Ergebnisse visualisieren](./media/flow/flow-addconnection.png)

1. Wählen Sie **Neue Verbindung hinzufügen** > **Mit Dienstprinzipal verbinden** aus.

   ![Screenshot: Anmeldeaufforderung von Azure Data Explorer mit der Option „Mit Dienstprinzipal verbinden“](./media/flow/flow-signin.png)

1. Geben Sie die erforderlichen Informationen ein:
   - Verbindungsname: Ein beschreibender und aussagekräftiger Name für die neue Verbindung.
   - Client-ID: Ihre Anwendungs-ID
   - Geheimer Clientschlüssel: Ihr Anwendungsschlüssel
   - Mandant: Die ID des Azure AD-Verzeichnisses, in dem Sie die Anwendung erstellt haben

   ![Screenshot: Azure Data Explorer-Dialogfeld für die Anwendungsauthentifizierung](./media/flow/flow-appauth.png)

Wenn die Authentifizierung abgeschlossen ist, werden Sie sehen, dass Ihr Flow die neu hinzugefügte Verbindung verwendet.

![Screenshot: Abgeschlossene Anwendungsauthentifizierung](./media/flow/flow-appauthcomplete.png)

Ab jetzt wird dieser Flow unter Verwendung dieser Anwendungsanmeldeinformationen ausgeführt.

## <a name="find-the-azure-kusto-connector"></a>Suchen des Azure Kusto-Connectors

Um den Power Automate-Connector zu verwenden, müssen Sie zunächst einen Trigger hinzufügen. Sie können einen Trigger auf der Grundlage eines wiederkehrenden Zeitraums oder als Reaktion auf eine vorherige Flow-Aktion festlegen.

1. [Erstellen Sie einen neuen Flow](https://flow.microsoft.com/manage/flows/new), oder wählen Sie auf der Startseite von Microsoft Power Automate die Optionen **Meine Flows** >  **+ Neu** aus.

    ![Screenshot: Startseite von Microsoft Power Automate mit den hervorgehobenen Optionen „Meine Flows“ und „Neu“](./media/flow/flow-newflow.png)

1. Wählen Sie **Geplant – ohne Vorlage** aus.

    ![Screenshot: Dialogfeld „Neu“ mit hervorgehobener Option „Geplant – ohne Vorlage“](./media/flow/flow-scheduled-from-blank.png)

1. Geben Sie unter **Geplanten Flow erstellen** die erforderlichen Informationen ein.
    
    ![Screenshot: Seite „Geplanten Flow erstellen“ mit hervorgehobener Option „Flowname“](./media/flow/flow-build-scheduled-flow.png)

1. Wählen Sie **Erstellen** >  **+ Neuer Schritt** aus.
1. Geben Sie in das Suchfeld *Kusto* ein, und wählen Sie **Azure Data Explorer** aus.

    ![Screenshot: Option „Aktion auswählen“ mit hervorgehobenem Suchfeld und hervorgehobenem Eintrag „Azure Data Explorer“](./media/flow/flow-actions.png)

## <a name="flow-actions"></a>Flow-Aktionen

Wenn Sie den Azure Data Explorer-Connector öffnen, können Sie dem Flow drei mögliche Aktionen hinzufügen. In diesem Abschnitt werden die Funktionen und Parameter für die einzelnen Aktionen beschrieben.

![Screenshot: Aktionen des Azure Data Explorer-Connectors](./media/flow/flow-adx-actions.png)

### <a name="run-control-command-and-visualize-results"></a>Steuerungsbefehl ausführen und Ergebnisse visualisieren

Verwenden Sie diese Aktion, um einen [Steuerungsbefehl](kusto/management/index.md) auszuführen.

1. Geben Sie die Cluster-URL an. Beispiel: `https://clusterName.eastus.kusto.windows.net`.
1. Geben Sie den Namen der Datenbank ein.
1. Geben Sie den Steuerungsbefehl an:
   * Wählen Sie dynamischen Inhalt aus den im Flow verwendeten Apps und Connectors aus.
   * Fügen Sie einen Ausdruck hinzu, um auf Werte zuzugreifen, sie zu konvertieren und zu vergleichen.
1. Geben Sie zum Senden der Ergebnisse dieser Aktion per E-Mail in Form einer Tabelle oder eines Diagramms den Diagrammtyp an. Infrage kommt:
   * Eine HTML-Tabelle
   * Ein Kreisdiagramm
   * Ein Zeitdiagramm
   * Ein Balkendiagramm.

![Screenshot: Ausführen des Steuerungsbefehls und Visualisieren von Ergebnissen im Bereich „Serie“](./media/flow/flow-runcontrolcommand.png)

> [!IMPORTANT]
> Geben Sie in das Feld **Clustername** die Cluster-URL ein.

### <a name="run-query-and-list-results"></a>Abfrage ausführen und Ergebnisse auflisten

> [!Note]
> Wenn Ihre Abfrage mit einem Punkt beginnt (d. h. es handelt sich um einen [Steuerungsbefehl](kusto/management/index.md)), verwenden Sie [Steuerungsbefehl ausführen und Ergebnisse visualisieren](#run-control-command-and-visualize-results).

Diese Aktion sendet eine Abfrage an den Kusto-Cluster. Die Aktionen, die später hinzugefügt werden, durchlaufen jede Zeile der Ergebnisse der Abfrage.

Im folgenden Beispiel wird jede Minute eine Abfrage ausgelöst und basierend auf den Abfrageergebnissen eine E-Mail gesendet. Die Abfrage prüft die Anzahl der Zeilen in der Datenbank und sendet dann nur dann eine E-Mail, wenn die Zeilenanzahl größer als 0 (null) ist. 

![Screenshot: Abfrage ausführen und Ergebnisse auflisten](./media/flow/flow-runquerylistresults-2.png)

> [!Note]
> Wenn die Spalte mehrere Zeilen enthält, wird der Connector für jede Zeile in der Spalte ausgeführt.

### <a name="run-query-and-visualize-results"></a>Abfrage ausführen und Ergebnisse visualisieren
        
> [!Note]
> Wenn Ihre Abfrage mit einem Punkt beginnt (d. h. es handelt sich um einen [Steuerungsbefehl](kusto/management/index.md)), verwenden Sie [Steuerungsbefehl ausführen und Ergebnisse visualisieren](#run-control-command-and-visualize-results).
        
Verwenden Sie diese Aktion, um ein Kusto-Abfrageergebnis als Tabelle oder Diagramm zu visualisieren. Verwenden Sie diesen Flow beispielsweise, um tägliche Berichte per E-Mail zu erhalten. 
    
In diesem Beispiel werden die Ergebnisse der Abfrage als HTML-Tabelle zurückgegeben.
            
![Screenshot: Abfrage ausführen und Ergebnisse visualisieren](./media/flow/flow-runquery.png)

> [!IMPORTANT]
> Geben Sie in das Feld **Clustername** die Cluster-URL ein.

## <a name="email-kusto-query-results"></a>Kusto-Abfrageergebnisse per E-Mail senden

Sie können einen Schritt in einen beliebigen Flow einbeziehen, um Berichte per E-Mail an eine beliebige E-Mail-Adresse zu senden. 

1. Wählen Sie **+ Neuer Schritt** aus, um einen neuen Schritt zu Ihrem Flow hinzuzufügen.
1. Geben Sie in das Suchfeld *Office 365* ein, und wählen Sie **Office 365 Outlook** aus.
1. Wählen Sie **E-Mail senden (V2)** aus.
1. Geben Sie die E-Mail-Adresse ein, an die der E-Mail-Bericht gesendet werden soll.
1. Geben Sie den Betreff der E-Mail ein.
1. Wählen Sie **Codeansicht** aus.
1. Platzieren Sie den Cursor in das Feld **Text** , und wählen Sie **Dynamischen Inhalt hinzufügen** aus.
1. Wählen Sie **BodyHtml** aus.
    ![Screenshot: Dialogfeld „E-Mail senden“ mit hervorgehobenen Optionen „Text“ und „BodyHtml“](./media/flow/flow-send-email.png)
1. Wählen Sie **Erweiterte Optionen anzeigen** aus.
1. Wählen Sie unter **Anlagenname -1** die Option **Anlagenname** aus.
1. Wählen Sie unter **Anlageninhalt** die Option **Anlageninhalt** aus.
1. Fügen Sie bei Bedarf weitere Anlagen hinzu. 
1. Legen Sie, falls erforderlich, die Wichtigkeitsstufe fest.
1. Wählen Sie **Speichern** aus.

![Screenshot: Dialogfeld „E-Mail senden“ mit hervorgehobenen Optionen „Anlagenname“, „Anlageninhalt“ und „Speichern“](./media/flow/flow-add-attachments.png)

## <a name="check-if-your-flow-succeeded"></a>Prüfen Sie, ob Ihr Flow erfolgreich war.

Um zu überprüfen, ob Ihr Flow erfolgreich war, betrachten Sie sich den Verlauf des Flows:
1. Navigieren Sie zur [Microsoft Power Automate-Startseite](https://flow.microsoft.com/).
1. Wählen Sie im Hauptmenü [Meine Flows](https://flow.microsoft.com/manage/flows) aus.
   
   ![Screenshot: Hauptmenü von Microsoft Power Automate mit hervorgehobener Option „Meine Flows“](./media/flow/flow-myflows.png)

1. Wählen Sie in der Zeile des Flows, den Sie untersuchen möchten, das Symbol für weitere Befehle und dann **Ausführungsverlauf** aus.

    ![Screenshot: Registerkarte „Meine Flows“ mit hervorgehobener Option „Ausführungsverlauf“](./media/flow//flow-runhistory.png)

    Alle Flowausführungen werden mit Informationen zu Startzeit, Dauer und Status aufgelistet.
    ![Screenshot: Seite mit Ergebnissen für den Ausführungsverlauf](./media/flow/flow-runhistoryresults.png)

    Wählen Sie zum Abrufen der vollständigen Details zum Flow auf der Seite **[Meine Flows](https://flow.microsoft.com/manage/flows)** den Flow aus, den Sie untersuchen möchten.

    ![Screenshot: Seite mit den vollständigen Ergebnissen für den Ausführungsverlauf](./media/flow/flow-fulldetails.png) 

Wählen Sie die Startzeit der Ausführung aus, um zu prüfen, warum bei der Ausführung ein Fehler aufgetreten ist. Der Flow wird angezeigt, und der fehlerhafte Schritt des Flows wird durch ein rotes Ausrufezeichen gekennzeichnet. Erweitern Sie den fehlerhaften Schritt, um seine Details anzuzeigen. Der Bereich **Details** auf der rechten Seite enthält Informationen zu dem Fehler, damit Sie ihn beheben können.

![Screenshot: Seite mit dem Flow-Fehler](./media/flow/flow-error.png)

## <a name="timeout-exceptions"></a>Timeoutausnahmen

Ihr Flow kann einen Fehler aufweisen und eine „RequestTimeout“-Ausnahme zurückgeben, wenn er länger als sieben Minuten ausgeführt wird.
    
![Screenshot: Timeoutausnahmefehler bei der Flow-Anforderung](./media/flow/flow-requesttimeout.png)

Wenn Sie ein Timeoutproblem beheben möchten, gestalten Sie Ihre Abfrage effizienter, damit sie schneller ausgeführt wird, oder unterteilen Sie sie in Blöcke. Jeder Block kann in einem anderen Teil der Abfrage ausgeführt werden. Weitere Informationen finden Sie unter [Bewährte Methoden für Abfragen](kusto/query/best-practices.md).

Dieselbe Abfrage kann erfolgreich in Azure Data Explorer ausgeführt werden, wo die Zeit nicht begrenzt ist und geändert werden kann.

## <a name="limitations"></a>Einschränkungen

* An den Client zurückgegebene Ergebnisse sind auf 500.000 Datensätze begrenzt. Der gesamte Arbeitsspeicher für diese Datensätze darf 64 MB und eine Ausführungszeit von sieben Minuten nicht überschreiten.
* Der Connector unterstützt die Operatoren [fork](kusto/query/forkoperator.md) und [facet](kusto/query/facetoperator.md) nicht.
* Der Flow funktioniert am besten mit Microsoft Edge und Google Chrome.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über den [Azure Kusto-Logik-App-Connector](kusto/tools/logicapps.md), der eine weitere Möglichkeit darstellt, Kusto-Abfragen und -Befehle als Teil einer geplanten oder ausgelösten Aufgabe automatisch auszuführen.