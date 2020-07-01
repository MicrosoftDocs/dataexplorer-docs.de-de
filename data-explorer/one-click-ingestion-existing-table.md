---
title: Erfassen von JSON-Daten aus einer lokalen Datei in einer vorhandenen Tabelle in Azure Data Explorer mithilfe der 1-Klick-Erfassung
description: Hier finden Sie Informationen zum komfortablen Erfassen (Laden) von Daten in einer vorhandenen Azure Data Explorer-Tabelle mit der 1-Klick-Erfassung.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 422c930835e4ebe68cb59b27e51934d800fe14c1
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85265183"
---
# <a name="use-one-click-ingestion-to-ingest-json-data-from-a-local-file-to-an-existing-table-in-azure-data-explorer"></a>Erfassen von JSON-Daten aus einer lokalen Datei in einer vorhandenen Tabelle in Azure Data Explorer mithilfe der 1-Klick-Erfassung

Mit der [1-Klick-Erfassung](ingest-data-one-click.md) können Sie Daten im JSON- und CSV-Format sowie in anderen Formaten schnell in einer Tabelle erfassen und auf einfache Weise Zuordnungsstrukturen erstellen. Die Daten können entweder aus dem Speicher, aus einer lokalen Datei oder aus einem Container als einmaliger oder kontinuierlicher Erfassungsprozess erfasst werden.  

In diesem Dokument erfahren Sie, wie Sie mithilfe des intuitiven 1-Klick-Assistenten in einem bestimmten Anwendungsfall **JSON**-Daten aus einer **lokalen Datei** in einer **vorhandenen Tabelle** erfassen. Sie können den gleichen Prozess mit geringfügigen Anpassungen verwenden, um eine Vielzahl verschiedener Anwendungsfälle abzudecken.

Eine Übersicht über die 1-Klick-Erfassung sowie eine Liste der Voraussetzungen finden Sie unter [Was ist die 1-Klick-Erfassung?](ingest-data-one-click.md)
Informationen zu verschiedenen Datentypen oder-quellen finden Sie unter [Erfassen von CSV-Daten aus einem Container in einer neuen Tabelle in Azure Data Explorer mithilfe der 1-Klick-Erfassung](one-click-ingestion-new-table.md).

## <a name="ingest-new-data"></a>Erfassen neuer Daten

1. Klicken Sie im linken Menü der Webbenutzeroberfläche mit der rechten Maustaste auf eine *Datenbank* oder *Tabelle*, und wählen Sie **Neue Daten erfassen (Vorschau)** aus.

    :::image type="content" source="media/one-click-ingestion-existing-table/one-click-ingestion-in-webui.png" alt-text="Auswählen der 1-Klick Erfassung auf der Webbenutzeroberfläche":::
 
## <a name="select-an-ingestion-type"></a>Auswahl eines Erfassungstyps

1. Im Fenster **Neue Daten erfassen (Vorschau)** wird die Registerkarte **Quelle** ausgewählt.

1. Wenn das Feld **Tabelle** nicht automatisch gefüllt wird, wählen Sie eine vorhandene Tabelle aus dem Dropdownmenü aus.

    > [!NOTE]
    > Wenn Sie **Neue Daten erfassen (Vorschau)** für eine Zeile vom Typ *Tabelle* ausgewählt haben, wird der Name der ausgewählten Tabelle in den **Projektdetails** angezeigt.

