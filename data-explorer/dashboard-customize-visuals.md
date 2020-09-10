---
title: Anpassen der visuellen Elemente des Azure Data Explorer-Dashboards
description: Hier erfahren Sie, wie Sie ganz einfach die visuellen Elemente Ihres Azure Data Explorer-Dashboards anpassen.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/25/2020
ms.openlocfilehash: 22463307df0358228469fe480f5578222bed52bf
ms.sourcegitcommit: a4779e31a52d058b07b472870ecd2b8b8ae16e95
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379746"
---
# <a name="customize-azure-data-explorer-dashboard-visuals"></a>Anpassen der visuellen Elemente des Azure Data Explorer-Dashboards

Visuelle Elemente sind ein wesentlicher Bestandteil jedes Azure Data Explorer-Dashboards. In diesem Dokument werden die verschiedenen Arten von visuellen Elementen erläutert und verschiedene Optionen beschrieben, mit denen Dashboardbenutzer ihre visuellen Elemente anpassen können.

> [!NOTE]
> Die Anpassung visueller Elemente ist für Dashboard-Editoren im Bearbeitungsmodus verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

[Visualisieren von Daten mit Azure Data Explorer-Dashboards](azure-data-explorer-dashboards.md)

## <a name="types-of-visuals"></a>Arten von visuellen Elementen

Von Azure Data Explorer werden mehrere verschiedene Arten von visuellen Elementen unterstützt. In diesem Abschnitt werden die verschiedenen Arten von visuellen Elementen und die Datenspalten beschrieben, die in Ihrem Resultset enthalten sein müssen, um diese visuellen Elemente zeichnen zu können.

### <a name="table"></a>Tabelle

:::image type="content" source="media/dashboard-customize-visuals/table.png" alt-text="Tabelle (visuelles Element)":::

Ergebnisse werden standardmäßig als Tabelle angezeigt. Visuelle Elemente vom Typ „Tabelle“ eignen sich am besten für die Präsentation detaillierter oder komplizierter Daten.

### <a name="bar-chart"></a>Balkendiagramm

:::image type="content" source="media/dashboard-customize-visuals/bar-chart.png" alt-text="Balkendiagramm (visuelles Element)":::

Für visuelle Elemente vom Typ „Balkendiagramm“ muss das Abfrageergebnis mindestens zwei Spalten enthalten. Die erste Spalte wird standardmäßig als y-Achse verwendet. Diese Spalte kann Text, Datums-/Uhrzeitwerte oder numerische Daten enthalten. Die anderen Spalten werden als x-Achse verwendet und enthalten numerische Daten, die als horizontale Linien dargestellt werden. Balkendiagramme werden hauptsächlich verwendet, um numerische und nominale diskrete Werte zu vergleichen, wobei die Länge der jeweiligen Zeile den Wert darstellt.

### <a name="column-chart"></a>Säulendiagramm

:::image type="content" source="media/dashboard-customize-visuals/column-chart.png" alt-text="Säulendiagramm (visuelles Element)":::

Für visuelle Elemente vom Typ „Säulendiagramm“ muss das Abfrageergebnis mindestens zwei Spalten enthalten. Die erste Spalte wird standardmäßig als x-Achse verwendet. Diese Spalte kann Text, Datums-/Uhrzeitwerte oder numerische Daten enthalten. Die anderen Spalten werden als y-Achse verwendet und enthalten numerische Daten, die als vertikale Linien dargestellt werden. Säulendiagramme werden verwendet, um bestimmte Unterkategorieelemente in einem Hauptkategoriebereich zu vergleichen, wobei die Länge der jeweiligen Zeile den Wert darstellt.

### <a name="area-chart"></a>Flächendiagramm

:::image type="content" source="media/dashboard-customize-visuals/area-chart.png" alt-text="Flächendiagramm (visuelles Element)":::

