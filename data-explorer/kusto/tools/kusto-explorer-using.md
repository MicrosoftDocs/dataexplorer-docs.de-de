---
title: Verwenden von Kusto.Explorer
description: Erfahren Sie, wie Sie Kusto. Explorer verwenden.
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 17623f739c3bc3a8573d208434753b879931ac02
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342772"
---
# <a name="using-kustoexplorer"></a>Verwenden von Kusto.Explorer

Kusto. Explorer ist eine Desktop Anwendung, mit der Sie Ihre Daten mithilfe der Kusto-Abfragesprache in einer benutzerfreundlichen Benutzeroberfläche untersuchen können. In diesem Artikel erfahren Sie, wie Sie Such-und Abfrage Modi verwenden, Ihre Abfragen freigeben und Cluster, Datenbanken und Tabellen verwalten.

## <a name="search-mode"></a>Such Modus + +

Im Such-und +-Modus können Sie mithilfe der Such Syntax in einer oder mehreren Tabellen nach einem Begriff suchen.

1. Wählen Sie auf der Registerkarte Startseite in der Dropdown Liste Abfrage die Option **Suchen + +** aus.
1. Wählen Sie **mehrere Tabellen**aus.
1. Definieren **Sie unter Tabellen auswählen**, welche Tabellen durchsucht werden sollen.
1. Geben Sie im Bearbeitungsfeld Ihren Suchbegriff ein, und wählen Sie **Los**aus.
1. Eine Heat Map des Tabellen-/Uhrzeit-slotrasters zeigt an, welche Begriffe angezeigt werden und wo Sie angezeigt werden.

    :::image type="content" source="images/kusto-explorer-using/search-plus-plus.png" alt-text="Suchen + + Kusto-Explorer":::

1. Wählen Sie im Raster eine Zelle aus, und wählen Sie **Details anzeigen** aus, um die relevanten Einträge im Ergebnisbereich anzuzeigen.

    :::image type="content" source="images/kusto-explorer-using/search-plus-plus-results.png" alt-text="Suchen + + Kusto-Explorer":::

## <a name="query-mode"></a>Abfrage Modus

Kusto. Explorer enthält einen leistungsfähigen Skript Modus, mit dem Sie Ad-hoc-Abfragen schreiben, bearbeiten und ausführen können. Der Skript Modus verfügt über Syntax Hervorhebung und IntelliSense, sodass Sie Ihre Kenntnisse über die Kusto-Abfragesprache schnell übernehmen können.

In diesem Abschnitt wird beschrieben, wie Sie grundlegende Abfragen in Kusto. Explorer ausführen und Ihren Abfragen Parameter hinzufügen.

## <a name="basic-queries"></a>Grundlegende Abfragen

Wenn Sie über Tabellen Protokolle verfügen, können Sie diese untersuchen:

<!-- csl: https://help.kusto.windows.net:443/Samples -->

```kusto
StormEvents | count 
```

Wenn sich der Cursor in dieser Zeile befindet, ist er grau gefärbt. Drücken Sie **F5** , um die Abfrage auszuführen. 

Im folgenden finden Sie einige Beispiele für Abfragen:

<!-- csl: https://help.kusto.windows.net:443/Samples -->

```kusto
// Take 10 lines from the table. Useful to get familiar with the data
StormEvents | limit 10 
```

<!-- csl: https://help.kusto.windows.net:443/Samples -->

```kusto
// Filter by EventType == 'Flood' and State == 'California' (=~ means case insensitive) 
// and take sample of 10 lines
StormEvents 
| where EventType == 'Flood' and State =~ 'California'
| limit 10
```

:::image type="content" source="images/kusto-explorer-using/basic-query.png" alt-text="Suchen + + Kusto-Explorer":::

Erfahren Sie mehr über die [Kusto-Abfragesprache](../query/index.md).

> [!NOTE]
> Leere Zeilen im Abfrageausdruck können beeinflussen, welcher Teil der Abfrage ausgeführt wird.
>
> Ist kein Text ausgewählt, wird davon ausgegangen, dass die Abfrage oder der Befehl durch leere Zeilen getrennt ist.
> Ist Text ausgewählt, wird der ausgewählte Text ausgeführt.

## <a name="client-side-query-parameterization"></a>Client seitige Abfrage Parametrisierung

