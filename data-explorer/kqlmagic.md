---
title: Verwenden einer Jupyter Notebook-Instanz zum Analysieren von Daten in Azure Data Explorer
description: In diesem Thema erfahren Sie, wie Sie mithilfe einer Jupyter Notebook-Instanz und der kqlmagic-Erweiterung Daten in Azure Data Explorer analysieren.
author: orspod
ms.author: orspodek
ms.reviewer: maraheja
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/20/2020
ms.openlocfilehash: 2d3f81b897f264d9f804f545a2155958606dfc79
ms.sourcegitcommit: 2b0156fc244757e62aaef5d4782c4abebaf09754
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99808657"
---
# <a name="use-a-jupyter-notebook-and-kqlmagic-extension-to-analyze-data-in-azure-data-explorer"></a>Verwenden einer Jupyter Notebook-Instanz und der kqlmagic-Erweiterung zum Analysieren von Daten in Azure Data Explorer

Jupyter Notebook ist eine Open Source-Webanwendung, mit der Sie Dokumente erstellen und freigeben können, die Livecode, Gleichungen, Visualisierungen und beschreibenden Text enthalten. Die Nutzung umfasst Folgendes: Bereinigung und Transformation von Daten, numerische Simulation, statistische Modellierung, Datenvisualisierung und Machine Learning.
[Jupyter Notebook](https://jupyter.org/) unterstützt Magic-Befehle, die die Funktionen des Kernels mit zusätzlichen Befehlen erweitern. kqlmagic ist ein Befehl, der die Funktionen des Python-Kernels in Jupyter Notebook erweitert, damit Sie Abfragen in der Kusto-Sprache nativ ausführen können. Sie können Python und die Kusto-Abfragesprache mühelos zum Abfragen und Visualisieren von Daten mithilfe der umfassenden Bibliothek „Plot.ly“ kombinieren, die mit `render`-Befehlen integriert ist. Zum Ausführen von Abfragen werden Datenquellen unterstützt. Zu diesen Datenquellen gehören Azure Data Explorer, ein schneller und hochgradig skalierbarer Dienst zur Untersuchung von Protokoll- und Telemetriedaten, sowie Azure Monitor-Protokolle und Application Insights. kqlmagic funktioniert auch mit Azure Data Studio, Jupyter Lab und der Jupyter-Erweiterung von Visual Studio Code.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Organisations-E-Mail-Konto, das Mitglied von Azure Active Directory (Azure AD) ist
- Installation von Jupyter Notebook auf dem lokalen Computer oder Verwendung von [Azure Data Studio](/sql/azure-data-studio/notebooks/notebooks-kqlmagic)

## <a name="install-kqlmagic-library"></a>Installieren der kqlmagic-Bibliothek

1. Installieren von kqlmagic:

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```

1. Laden von kqlmagic:

    ```python
    %reload_ext Kqlmagic
    ```
    > [!NOTE]
    > Ändern Sie die Kernel Version in Python 3.6, indem Sie auf „Kernel > Kernel ändern > Python 3.6“ klicken.
    
## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Herstellen einer Verbindung mit dem Azure Data Explorer-Hilfecluster

Verwenden Sie den folgenden Befehl, um eine Verbindung mit der Datenbank *Samples* herzustellen, die auf dem *Hilfecluster* gehostet wird. Ersetzen Sie für Benutzer, die nicht Microsoft Azure AD verwenden, den Mandantenname `Microsoft.com` durch den Namen Ihres Azure AD-Mandanten.

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

> [!Note]
> Wenn Sie Ihren eigenen ADX-Cluster verwenden, müssen Sie die Region wie folgt in die Verbindungszeichenfolge aufnehmen:   
   ```%kql azuredataexplorer://tenant="yourcompany.com";code;cluster='mycluster.westus';database='mykustodb'```

## <a name="query-and-visualize"></a>Abfragen und Visualisieren

Fragen Sie Daten mit dem [Render-Operator](kusto/query/renderoperator.md) ab, und visualisieren Sie Daten mithilfe der Bibliothek „ploy.ly“ Durch die Abfrage und Visualisierung wird eine integrierte Benutzeroberfläche bereitgestellt, die die native KQL verwendet. Kqlmagic unterstützt abgesehen von `timepivot`, `pivotchart` und `ladderchart` die meisten Diagramme. Der Render-Operator wird mit Ausnahme von `kind`, `ysplit` und `accumulate` mit allen Attributen unterstützt. 

### <a name="query-and-render-piechart"></a>Abfragen und Rendern eines Kreisdiagramms

```python
%%kql
StormEvents
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>Abfragen und Rendern eines Zeitdiagramms

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> Diese Diagramme sind interaktiv. Wählen Sie einen Zeitbereich aus, um einen bestimmten Zeitraum zu vergrößern.

### <a name="customize-the-chart-colors"></a>Anpassen der Diagrammfarben

Wenn Ihnen die Standardfarbpalette nicht gefällt, können Sie die Diagramme mit Palettenoptionen anpassen. Die verfügbaren Paletten finden Sie auf der folgenden Website: [Auswählen der Farbpalette für das kqlmagic-Abfragendiagrammergebnis](https://mybinder.org/v2/gh/Microsoft/jupyter-kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

1. Mit folgendem Befehl rufen Sie eine Liste der Paletten auf:

    ```python
    %kql --palettes -popup_window
    ```

1. Wählen Sie die Farbpalette `cool` aus, und rendern Sie die Abfrage erneut:

    ```python
    %%kql -palette_name "cool"
    StormEvents
    | summarize statecount=count() by State
    | sort by statecount
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parameterize-a-query-with-python"></a>Parametrisieren einer Abfrage mit Python

Kqlmagic ermöglicht einen einfachen Austausch zwischen der Kusto-Abfragesprache und Python. Weitere Informationen: [Parametrisieren von kqlmagic-Abfragen mit Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb)

### <a name="use-a-python-variable-in-your-kql-query"></a>Verwenden einer Python-Variablen in KQL-Abfragen

Sie können den Wert einer Python-Variablen in Ihrer Abfrage verwenden, um die Daten zu filtern:

```python
statefilter = ["TEXAS", "KANSAS"]
```

```python
%%kql
let _state = statefilter;
StormEvents 
| where State in (_state) 
| summarize statecount=count() by bin(StartTime,1d), State
| render timechart title = "Trend"
```

### <a name="convert-query-results-to-pandas-dataframe"></a>Konvertieren von Abfrageergebnissen in Pandas Dataframe

Sie können in Pandas DataFrame auf die Ergebnisse einer KQL-Abfrage zugreifen. Wie im folgenden Beispiel gezeigt können Sie mit der Variablen `_kql_raw_result_` auf die Ergebnisse der zuletzt ausgeführten Abfrage zugreifen und diese unkompliziert in Pandas DataFrame konvertieren:

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>Beispiel

In vielen Analyseszenarios sollten Sie, wiederverwendbare Notebooks erstellen, die viele Abfragen enthalten und die Ergebnisse einer Abfrage an nachfolgende Abfragen übergeben. Im folgenden Beispiel wird die Python-Variable `statefilter` verwendet, um die Daten zu filtern.

1. Führen Sie eine Abfrage aus, um die zehn Zustände mit dem Höchstwert für die `DamageProperty`-Eigenschaft aufzuführen:

    ```python
    %%kql
    StormEvents
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. Führen Sie eine Abfrage aus, um den Zustand mit dem Höchstwert zu extrahieren, und legen Sie diesen in einer Python-Variablen fest:

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. Führen Sie eine Abfrage mit der `let`-Anweisung und der Python-Variablen aus:

    ```python
    %%kql
    let _state = statefilter;
    StormEvents 
    | where State in (_state)
    | summarize statecount=count() by bin(StartTime,1d), State
    | render timechart title = "Trend"
    ```

1. Führen Sie den help-Befehl aus:

    ```python
    %kql --help "help"
    ```

> [!TIP]
> Um Informationen zu allen verfügbaren Konfigurationen zu erhalten, verwenden Sie `%config Kqlmagic`. Verwenden Sie zum Beheben und Erfassen von Kusto-Fehlern (z. B. Verbindungsprobleme und falsche Abfragen) `%config Kqlmagic.short_errors=False`.

## <a name="next-steps"></a>Nächste Schritte

Führen Sie den help-Befehl aus, um die folgenden Beispiel-Notebooks kennenzulernen, die alle unterstützten Features enthalten:
- [Erste Schritte mit kqlmagic für Azure Data Explorer](https://mybinder.org/v2/gh/Microsoft/jupyter-kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [Erste Schritte mit kqlmagic für Application Insights](https://mybinder.org/v2/gh/Microsoft/jupyter-kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [Erste Schritte mit kqlmagic für Azure Monitor-Protokolle](https://mybinder.org/v2/gh/Microsoft/jupyter-kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [Parametrisieren von kqlmagic-Abfragen mit Python](https://mybinder.org/v2/gh/Microsoft/jupyter-kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [Auswählen der Farbpalette für das kqlmagic-Abfragendiagrammergebnis](https://mybinder.org/v2/gh/Microsoft/jupyter-kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)
