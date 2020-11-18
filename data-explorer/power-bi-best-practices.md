---
title: Bewährte Methoden für die Verwendung von Power BI zum Abfragen und Visualisieren von Azure Data Explorer-Daten
description: In diesem Artikel lernen Sie die bewährten Methoden kennen, die für die Verwendung von Power BI zum Abfragen und Visualisieren von Azure Data Explorer-Daten empfohlen werden.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/26/2019
ms.openlocfilehash: 47a18e8b8a2ec34207acacfd508114955f28953f
ms.sourcegitcommit: 88f8ad67711a4f614d65d745af699d013d01af32
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2020
ms.locfileid: "94639004"
---
# <a name="best-practices-for-using-power-bi-to-query-and-visualize-azure-data-explorer-data"></a>Bewährte Methoden für die Verwendung von Power BI zum Abfragen und Visualisieren von Azure Data Explorer-Daten

Azure-Daten-Explorer ist ein schneller und hochgradig skalierbarer Dienst zur Untersuchung von Daten (Protokoll- und Telemetriedaten). [Power BI](/power-bi/) ist eine Business Analytics-Lösung, mit der Sie Ihre Daten visualisieren und die Ergebnisse unternehmensweit teilen können. Azure Data Explorer bietet drei Möglichkeiten, um eine Verbindung mit Daten in Power BI herzustellen. Verwenden Sie den [integrierten Connector](power-bi-connector.md), [importieren Sie eine Abfrage aus Azure Data Explorer in Power BI](power-bi-imported-query.md), oder verwenden Sie eine [SQL-Abfrage](power-bi-sql-query.md). In den in diesem Artikel enthaltenen Tipps wird erläutert, wie Sie Azure Data Explorer-Daten mit Power BI abfragen und visualisieren. 

## <a name="best-practices-for-using-power-bi"></a>Bewährte Methoden für die Verwendung von Power BI

Wenn Sie mit neuen Rohdaten im Terabytebereich arbeiten, befolgen Sie diese Anweisungen, damit Ihre Power BI-Dashboards und -Berichte immer gut organisiert und auf dem neuesten Stand sind:

* **Nur das Nötigste** – Laden Sie nur die Daten in Power BI, die Sie für Ihre Berichte benötigen. Verwenden Sie für umfassende interaktive Analysen die [Azure Data Explorer-Webbenutzeroberfläche](web-query-data.md), die für die Ad-hoc-Exploration mit der Kusto-Abfragesprache optimiert ist.

* **Zusammengesetztes Modell** – Verwenden Sie ein [zusammengesetztes Modell](/power-bi/desktop-composite-models), um aggregierte Daten für Dashboards der obersten Ebene mit gefilterten, operativen Rohdaten zu kombinieren. Sie können klar definieren, wann Rohdaten und wann eine aggregierte Ansicht verwendet werden sollen. 

* **Importmodus oder DirectQuery-Modus** – Verwenden Sie den **Importmodus** für Interaktionen mit kleineren Datasets. Verwenden Sie den **DirectQuery-Modus** für große, häufig aktualisierte Datasets. Erstellen Sie beispielsweise Dimensionstabellen mit dem **Importmodus**, da sie klein sind und sich nicht häufig ändern. Legen Sie das Aktualisierungsintervall entsprechend der erwarteten Datenaktualisierungsrate fest. Erstellen Sie mit dem **DirectQuery-Modus** Faktentabellen, da diese Tabellen groß sind und Rohdaten enthalten. Verwenden Sie diese Tabellen, um gefilterte Daten mithilfe von Power BI-[Drillthroughs](/power-bi/desktop-drillthrough) darzustellen.