Visuelle Elemente vom Typ „Flächendiagramm“ zeigen eine Zeitreihenbeziehung. Die erste Spalte der Abfrage muss numerisch sein und wird als x-Achse verwendet. Andere numerische Spalten werden als y-Achsen verwendet. Anders als bei Liniendiagrammen wird bei Flächendiagrammen auch das Volumen visuell dargestellt. Flächendiagramme sind ideal, um Veränderungen zwischen verschiedenen Datasets darzustellen.

### <a name="line-chart"></a>Liniendiagramm

:::image type="content" source="media/dashboard-customize-visuals/line-chart.png" alt-text="Liniendiagramm (visuelles Element)":::

Bei visuellen Elementen vom Typ „Liniendiagramm“ handelt es sich um den einfachsten Diagrammtyp. Die erste Spalte der Abfrage muss numerisch sein und wird als x-Achse verwendet. Andere numerische Spalten werden als y-Achsen verwendet. In Liniendiagrammen werden Veränderungen für kurze und lange Zeiträume nachverfolgt. Bei kleineren Veränderungen sind Liniendiagramme besser geeignet als Balkendiagramme.

### <a name="stat"></a>Stat

:::image type="content" source="media/dashboard-customize-visuals/stat.png" alt-text="Stat (visuelles Element)":::

Visuelle Elemente vom Typ „Stat“ zeigen nur ein einzelnes Element an. Sollte die Ausgabe mehrere Spalten und Zeilen umfassen, wird von „Stat“ das erste Element der ersten Spalte angezeigt. Stat-Karten sind hilfreich, um KPIs auf dem Dashboard hervorzuheben.

### <a name="pie-chart"></a>Kreisdiagramm

:::image type="content" source="media/dashboard-customize-visuals/pie-chart.png" alt-text="Kreisdiagramm (visuelles Element)":::

Für visuelle Elemente vom Typ „Kreisdiagramm“ muss das Abfrageergebnis mindestens zwei Spalten enthalten. Die erste Spalte wird standardmäßig als Farbachse verwendet. Diese Spalte kann Text, Datums-/Uhrzeitwerte oder numerische Daten enthalten. Andere Spalten werden verwendet, um die Größe der einzelnen Segmente zu bestimmen, und enthalten numerische Daten. In Kreisdiagrammen werden verschiedene Kategorien und deren Verhältnis auf der Grundlage einer Gesamtheit dargestellt.

### <a name="scatter-chart"></a>Punktdiagramm

:::image type="content" source="media/dashboard-customize-visuals/scatter-chart.png" alt-text="Punktdiagramm (visuelles Element)":::

In visuellen Elementen vom Typ „Punktdiagramm“ ist die erste Spalte die x-Achse und muss eine numerische Spalte sein. Andere numerische Spalten werden als y-Achsen verwendet. Punktdiagramme dienen zur Beobachtung von Beziehungen zwischen Variablen.

### <a name="time-chart"></a>Zeitdiagramm 

:::image type="content" source="media/dashboard-customize-visuals/time-chart.png" alt-text="Zeitdiagramm (visuelles Element)":::