> [!NOTE]
> Es gibt zwei Typen von Abfrage parametrisierungstechniken in Kusto:
> * Die [sprach integrierte Abfrage Parametrisierung](../query/queryparametersstatement.md) wird als Teil der Abfrage-Engine implementiert und soll von Anwendungen verwendet werden, die den Dienstprogramm gesteuert Abfragen. Diese Methode wird in diesem Dokument nicht beschrieben.
>
> * Die nachstehend beschriebene Client seitige Abfrage Parametrisierung ist eine Funktion der Anwendung "Kusto. Explorer". Dies entspricht der Verwendung von String-Replace-Vorgängen für die Abfragen, bevor Sie von dem Dienst ausgeführt werden. Die unten beschriebene Syntax ist nicht Teil der Abfragesprache und kann nicht verwendet werden, wenn Abfragen an den Dienst gesendet werden, die nicht "Kusto. Explorer" sind.

Wenn Sie denselben Wert in mehreren Abfragen oder auf mehreren Registerkarten verwenden, ist es äußerst unpraktisch, diesen Wert an jedem Ort zu ändern, an dem er verwendet wird. Daher unterstützt Kusto. Explorer Abfrage Parameter. Abfrage Parameter werden von Registerkarten gemeinsam genutzt, sodass Sie problemlos wieder verwendet werden können. Parameter werden durch eckige {} Klammern bezeichnet. Beispiel: `{parameter1}`

Der Skript-Editor hebt die Abfrage Parameter hervor:

:::image type="content" source="images/kusto-explorer-using/parametrized-query-1.png" alt-text="Suchen + + Kusto-Explorer":::

Sie können vorhandene Abfrage Parameter problemlos definieren und bearbeiten:


:::image type="content" source="images/kusto-explorer-using/parametrized-query-2.png" alt-text="Suchen + + Kusto-Explorer":::


:::image type="content" source="images/kusto-explorer-using/parametrized-query-3.png" alt-text="Suchen + + Kusto-Explorer":::

Der Skript-Editor verfügt auch über IntelliSense für bereits definierte Abfrage Parameter:

:::image type="content" source="images/kusto-explorer-using/parametrized-query-4.png" alt-text="Suchen + + Kusto-Explorer":::

Sie können mehrere Sätze von Parametern (aufgelistet im Kombinations Feld **Parametersatz** ) enthalten.
Wählen Sie **neue hinzufügen** oder **aktuelle löschen** aus, um die Liste der Parametersätze zu bearbeiten.

:::image type="content" source="images/kusto-explorer-using/parametrized-query-5.png" alt-text="Suchen + + Kusto-Explorer":::

## <a name="share-queries-and-results"></a>Freigeben von Abfragen und Ergebnissen

In Kusto. Explorer können Sie Abfragen und Ergebnisse per e-Mail freigeben. Sie können auch Deep-Links erstellen, die im Browser geöffnet werden und eine Abfrage ausführen.

### <a name="share-queries-and-results-by-email"></a>Abfragen und Ergebnisse per e-Mail freigeben

"Kusto. Explorer" stellt eine bequeme Möglichkeit dar, Abfragen und Abfrageergebnisse per e-Mail freizugeben.

