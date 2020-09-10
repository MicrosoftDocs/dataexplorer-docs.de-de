---
title: Parameter in Azure Data Explorer-Dashboards
description: Verwenden von Parametern als Bausteine für Dashboardfilter.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 4921a48ff879879084ec1941ab69c6e9d29b9773
ms.sourcegitcommit: a4779e31a52d058b07b472870ecd2b8b8ae16e95
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89366162"
---
# <a name="use-parameters-in-azure-data-explorer-dashboards"></a>Verwenden von Parametern in Azure Data Explorer-Dashboards

Parameter werden als Bausteine für Dashboardfilter in Azure Data Explorer-Dashboards verwendet. Sie werden im Dashboardbereich verwaltet und können zu Abfragen hinzugefügt werden, um die Daten zu filtern, die von dem zugrunde liegenden visuellen Element dargestellt werden. Eine Abfrage kann einen oder mehrere Parameter verwenden. Dieses Dokument beschreibt die Erstellung und Verwendung von Parametern sowie verknüpften Filtern in Azure Data Explorer-Dashboards. 

> [!NOTE]
> Die Parameterverwaltung ist für Dashboard-Editoren im Bearbeitungsmodus verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

[Visualisieren von Daten mit Azure Data Explorer-Dashboards](azure-data-explorer-dashboards.md)

## <a name="view-parameters-list"></a>Anzeigen von Parameterlisten

Wählen Sie am oberen Rand des Dashboards die Schaltfläche **Parameter** aus, um die Liste aller Dashboardparameter anzuzeigen.

:::image type="content" source="media/dashboard-parameters/dashboard-icons.png" alt-text="Schaltfläche „Parameter“ am oberen Rand des Dashboards":::

## <a name="create-a-parameter"></a>Erstellen eines Parameters

Um einen Parameter zu erstellen, wählen Sie oben im rechten Bereich die Schaltfläche **Neuer Parameter** aus.

:::image type="content" source="media/dashboard-parameters/new-parameter-button.png" alt-text="Schaltfläche „Neue Parameter“":::

### <a name="properties"></a>Eigenschaften

1. Konfigurieren Sie im Bereich **Parameter hinzufügen** die unten aufgeführten Eigenschaften.

:::image type="content" source="media/dashboard-parameters/properties.png" alt-text="Parametereigenschaften hinzufügen":::