1. Führen Sie unter **Erfassungstyp** die folgenden Schritte aus:

   1. Wählen Sie **aus Datei** aus.  
   1. Wählen Sie **Durchsuchen** aus, um nach der Datei zu suchen, oder ziehen Sie die Datei in das Feld.
    * Es wird eine Stichprobe der Daten angezeigt. Sie können diese Anzeige filtern, um nur Dateien zu erfassen, die mit bestimmten Zeichen beginnen und/oder auf bestimmte Zeichen enden. 
    >[!NOTE] 
    >Die Vorschau wird automatisch aktualisiert, sobald Sie die Filter anpassen.
  
      :::image type="content" source="media/one-click-ingestion-existing-table/from-file.png" alt-text="1-Klick-Erfassung aus Datei":::

 > [!TIP]
 > Informationen zur Erfassung **aus einem Container** finden Sie unter [Erfassen von CSV-Daten aus einem Container in einer neuen Tabelle in Azure Data Explorer mithilfe der 1-Klick-Erfassung](one-click-ingestion-new-table.md#select-an-ingestion-type).

## <a name="edit-the-schema"></a>Bearbeiten des Schemas

Wählen Sie **Schema bearbeiten** aus, um Ihre Tabellenspaltenkonfiguration anzuzeigen und zu bearbeiten.

### <a name="map-columns"></a>Zuordnen von Spalten 

1. Das Dialogfeld **Spalten zuordnen** wird geöffnet. Hier können Sie Quellspalten oder -attribute an Ihre Azure Data Explorer-Spalten anfügen.
    * Neue Zuordnungen werden automatisch festgelegt. Sie können auch eine vorhandene Zuordnung verwenden. 
    * Geben Sie in den Feldern **Quellspalten** die Spaltennamen ein, die den **Zielspalten** zugeordnet werden sollen.
    * Um eine Spalte aus der Zuordnung zu löschen, wählen Sie das Papierkorbsymbol aus.

    :::image type="content" source="media/one-click-ingestion-existing-table/map-columns.png" alt-text="Fenster für die Spaltenzuordnung"::: 
    
1. Wählen Sie **Update** aus.
1. Auf der Registerkarte **Schema**:
    * Der **Komprimierungstyp** wird automatisch durch den Quelldateinamen ausgewählt. In diesem Fall ist der Komprimierungstyp **JSON**.
        
    * Bei der Auswahl von **JSON** müssen auch **JSON-Ebenen** (1 bis 10) ausgewählt werden. Die Aufteilung der Tabellenspaltendaten wird durch die Ebenen bestimmt.

    :::image type="content" source="media/one-click-ingestion-existing-table/json-levels.png" alt-text="Auswählen von JSON-Ebenen":::
    
    > [!TIP]
    > Informationen zur Verwendung von **CSV**-Dateien finden Sie unter [Erfassen von CSV-Daten aus einem Container in einer neuen Tabelle in Azure Data Explorer mithilfe der 1-Klick-Erfassung](one-click-ingestion-new-table.md#edit-the-schema).

### <a name="table"></a>Tabelle 

In der Tabelle: 
    * Wählen Sie neue Spaltenüberschriften aus, um eine der folgenden Aufgaben auszuführen: **Neue Spalte** hinzufügen, **Spalte löschen**, **Aufsteigend sortieren** oder **Absteigend sortieren**. Für vorhandene Spalten steht nur eine Datensortierung zur Verfügung.

[!INCLUDE [data-explorer-one-click-column-table](includes/data-explorer-one-click-column-table.md)]

[!INCLUDE [data-explorer-one-click-command-editor](includes/data-explorer-one-click-command-editor.md)]

## <a name="start-ingestion"></a>Starten der Erfassung

Wählen Sie **Erfassung starten** aus, um Tabelle und Zuordnung zu erstellen und die Datenerfassung zu starten.

:::image type="content" source="media/one-click-ingestion-existing-table/start-ingestion.png" alt-text="Starten der Erfassung":::

## <a name="data-ingestion-completed"></a>Datenerfassung abgeschlossen

Im Fenster **Datenerfassung abgeschlossen** werden alle drei Schritte mit grünen Häkchen markiert, wenn die Datenerfassung erfolgreich abgeschlossen wurde.

:::image type="content" source="media/one-click-ingestion-existing-table/one-click-data-ingestion-complete.png" alt-text="1-Klick-Erfassung abgeschlossen":::

> [!IMPORTANT]
> Informationen zum Einrichten der kontinuierlichen Erfassung aus einem Container finden Sie unter [Erfassen von CSV-Daten aus einem Container in einer neuen Tabelle in Azure Data Explorer mithilfe der 1-Klick-Erfassung](one-click-ingestion-new-table.md#continuous-ingestion---container-only).

[!INCLUDE [data-explorer-one-click-ingestion-query-data](includes/data-explorer-one-click-ingestion-query-data.md)]

## <a name="next-steps"></a>Nächste Schritte

* [Abfragen von Daten auf der Azure Data Explorer-Webbenutzeroberfläche](web-query-data.md)
* [Schreiben von Abfragen für Azure Data Explorer mithilfe der Kusto-Abfragesprache](write-queries.md)