Bei visuellen Elementen vom Typ „Zeitdiagramm“ handelt es sich um eine Art von Liniendiagramm. Die erste Spalte der Abfrage ist die x-Achse und muss Datums-/Uhrzeitwerte enthalten. Andere numerische Spalten werden als y-Achsen verwendet. Die Werte einer einzelnen Zeichenfolgenspalte werden verwendet, um die numerischen Spalten zu gruppieren und verschiedene Zeilen im Diagramm zu erstellen. Andere Zeichenfolgenspalten werden ignoriert. Visuelle Elemente vom Typ „Zeitdiagramm“ ähneln einem [Liniendiagramm](#line-chart). Die x-Achse ist allerdings immer zeitbasiert.

### <a name="anomaly-chart"></a>Anomaliediagramm 

:::image type="content" source="media/dashboard-customize-visuals/anomaly-chart.png" alt-text="Anomaliediagramm (visuelles Element)":::

Visuelle Elemente vom Typ „Anomaliediagramm“ ähneln einem [Zeitdiagramm](#time-chart). In einem Anomaliediagramm werden jedoch mithilfe der Funktion `series_decompose_anomalies` Anomalien hervorgehoben.

### <a name="map"></a>Karte

:::image type="content" source="media/dashboard-customize-visuals/map.png" alt-text="Karte (visuelles Element)":::

Wenn Sie visuelle Elemente vom Typ „Karte“ rendern möchten, muss die Abfrage vier Spalten umfassen:
* Zeichenfolge (erste Spalte) für die Beschriftung beim Daraufzeigen
* Längengrad (real)
* Breitengrad (real)
* Blasengröße (ganze Zahl). Diese Spalte kann konstant „1“ sein, wenn keine unterschiedlichen Größen erforderlich sind.

Karten sind hilfreich, um Daten mit Geokoordinaten zu visualisieren. Visuelle Elemente vom Typ „Karte“ verfügen auch über eine integrierte Zoomfunktion.

## <a name="customize-visuals"></a>Anpassen visueller Elemente

1. Wählen Sie im Dashboardmenü **Bearbeiten** aus, um in den Bearbeitungsmodus zu wechseln.
1. Klicken Sie auf einer Karte auf das Dropdownmenü und anschließend auf **Karte bearbeiten**, um auf das Dialogfeld zum Anpassen visueller Elemente zuzugreifen. Alternativ können Sie beim Erstellen einer neuen Karte mithilfe von **Abfrage hinzufügen** die Option **Karte bearbeiten** auswählen.

:::image type="content" source="media/dashboard-customize-visuals/edit-card.png" alt-text="Bearbeiten einer Karte, um visuelle Elemente anzupassen":::

### <a name="select-properties-to-customize-visuals"></a>Auswählen von Eigenschaften zum Anpassen visueller Elemente

Verwenden Sie die folgenden Eigenschaften, um visuelle Elemente anzupassen.

:::image type="content" source="media/dashboard-customize-visuals/visual-customization-sidebar.png" alt-text="Seitenleiste für die Anpassung visueller Elemente":::

|`Section`  |BESCHREIBUNG | Visualisierungstypen
|---------|---------|-----|
|**Allgemein**    |    Wählen Sie das **gestapelte** oder das **nicht gestapelte** Diagrammformat aus.  | Balken-, Säulen- und Flächendiagramme |
|**Daten**    |   Wählen Sie **y- und x-Spalten** für Ihr visuelles Element aus. Behalten Sie die Auswahl **Infer** (Ableiten) bei, wenn auf der Grundlage des Abfrageergebnisses automatisch eine Spalte ausgewählt werden soll.    |Balken-, Säulen-, Punkt- und Anomaliediagramme|
|**Legende**    |   Mit dieser Option können Sie die Legendenanzeige für Ihre visuelle Elemente aktivieren oder deaktivieren.   |Balken-, Säulen-, Flächen-, Linien-, Punkt-, Anomalie- und Zeitdiagramme |
|**y-Achse**     |   Ermöglicht die Anpassung von Eigenschaften der y-Achse: <ul><li>**Bezeichnung:** Text für eine benutzerdefinierte Bezeichnung. </li><li>**Höchstwert:** Dient zum Ändern des Höchstwerts der y-Achse.  </li><li>**Mindestwert:** Dient zum Ändern des Mindestwerts der y-Achse.  </li></ul>      |Balken-, Säulen-, Flächen-, Linien-, Punkt-, Anomalie- und Zeitdiagramme |
|**X-Achse**     |    Ermöglicht die Anpassung von Eigenschaften der x-Achse: <ul><li>**Bezeichnung:** Text für eine benutzerdefinierte Bezeichnung. </li>     | Balken-, Säulen-, Flächen-, Linien-, Punkt-, Anomalie- und Zeitdiagramme|

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von Parametern in Azure Data Explorer-Dashboards](dashboard-parameters.md)
* [Abfragen von Daten in Azure Data Explorer](web-query-data.md) 