1. [Führen Sie die Abfrage](#basic-queries) in Kusto. Explorer aus.
1. Wählen Sie auf der Registerkarte Startseite im Abschnitt Freigabe die Option in **Zwischenablage exportieren** aus (oder drücken Sie STRG + UMSCHALT + C).

    :::image type="content" source="images/kusto-explorer-using/menu-export.png" alt-text="Suchen + + Kusto-Explorer" fügt Folgendes in die Zwischenablage ein:
     * Ihre Abfrage
     * Die Abfrageergebnisse (Tabelle oder Diagramm)
     * Verbindungsdetails für den Kusto-Cluster und die Datenbank
     * Ein Link, mit dem die Abfrage automatisch erneut ausgeführt wird.

1. Fügen Sie den Inhalt der Zwischenablage in eine neue e-Mail-Nachricht ein.

    :::image type="content" source="images/kusto-explorer-using/share-results-2.png" alt-text="Suchen + + Kusto-Explorer":::

### <a name="deep-linking-queries"></a>Deep-Linking-Abfragen

Sie können einen URI erstellen, der, wenn er in einem Browser geöffnet wird, "Kusto. Explorer" lokal öffnet und eine bestimmte Abfrage für eine angegebene Kusto-Datenbank ausführt.

> [!NOTE] 
> Aus Sicherheitsgründen ist Deep-Linking für Steuerungsbefehle deaktiviert.

#### <a name="creating-a-deep-link"></a>Erstellen eines Deep-Links

Die einfachste Möglichkeit, einen Deep-Link zu erstellen, besteht darin, die Abfrage in Kusto. Explorer zu erstellen und dann mit `Export to Clipboard` die Abfrage (einschließlich Deep-Link und Ergebnisse) in die Zwischenablage zu kopieren. Sie können Sie dann per e-Mail freigeben.
        
Beim Kopieren in eine e-Mail wird der Deep-Link in der kleinen Schriftart angezeigt. Beispiel:

https://help.kusto.windows.net:443/Samples [[Zum Ausführen der Abfrage klicken](https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d)] 

Der erste Link öffnet Kusto. Explorer und legt den Cluster und den Daten Bank Kontext entsprechend fest.
Der zweite Link ( `Click to run query` ) ist der Deep-Link. Wenn Sie den Link zu einer e-Mail-Nachricht verschieben und STRG + K drücken, wird die tatsächliche URL angezeigt:

https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d

#### <a name="deep-links-and-parametrized-queries"></a>Deep-Links und Paramete Abfragen

Paramete Abfragen können mit Deep-Linking verwendet werden.

1. Erstellen Sie eine Abfrage, die als Parameterabfrage geformt werden soll (z. b. `KustoLogs | where Timestamp > ago({Period}) | count` ). 
1. Geben Sie einen Parameter für jeden Abfrage Parameter im URI an, z. b.: 
    
    `https://<your_cluster>.kusto.windows.net/MyDatabase?
web=0&query=KustoLogs+%7c+where+Timestamp+>+ago({Period})+%7c+count&Period=1h`

    Ersetzen Sie hierbei &lt;your_cluster&gt; durch den Namen Ihres Azure Data Explorer-Clusters.

#### <a name="limitations"></a>Einschränkungen

Die Abfragen sind aufgrund von Browser Einschränkungen, HTTP-Proxys und Tools, die Links überprüfen, wie z. b. Microsoft Outlook, auf ~ 2000 Zeichen beschränkt. Die Einschränkung ist ungefähre Werte, da Sie von der Länge des Clusters und des Daten Banknamens abhängig ist. Weitere Informationen finden Sie unter [https://support.microsoft.com/kb/208427](https://support.microsoft.com/kb/208427). 

Um die Wahrscheinlichkeit zu verringern, dass das Zeichenlimit erreicht wird, finden Sie unter [Getting kürzerer Links](#getting-shorter-links).

Der URI hat folgendes Format: `https://<ClusterCname>.kusto.windows.net/<DatabaseName>web=0?query=<QueryToExecute>`

Beispiel: [https://help.kusto.windows.net/Samples?web=0query=StormEvents+%7c+limit+10](https://help.kusto.windows.net/Samples?web=0query=StormEvents+%7c+limit+10)
 
Dieser URI öffnet "Kusto. Explorer", stellt eine Verbindung mit dem `Help` Kusto-Cluster her und führt die angegebene Abfrage für die `Samples` Datenbank aus. Wenn eine Instanz von "Kusto. Explorer" bereits ausgeführt wird, öffnet die laufende Instanz eine neue Registerkarte und führt die Abfrage darin aus.

### <a name="getting-shorter-links"></a>Erhalten kürzerer Links

Abfragen können zu lang sein. Um die Wahrscheinlichkeit zu verringern, dass die Abfrage die maximale Länge überschreitet, verwenden Sie die `String Kusto.Data.Common.CslCommandGenerator.EncodeQueryAsBase64Url(string query)` in der Kusto-Client Bibliothek verfügbare Methode. Diese Methode erzeugt eine kompaktere Version der Abfrage. Das kürzere Format wird auch von Kusto. Explorer erkannt.

https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d

Die Abfrage wird durch Anwenden der nächsten Transformation kompakter:

```csharp
 UrlEncode(Base64Encode(GZip(original query)))
```

## <a name="kustoexplorer-command-line-arguments"></a>Kusto. Explorer-Befehlszeilenargumente

Befehlszeilenargumente werden verwendet, um das Tool für das Ausführen zusätzlicher Funktionen beim Start zu konfigurieren. Laden Sie beispielsweise ein Skript, und stellen Sie eine Verbindung mit einem Cluster her. Daher sind Befehlszeilenargumente kein Ersatz für Kusto. Explorer-Funktionen.

Befehlszeilenargumente werden als Teil der URL, die zum Öffnen der Anwendung verwendet wird, auf ähnliche Weise wie das Abfragen von [Deep-Linking-Abfragen](#creating-a-deep-link)übermittelt.

### <a name="command-line-argument-syntax"></a>Syntax der Befehlszeilenargumente

"Kusto. Explorer" unterstützt mehrere Befehlszeilenargumente in der folgenden Syntax (die Reihenfolge spielt eine Rolle):

[*Localscriptfile*] [*QueryString*]

* *Localscriptfile* ist der Name einer Skriptdatei auf dem lokalen Computer, die die Erweiterung aufweisen muss `.kql` . Wenn eine solche Datei vorhanden ist, lädt Kusto. Explorer diese Datei automatisch, wenn Sie gestartet wird.
* *QueryString* ist eine Zeichenfolge, die HTTP-Abfrage Zeichenfolgen formatiert. Diese Methode stellt zusätzliche Eigenschaften bereit, wie in der folgenden Tabelle beschrieben.

Wenn Sie z. b. "Kusto. Explorer" mit einer Skriptdatei starten möchten, `c:\temp\script.kql` die für die Kommunikation mit dem Cluster und `help` der Datenbank konfiguriert `Samples` ist, verwenden Sie den folgenden Befehl:

```kusto
Kusto.Explorer.exe c:\temp\script.kql uri=https://help.kusto.windows.net/Samples;Fed=true&name=Samples
```

|Argument  |BESCHREIBUNG                                                               |
|----------|--------------------------------------------------------------------------|
|**Auszuführende Abfrage**                                                                 |
|`query`   |Die auszuführende Abfrage (base64-codiert). Wenn leer, verwenden Sie `querysrc` .          |
|`querysrc`|Die URL einer Datei oder eines BLOBs, die die auszuführende Abfrage enthält (wenn `query` leer ist).|
|**Verbindung mit dem Kusto-Cluster**                                                  |
|`uri`     |Die Verbindungs Zeichenfolge des Kusto-Clusters, mit dem eine Verbindung hergestellt wird.                 |
|`name`    |Der Anzeige Name der Verbindung mit dem Kusto-Cluster.                  |
|**Verbindungsgruppe**                                                                 |
|`path`    |Die URL der herunter zuladenden Verbindungs Gruppendatei (URL-codiert).             |
|`group`   |Der Name der Verbindungsgruppe.                                         |
|`filename`|Die lokale Datei, die die Verbindungsgruppe enthält.                              |


## <a name="manage-clusters-databases-tables-or-function-authorized-principals"></a>Verwalten von Clustern, Datenbanken, Tabellen oder Funktions berechtigten Prinzipale

> [!NOTE]
> Nur [Administratoren](../management/access-control/role-based-authorization.md) können autorisierte Prinzipale in Ihrem eigenen Gültigkeitsbereich hinzufügen oder löschen.

Klicken Sie im Bereich [Verbindungen](kusto-explorer.md#connections-tab)mit der rechten Maustaste auf die Ziel Entität, und wählen Sie verwaltete **Cluster Prinzipale verwalten**aus. (Sie können diese Option auch im Menü "Verwaltung" auswählen.)

:::image type="content" source="images/kusto-explorer-using/right-click-manage-authorized-principals.png" alt-text="Suchen + + Kusto-Explorer":::

:::image type="content" source="images/kusto-explorer-using/manage-authorized-principals-window.png" alt-text="Suchen + + Kusto-Explorer":::

* Zum Hinzufügen eines neuen autorisierten Prinzipals wählen Sie **Prinzipal hinzufügen**aus, geben die Prinzipal Details an und bestätigen die Aktion.
    
    :::image type="content" source="images/kusto-explorer-using/add-authorized-principals-window.png" alt-text="Suchen + + Kusto-Explorer":::

    :::image type="content" source="images/kusto-explorer-using/confirm-add-authorized-principals.png" alt-text="Suchen + + Kusto-Explorer":::

* Um einen vorhandenen autorisierten Prinzipal zu löschen, wählen Sie **Drop Principal** aus, und bestätigen Sie die Aktion.

    :::image type="content" source="images/kusto-explorer-using/confirm-drop-authorized-principals.png" alt-text="Suchen + + Kusto-Explorer":::


## <a name="next-steps"></a>Nächste Schritte

* [Tastenkombinationen für Kusto.Explorer](kusto-explorer-shortcuts.md)
* [Optionen von Kusto.Explorer](kusto-explorer-options.md)
* [Problembehandlung für Kusto.Explorer](kusto-explorer-troubleshooting.md)

Informieren Sie sich über die Tools und Hilfsprogramme von Kusto.Explorer:
* [Codeanalyse von Kusto.Explorer](kusto-explorer-code-analyzer.md)
* [Codenavigation in Kusto.Explorer](kusto-explorer-codenav.md)
* [Code-Refactoring in Kusto.Explorer](kusto-explorer-refactor.md)
* [Kusto-Abfragesprache (KQL)](../query/index.md)