|Feld  |BESCHREIBUNG |
|---------|---------|
|**Parameter display name** (Anzeigename des Parameters)    |   Der Name des Parameters, der auf dem Dashboard oder der Bearbeitungskarte angezeigt wird.      |
|**Parametertyp**    |Einer der folgenden Parameter:<ul><li>**Single selection** (Einfachauswahl): Im Filter kann als Eingabe für den Parameter nur ein Wert ausgewählt werden.</li><li>**Mehrfachauswahl:** Im Filter können als Eingaben für den Parameter mehrere Werte ausgewählt werden.</li><li>**Zeitbereich**: Ermöglicht das Erstellen zusätzlicher Parameter, um die Abfragen und Dashboards basierend auf der Zeit zu filtern. Jedes Dashboard verfügt standardmäßig über eine Zeitbereichsauswahl.</li><li>**Freier Text:** Im Filter sind keine Werte aufgefüllt. Der Benutzer kann einen Wert eingeben oder einen Wert kopieren und in das Textfeld einfügen. Der Filter speichert die zuletzt verwendeten Werte.</li></ul>    |
|**Variablenname**     |   Der Name des Parameters, der in der Abfrage verwendet werden soll.      |
|**Datentyp**    |    Der Datentyp des Parameterwerts.     |
|**Pin as dashboard filter** (Als Dashboardfilter anheften)   |   Heften Sie den parameterbasierten Filter an das Dashboard an, oder lösen Sie ihn vom Dashboard.       |
|**Quelle**     |    Die Quelle der Parameterwerte: <ul><li>**Feste Werte:** Manuell eingefügte statische Filterwerte. </li><li>**Query** (Abfrage): Dynamisch über eine KQL-Abfrage eingefügte Werte.  </li></ul>    |
|**Add a „Select all“ value** („Alle auswählen“ hinzufügen)    |   Gilt nur für Parametertypen mit Einfach- und Mehrfachauswahl. Wird verwendet, um Daten für alle Parameterwerte abzurufen. Dieser Wert sollte in die Abfrage integriert werden, um die Funktionalität bereitzustellen. Weitere Beispiele zum Erstellen solcher Abfragen finden Sie unter [Verwenden des abfragebasierten Parameters für Mehrfachauswahl](#use-the-multiple-selection-query-based-parameter).     |

## <a name="manage-parameters-in-parameter-card"></a>Verwalten von Parametern auf der Karte „Parameter“

Wählen Sie im Menü mit den drei Punkten auf der Karte „Parameter“ **Bearbeiten**, **Parameter duplizieren**, **Löschen** aus oder **Von Dashboard lösen**/**An Dashboard anheften**.

Die folgenden Indikatoren können auf der Karte „Parameter“ angezeigt werden:
* Anzeigename des Parameters 
* Variablennamen 
* Anzahl der Abfragen, in denen der Parameter verwendet wurde
* „Anheften/Lösen“-Zustand im Dashboard

:::image type="content" source="media/dashboard-parameters/modify-parameter.png" alt-text="Parameter ändern":::

## <a name="use-parameters-in-your-query"></a>Verwenden von Parametern in Ihrer Abfrage

Ein Parameter muss in der Abfrage verwendet werden, damit der Filter auf das visuelle Element diese Abfrage anwendbar wird. Nachdem Sie die Parameter definiert haben, können Sie sie auf der Seite **Abfrage** > „Filtern“ auf der oberen Leiste und im Abfrage-IntelliSense sehen.

:::image type="content" source="media/dashboard-parameters/query-intellisense.png" alt-text="Parameter auf der oberen Leiste und in IntelliSense anzeigen":::

> [!NOTE]
> Wenn der Parameter nicht in der Abfrage verwendet wird, bleibt der Filter deaktiviert. Nachdem der Parameter der Abfrage hinzugefügt wurde, wird der Filter aktiv.

## <a name="use-different-parameter-types"></a>Verwenden verschiedener Parametertypen

Es werden mehrere Dashboardparametertypen unterstützt. In den folgenden Beispielen wird beschrieben, wie Parameter in einer Abfrage für verschiedene Parametertypen verwendet werden. 

### <a name="use-the-default-time-range-parameter"></a>Verwenden des Standardparameters „Zeitbereich“

Jedes Dashboard verfügt standardmäßig über einen Parameter *Zeitbereich*. Er wird nur dann im Dashboard als Filter angezeigt, wenn er in einer Abfrage verwendet wird. Verwenden Sie die Parameterschlüsselwörter `_startTime` und `__endTime`, um den Standardparameter „Zeitbereich“ in einer Abfrage zu verwenden, wie im folgenden Beispiel gezeigt:

```kusto
EventsAll
| where Repo.name has 'Microsoft'
| where CreatedAt between (_startTime.._endTime)
| summarize TotalEvents = count() by RepoName=tostring(Repo.name)
| top 5 by TotalEvents
```

Nach dem Speichern wird der Zeitbereichsfilter im Dashboard angezeigt. Er kann jetzt zum Filtern der Daten auf der Karte verwendet werden. Sie können Ihr Dashboard filtern, indem Sie einen Dropdowneintrag auswählen: **Zeitbereich** (letzte x Minuten/Stunden/Tage) oder einen **Benutzerdefinierten Zeitbereich**.

:::image type="content" source="media/dashboard-parameters/time-range.png" alt-text="Filter, der einen benutzerdefinierten Zeitbereich verwendet":::

### <a name="use-the-single-selection-fixed-value-parameter"></a>Verwenden des Parameters mit festem Wert für Einfachauswahl

Parameter mit festem Wert basieren auf vom Benutzer angegebenen, vordefinierten Werten. Das folgende Beispiel zeigt Ihnen, wie Sie einen Parameter mit festem Wert für Einfachauswahl erstellen.

#### <a name="create-the-parameter"></a>Erstellen des Parameters

1. Wählen Sie **Parameter** aus, um den Bereich **Parameter** zu öffnen, und wählen Sie **Neuer Parameter** aus.

1. Geben Sie die Details wie folgt ein:

    * **Anzeigename des Parameters**: Company
    * **Parametertyp**: Einfachauswahl
    * **Variablenname**: `_company`
    * **Datentyp**: String
    * **Als Dashboardfilter anheften**: aktiviert
    * **Quelle**: Feste Werte
    
    Verwenden Sie in diesem Beispiel folgende Werte:
    
    | Wert      | Anzeigename des Parameters |
    |------------|------------------------|
    | google/    | Google                 |
    | microsoft/ | Microsoft              |
    | facebook/  | Facebook               |
    | aws/       | AWS                    |
    | uber/      | Uber                   |
    
    * Fügen Sie einen **Alle auswählen**-Wert hinzu: Deaktiviert
    * Standardwert: Microsoft

1. Wählen Sie **Fertig** aus, um den Parameter zu erstellen.

Die Parameter können im Seitenbereich **Parameter** angezeigt werden, werden aber zurzeit in keinen visuellen Elementen verwendet.

:::image type="content" source="media/dashboard-parameters/start-end-side-pane.png" alt-text="Startzeit- und Endzeitparameter im Seitenbereich":::

#### <a name="use-the-parameter"></a>Verwenden des Parameters

1. Führen Sie eine Beispielabfrage mit dem neuen Parameter *Company* aus, indem Sie den Variablennamen `_company` verwenden:

    ```kusto
    EventsAll
    | where CreatedAt > ago(7d)
    | where Type == "WatchEvent"
    | where Repo.name has _company
    | summarize WatchEvents=count() by RepoName = tolower(tostring(Repo.name))
    | top 5 by WatchEvents
    ```

    Der neue Parameter wird in der Parameterliste am oberen Rand des Dashboards angezeigt.

1. Wählen Sie unterschiedliche Werte aus, um die visuellen Elemente zu aktualisieren.

    :::image type="content" source="media/dashboard-parameters/top-five-repos.png" alt-text="Top 5-Repositorys-Ergebnis":::

### <a name="use-the-multiple-selection-fixed-value-parameters"></a>Verwenden des Parameters mit festem Wert für Mehrfachauswahl

Parameter mit festem Wert basieren auf vom Benutzer angegebenen, vordefinierten Werten. Das folgende Beispiel zeigt, wie Sie einen Parameter mit festem Wert für Mehrfachauswahl erstellen und verwenden.

#### <a name="create-the-parameters"></a>Erstellen der Parameter

1. Wählen Sie **Parameter** aus, um den Bereich **Parameter** zu öffnen, und wählen Sie **Neuer Parameter** aus.

1. Geben Sie die Details, wie unter [Verwenden des Parameters mit festem Wert für Einfachauswahl](#use-the-single-selection-fixed-value-parameter) beschrieben, mit den folgenden Änderungen ein:

    * **Anzeigename des Parameters**: Companies
    * **Parametertyp**: Mehrfachauswahl
    * **Variablenname**: `_companies`

1. Klicken Sie auf **Fertig**, um den Parameter zu erstellen.

Die neuen Parameter können im Seitenbereich **Parameter** angezeigt werden, werden aber zurzeit in keinen visuellen Elementen verwendet.

:::image type="content" source="media/dashboard-parameters/companies-side-pane.png" alt-text="Seitenbereich „Companies“":::

#### <a name="use-the-parameters"></a>Verwenden der Parameter 

1. Führen Sie eine Beispielabfrage mit dem neuen Parameter *Companies* aus, indem Sie die Variable `_companies` verwenden.

    ```kusto
    EventsAll
    | where CreatedAt > ago(7d)
    | extend RepoName = tostring(Repo.name)
    | where Type == "WatchEvent"
    | where RepoName has_any (_companies)
    | summarize WatchEvents=count() by RepoName
    | top 5 by WatchEvents
    ```

    Der neue Parameter wird in der Parameterliste am oberen Rand des Dashboards angezeigt.  

1. Wählen Sie einen oder mehrere unterschiedliche Werte aus, um die visuellen Elemente zu aktualisieren.

    :::image type="content" source="media/dashboard-parameters/select-companies.png" alt-text="„Companies“ auswählen":::

### <a name="use-the-single-selection-query-based-parameter"></a>Verwenden des abfragebasierten Parameters für Einfachauswahl

Abfragebasierte Parameterwerte werden beim Laden des Dashboards durch Ausführen der Parameterabfrage abgerufen. Im folgenden Beispiel wird gezeigt, wie Sie einen abfragebasierten Parameter für Einfachauswahl erstellen und verwenden.

#### <a name="create-the-parameter"></a>Erstellen des Parameters

1. Wählen Sie **Parameter** aus, um den Bereich **Parameter** zu öffnen, und wählen Sie **Neuer Parameter** aus.

2. Geben Sie die Details, wie unter [Verwenden des Parameters mit festem Wert für Einfachauswahl](#use-the-single-selection-fixed-value-parameter) beschrieben, mit den folgenden Änderungen ein:

    * **Anzeigename des Parameters**: Ereignis
    * **Variablenname**: `_event`
    * **Quelle**: Abfrage
    * **Datenquelle**: GitHub
    * Wählen Sie **Abfrage hinzufügen** aus, und geben Sie die folgende Abfrage ein. Wählen Sie **Fertig**aus.

    ```kusto
    EventsAll
    | distinct Type
    | order by Type asc
    ```

    * **Value**: type
    * **Anzeigename**: type
    * **Standardwert**: WatchEvent

1. Wählen Sie **Fertig** aus, um den Parameter zu erstellen.

#### <a name="use-a-parameter-in-the-query"></a>Verwenden einer Parameters in der Abfrage

1. Die folgende Beispielabfrage mit dem neuen Parameter „Event“ verwendet die Variable `_ event`:

    ``` kusto
    EventsAll
    | where Type has (_event)
    | summarize count(Id) by Type, bin(CreatedAt,7d)
    ```

    Der neue Parameter wird in der Parameterliste am oberen Rand des Dashboards angezeigt. 

1. Wählen Sie unterschiedliche Werte aus, um die visuellen Elemente zu aktualisieren.

### <a name="use-the-multiple-selection-query-based-parameter"></a>Verwenden des abfragebasierten Parameters für Mehrfachauswahl

Abfragebasierte Parameterwerte werden zum Zeitpunkt des Ladens des Dashboards durch Ausführen der vom Benutzer angegebenen Abfrage abgeleitet. Im folgenden Beispiel wird gezeigt, wie Sie einen abfragebasierten Parameter für Mehrfachauswahl erstellen:

#### <a name="create-a-parameter"></a>Erstellen eines Parameters

1. Wählen Sie **Parameter** aus, um den Bereich **Parameter** zu öffnen, und wählen Sie **Neuer Parameter** aus.

1. Geben Sie die Details, wie unter [Verwenden des Parameters mit festem Wert für Einfachauswahl](#use-the-single-selection-fixed-value-parameter) beschrieben, mit den folgenden Änderungen ein:

    * **Anzeigename des Parameters**: Events
    * **Parametertyp**: Mehrfachauswahl
    * **Variablenname**: `_events`

1. Klicken Sie auf **Fertig**, um den Parameter zu erstellen.

#### <a name="use-parameters-in-the-query"></a>Verwenden von Parametern in der Abfrage

1. Die folgende Beispielabfrage verwendet den neuen Parameter *Events* unter Verwendung der Variablen `_events`.

    ``` kusto
    EventsAll
    | where Type in (_event) or isempty(_events)
    | summarize count(Id) by Type, bin(CreatedAt,7d)
    ```

    > [!NOTE]
    > In diesem Beispiel wird die Option **Alle auswählen** verwendet, indem mit der Funktion `isempty()` auf leere Werte überprüft wird.

    Der neue Parameter wird in der Parameterliste am oberen Rand des Dashboards angezeigt. 

1. Wählen Sie einen oder mehrere unterschiedliche Werte aus, um die visuellen Elemente zu aktualisieren.

### <a name="use-the-free-text-parameter"></a>Verwenden des Parameters für freien Text

Parameter für freien Text enthalten keine Werte. Sie ermöglichen Ihnen die Eingabe eines eigenen Werts.

#### <a name="create-the-parameter"></a>Erstellen des Parameters

1. Wählen Sie **Parameter** aus, um den Bereich **Parameter** zu öffnen, und wählen Sie **Neuer Parameter** aus.
1. Geben Sie die Details wie folgt ein:
    * **Anzeigename des Parameters**: Company
    * **Parametertyp**: Freier Text
    * **Variablenname** : _company
    * **Datentyp**: String
    * **Als Dashboardfilter anheften**: aktiviert
    * **Standardwert**: Für dieses Feld gibt es keinen Standardwert.

#### <a name="use-parameters-in-the-query"></a>Verwenden von Parametern in der Abfrage

1. Führen Sie eine Beispielabfrage mit dem neuen Parameter *Company* aus, indem Sie den Variablennamen `_company` verwenden:

    ```kusto
    EventsAll
    | where CreatedAt > ago(7d)
    | where Type == "WatchEvent"
    | where Repo.name has _company
    | summarize WatchEvents=count() by RepoName = tolower(tostring(Repo.name))
    | top 5 by WatchEvents
    ```

Der neue Parameter wird nun in der Parameterliste am oberen Rand des Dashboards angezeigt.

## <a name="use-filter-search-for-single-and-multiple-selection-filters"></a>Verwenden der Filtersucher für Filter für Einfach- und Mehrfachauswahl

Geben Sie in den Filtern für Einfach- und Mehrfachauswahl den gewünschten Wert ein. Die Filtersuche zeigt alle zuletzt abgerufenen Werte an, die dem Suchbegriff entsprechen.

## <a name="next-steps"></a>Nächste Schritte

* [Anpassen der visuellen Dashboardelemente](dashboard-customize-visuals.md)
* [Abfragen von Daten in Azure Data Explorer](web-query-data.md) 