* **Parallelität**: Der Azure Data Explorer ist eine linear skalierbare Datenplattform. Daher können Sie die Leistung beim Dashboardrendering verbessern, indem Sie die Parallelität des End-to-End-Flows wie folgt erhöhen:

  * Erhöhen Sie die Anzahl [gleichzeitiger Verbindungen in DirectQuery in Power BI](/power-bi/desktop-directquery-about#maximum-number-of-connections-option-for-directquery).

  * Verwenden Sie eine [schwache Konsistenz, um die Parallelität zu verbessern](kusto/concepts/queryconsistency.md). Dies kann sich auf die Aktualität der Daten auswirken.

* **Effektive Slicer**: Verwenden Sie [Synchronisierungsslicer](/power-bi/visuals/power-bi-visualization-slicers#sync-and-use-slicers-on-other-pages), um zu verhindern, dass Daten in Berichte geladen werden, bevor Sie bereit sind. Nachdem Sie das Dataset strukturiert, alle visuellen Elemente platziert und alle Slicer gekennzeichnet haben, können Sie den Synchronisierungsslicer auswählen, um nur die benötigten Daten zu laden.

* **Verwendung von Filtern** – Verwenden Sie so viele Power BI-Filter wie möglich, um die Azure Data Explorer-Suche auf die relevanten Datenshards zu beschränken.

* **Effiziente visuelle Elemente** – Wählen Sie die leistungsfähigsten visuellen Elemente für Ihre Daten.


## <a name="tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data"></a>Tipps zur Datenabfrage mit dem Azure Data Explorer-Connector für Power BI

Der folgende Abschnitt enthält Tipps und Tricks zur Verwendung der Kusto-Abfragesprache mit Power BI. Verwenden Sie den [Azure Data Explorer-Connector für Power BI](power-bi-connector.md), um Daten zu visualisieren.

### <a name="complex-queries-in-power-bi"></a>Komplexe Abfragen in Power BI

Komplexe Abfragen lassen sich einfacher in Kusto als in Power Query ausdrücken. Sie sollten als [Kusto-Funktionen](kusto/query/functions/index.md) implementiert und in Power BI aufgerufen werden. Diese Methode ist erforderlich, wenn Sie **DirectQuery** mit `let`-Anweisungen in der Kusto-Abfrage verwenden. Da Power BI zwei Abfragen miteinander verknüpft und `let`-Anweisungen nicht mit dem `join`-Operator verwendet werden können, treten möglicherweise Syntaxfehler auf. Daher sollten Sie jeden Teil der Verknüpfung als Kusto-Funktion speichern und zulassen, dass diese beiden Funktionen von Power BI verknüpft werden.

### <a name="how-to-simulate-a-relative-date-time-operator"></a>Simulieren eines relativen DateTime-Operators

Power BI enthält keinen *relativen* DateTime-Operator wie `ago()`.
Verwenden Sie zum Simulieren von `ago()` eine Kombination aus den Power BI-Funktionen `DateTime.FixedLocalNow()` und `#duration`.

Verwenden Sie anstelle dieser Abfrage, die den `ago()`-Operator enthält:

```kusto
    StormEvents | where StartTime > (now()-5d)
    StormEvents | where StartTime > ago(5d)
``` 

Die folgende äquivalente Abfrage:

```m
let
    Source = AzureDataExplorer.Contents("help", "Samples", "StormEvents", []),
    #"Filtered Rows" = Table.SelectRows(Source, each [StartTime] > (DateTime.FixedLocalNow()-#duration(5,0,0,0)))
in
    #"Filtered Rows"
```

### <a name="configuring-azure-data-explorer-connector-options-in-m-query"></a>Konfigurieren von Optionen für den Azure Data Explorer-Connector in M-Abfragen

Sie können die Optionen des Azure Data Explorer-Connectors über den erweiterten Editor von PBI in der Abfragesprache M konfigurieren. Mithilfe dieser Optionen können Sie die generierte Abfrage steuern, die an Ihren Azure Data Explorer-Cluster gesendet wird.

```m
let
    Source = AzureDataExplorer.Contents("help", "Samples", "StormEvents", [<options>])
in
    Source
```

Sie können eine der folgenden Optionen in der M-Abfrage verwenden:

| Option | Beispiel | BESCHREIBUNG
|---|---|---
| MaxRows | `[MaxRows=300000]` | Fügt die Set-Anweisung `truncationmaxrecords` zur Abfrage hinzu. Überschreibt die standardmäßige maximale Anzahl von Datensätzen, die eine Abfrage an den Aufrufer zurückgeben kann (Kürzung).
| MaxSize | `[MaxSize=4194304]` | Fügt die Set-Anweisung `truncationmaxsize` zur Abfrage hinzu. Überschreibt die standardmäßige maximale Datengröße, die eine Abfrage an den Aufrufer zurückgeben darf (Kürzung).
| NoTruncate | `[NoTruncate=true]` | Fügt die Set-Anweisung `notruncation` zur Abfrage hinzu. Dadurch kann die Kürzung der an den Aufrufer zurückgegebenen Abfrageergebnisse unterdrückt werden.
| AdditionalSetStatements | `[AdditionalSetStatements="set query_datascope=hotcache"]` | Fügt der Abfrage die bereitgestellten Set-Anweisungen hinzu. Diese Anweisungen werden zum Festlegen von Abfrageoptionen für die Dauer der Abfrage verwendet. Mit Abfrageoptionen wird gesteuert, wie eine Abfrage ausgeführt wird und wie Ergebnisse zurückgegeben werden.
| CaseInsensitive | `[CaseInsensitive=true]` | Diese Option bewirkt, dass der Connector Abfragen generiert, bei denen die Groß-/Kleinschreibung nicht beachtet wird. Abfragen verwenden beim Vergleichen von Werten den Operator `=~` anstelle des Operators `==`.
| ForceUseContains | `[ForceUseContains=true]` | Diese Option bewirkt, dass der Connector bei der Verwendung von Textfeldern Abfragen generiert, die `contains` anstelle des Standardwerts `has` verwenden. `has` ist zwar viel leistungsfähiger, damit werden aber keine Teilzeichenfolgen behandelt. Weitere Informationen zu den Unterschieden zwischen den beiden Operatoren finden Sie unter [Zeichenfolgenoperatoren](./kusto/query/datatypes-string-operators.md).
| Timeout | `[Timeout=#duration(0,10,0,0)]` | Dient zum Festlegen des Client- und Servertimeouts der Abfrage auf die angegebene Dauer.

> [!NOTE]
> Sie können mehrere Optionen miteinander kombinieren, um das gewünschte Verhalten zu erzielen: `[NoTruncate=true, CaseInsensitive=true]`

### <a name="reaching-kusto-query-limits"></a>Erreichen der Kusto-Abfragegrenzwerte

Kusto-Abfragen geben standardmäßig bis zu 500.000 Zeilen oder 64 MB zurück, wie unter [Abfragegrenzwerte](kusto/concepts/querylimits.md) beschrieben. Sie können diese Standardwerte außer Kraft setzen, indem Sie im Verbindungsfenster von **Azure Data Explorer (Kusto)** die Option **Erweiterte Optionen** verwenden:

![Erweiterte Optionen](media/power-bi-best-practices/advanced-options.png)

Durch diese Optionen werden mit Ihrer Abfrage [SET-Anweisungen](kusto/query/setstatement.md) ausgegeben, um die standardmäßigen Abfragegrenzwerte zu ändern:

* **Datensatzanzahl für Abfrageergebnisse einschränken** generiert `set truncationmaxrecords`.
* **Datengröße in Bytes für Abfrageergebnisse einschränken** generiert `set truncationmaxsize`.
* **Abschneiden von Resultsets deaktivieren** generiert `set notruncation`.

### <a name="case-sensitivity"></a>Groß- und Kleinschreibung

Der Connector generiert standardmäßig Abfragen, die beim Vergleichen von Zeichenfolgenwerten den Operator `==` verwenden, bei dem die Groß-/Kleinschreibung beachtet werden muss. Wird bei den Daten die Groß-/Kleinschreibung nicht beachtet, ist dies nicht das gewünschte Verhalten. Verwenden Sie die Connectoroption `CaseInsensitive`, um die generierte Abfrage zu ändern:

```m
let
    Source = AzureDataExplorer.Contents("help", "Samples", "StormEvents", [CaseInsensitive=true]),
    #"Filtered Rows" = Table.SelectRows(Source, each [State] == "aLaBama")
in
    #"Filtered Rows"
```

### <a name="using-query-parameters"></a>Verwenden von Abfrageparametern

Mit [Abfrageparametern](kusto/query/queryparametersstatement.md) können Sie die Abfrage dynamisch ändern. 

#### <a name="using-a-query-parameter-in-the-connection-details"></a>Verwenden eines Abfrageparameters in den Verbindungsdetails

Verwenden Sie einen Abfrageparameter, um Informationen in der Abfrage zu filtern und die Abfrageleistung zu optimieren.
 
Öffnen Sie im Fenster **Abfragen bearbeiten** -> **Home** > **Erweiterter Editor**.

1. Suchen Sie den folgenden Abschnitt in der Abfrage:

    ```m
    Source = AzureDataExplorer.Contents("<Cluster>", "<Database>", "<Query>", [])
    ```

   Beispiel:

    ```m
    Source = AzureDataExplorer.Contents("Help", "Samples", "StormEvents | where State == 'ALABAMA' | take 100", [])
    ```

1. Ersetzen Sie den relevanten Teil der Abfrage durch Ihren Parameter. Unterteilen Sie die Abfrage in mehrere Teile, und verketten Sie diese wieder mit dem kaufmännischen Und-Zeichen (&) und dem Parameter.

   In der Abfrage oben verwenden wir beispielsweise den Teil `State == 'ALABAMA'`, unterteilen ihn in `State == '` und `'` und fügen den `State`-Parameter dazwischen ein:

    ```kusto
    "StormEvents | where State == '" & State & "' | take 100"
    ```

1. Wenn die Abfrage Anführungszeichen enthält, codieren Sie sie ordnungsgemäß. Beispielsweise wird die folgende Abfrage:

   ```kusto
   "StormEvents | where State == "ALABAMA" | take 100"
   ```

   im **Erweiterten Editor** mit zwei Anführungszeichen wie folgt angezeigt:

   ```kusto
    "StormEvents | where State == ""ALABAMA"" | take 100"
   ```

   Sie sollte durch die folgende Abfrage mit drei Anführungszeichen ersetzt werden:

   ```kusto
   "StormEvents | where State == """ & State & """ | take 100"
   ```

#### <a name="use-a-query-parameter-in-the-query-steps"></a>Verwenden eines Abfrageparameters in den Abfrageschritten

Abfrageparameter können in beliebigen Abfrageschritten verwendet werden, vorausgesetzt, sie werden unterstützt. Filtern Sie die Ergebnisse z. B. basierend auf dem Wert eines Parameters.

![Filtern von Ergebnissen mithilfe eines Parameters](media/power-bi-best-practices/filter-using-parameter.png)

### <a name="use-valuenativequery-for-azure-data-explorer-features"></a>Verwenden von Value.NativeQuery für Azure Data Explorer-Features

Wenn Sie ein Azure Data Explorer-Feature nutzen möchten, das in Power BI nicht unterstützt wird, verwenden Sie die Methode [Value.NativeQuery()](/powerquery-m/value-nativequery) in M. Mit dieser Methode wird ein Fragment der Kusto-Abfragesprache in die generierte Abfrage eingefügt, und Sie haben mit dieser Methode außerdem mehr Kontrolle über die ausgeführte Abfrage.

Im folgenden Beispiel wird die Verwendung der `percentiles()`-Funktion in Azure Data Explorer veranschaulicht:

```m
let
    StormEvents = AzureDataExplorer.Contents(DefaultCluster, DefaultDatabase){[Name = DefaultTable]}[Data],
    Percentiles = Value.NativeQuery(StormEvents, "| summarize percentiles(DamageProperty, 50, 90, 95) by State")
in
    Percentiles
```

### <a name="dont-use-power-bi-data-refresh-scheduler-to-issue-control-commands-to-kusto"></a>Verwenden Sie keinen Power BI-Datenaktualisierungsplaner, um Steuerungsbefehle für Kusto auszugeben

Power BI enthält einen Datenaktualisierungsplaner, der regelmäßig Abfragen für eine Datenquelle ausgeben kann. Dieser Mechanismus sollte nicht verwendet werden, um Steuerungsbefehle für Kusto zu planen, da Power BI voraussetzt, dass alle Abfragen schreibgeschützt sind.

### <a name="power-bi-can-send-only-short-lt2000-characters-queries-to-kusto"></a>Von Power BI können nur kurze Abfragen (&lt;2.000 Zeichen) an Kusto gesendet werden.

Wenn die Ausführung einer Abfrage in Power BI folgenden Fehler verursacht: _„DataSource. Fehler: Fehler beim Abrufen von Inhalten von ... durch „Web.Contents“_ , ist die Abfrage möglicherweise länger als 2000 Zeichen. Power BI verwendet **PowerQuery** zum Abfragen von Kusto. Dabei wird eine HTTP GET-Anforderung ausgegeben, durch die die Abfrage als Teil des abgerufenen URIs codiert wird. Daher sind von Power BI ausgegebene Kusto-Abfragen auf die maximale Länge eines Anforderungs-URI beschränkt (2.000 Zeichen abzüglich eines kleinen Puffers). Um dieses Problem zu umgehen, können Sie eine [gespeicherte Funktion](kusto/query/schema-entities/stored-functions.md) in Kusto definieren und Power BI diese Funktion in der Abfrage verwenden lassen.

## <a name="next-steps"></a>Nächste Schritte

[Visualisieren von Daten mithilfe des Azure Data Explorer-Connectors für Power BI](power-bi-connector.md)
