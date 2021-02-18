---
title: Erfassen von JSON-Daten aus einer lokalen Datei in einer vorhandenen Tabelle in Azure Data Explorer mithilfe der 1-Klick-Erfassung
description: Hier finden Sie Informationen zum komfortablen Erfassen (Laden) von Daten in einer vorhandenen Azure Data Explorer-Tabelle mit der 1-Klick-Erfassung.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/29/2020
ms.openlocfilehash: b5b6c199c1b374caa40a07e277a06591edccdd59
ms.sourcegitcommit: abbcb27396c6d903b608e7b19edee9e7517877bb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100528406"
---
# <a name="use-one-click-ingestion-to-ingest-json-data-from-a-local-file-to-an-existing-table-in-azure-data-explorer"></a>Erfassen von JSON-Daten aus einer lokalen Datei in einer vorhandenen Tabelle in Azure Data Explorer mithilfe der 1-Klick-Erfassung


> [!div class="op_single_selector"]
> * [Erfassen von CSV-Daten aus einem Container in einer neuen Tabelle](one-click-ingestion-new-table.md)
> * [Erfassen von JSON-Daten aus einer lokaler Datei in einer vorhandene Tabelle](one-click-ingestion-existing-table.md)

Mit der [1-Klick-Erfassung](ingest-data-one-click.md) können Sie Daten im JSON- und CSV-Format sowie in anderen Formaten schnell in einer Tabelle erfassen und auf einfache Weise Zuordnungsstrukturen erstellen. Die Daten können entweder aus dem Speicher, aus einer lokalen Datei oder aus einem Container als einmaliger oder kontinuierlicher Erfassungsprozess erfasst werden.  

In diesem Dokument erfahren Sie, wie Sie mithilfe des intuitiven 1-Klick-Assistenten in einem bestimmten Anwendungsfall **JSON**-Daten aus einer **lokalen Datei** in einer **vorhandenen Tabelle** erfassen. Verwenden Sie den gleichen Prozess mit geringfügigen Anpassungen, um eine Vielzahl verschiedener Anwendungsfälle abzudecken.

Eine Übersicht über die 1-Klick-Erfassung sowie eine Liste der Voraussetzungen finden Sie unter [Was ist die 1-Klick-Erfassung?](ingest-data-one-click.md)
Informationen zu verschiedenen Datentypen oder-quellen finden Sie unter [Erfassen von CSV-Daten aus einem Container in einer neuen Tabelle in Azure Data Explorer mithilfe der 1-Klick-Erfassung](one-click-ingestion-new-table.md).

## <a name="ingest-new-data"></a>Erfassen neuer Daten

Klicken Sie im linken Menü der Webbenutzeroberfläche mit der rechten Maustaste auf eine *Datenbank* oder *Tabelle*, und wählen Sie **Neue Daten erfassen** aus.

   :::image type="content" source="media/one-click-ingestion-existing-table/one-click-ingestion-in-webui.png" alt-text="Auswählen der 1-Klick Erfassung auf der Webbenutzeroberfläche":::
 
## <a name="select-an-ingestion-type"></a>Auswahl eines Erfassungstyps

1. Im Fenster **Neue Daten erfassen** wird die Registerkarte **Quelle** ausgewählt.

1. Falls die Felder **Cluster** und **Datenbank** nicht automatisch ausgefüllt werden, wählen Sie über das Dropdownmenü einen vorhandenen Cluster und eine vorhandene Datenbank aus.
    
    [!INCLUDE [one-click-cluster](includes/one-click-cluster.md)]

1. Wenn das Feld **Tabelle** nicht automatisch gefüllt wird, wählen Sie eine vorhandene Tabelle aus dem Dropdownmenü aus.

1. Führen Sie unter **Quelltyp** die folgenden Schritte aus:

   1. Wählen Sie **aus Datei** aus.  
   1. Wählen Sie **Durchsuchen** aus, um nach bis zu zehn Dateien zu suchen, oder ziehen Sie die Dateien in das Feld. Die Schemadefinitionsdatei kann mithilfe des blauen Sterns ausgewählt werden.
    
      :::image type="content" source="media/one-click-ingestion-existing-table/from-file.png" alt-text="1-Klick-Erfassung aus Datei":::

## <a name="edit-the-schema"></a>Bearbeiten des Schemas

Wählen Sie **Schema bearbeiten** aus, um Ihre Tabellenspaltenkonfiguration anzuzeigen und zu bearbeiten. Auf der Registerkarte **Schema**:

   * Der **Komprimierungstyp** wird automatisch durch den Quelldateinamen ausgewählt. In diesem Fall ist der Komprimierungstyp **JSON**.
        
   * Bei Verwendung von **JSON** müssen auch geschachtelte Ebenen (**Nested levels**) von 1 bis 10 ausgewählt werden. Die Aufteilung der Tabellenspaltendaten wird durch die Ebenen bestimmt.

        :::image type="content" source="media/one-click-ingestion-existing-table/json-levels.png" alt-text="Auswählen geschachtelter Ebenen":::
    
       > [!TIP]
       > Informationen zur Verwendung von **CSV**-Dateien finden Sie unter [Erfassen von CSV-Daten aus einem Container in einer neuen Tabelle in Azure Data Explorer mithilfe der 1-Klick-Erfassung](one-click-ingestion-new-table.md#edit-the-schema).

### <a name="add-nested-json-data"></a>Hinzufügen von geschachtelten JSON-Daten 

Führen Sie die folgenden Schritte aus, um Spalten aus JSON-Ebenen hinzuzufügen, die sich von den oben ausgewählten **geschachtelten Hauptebenen** unterscheiden:

1. Klicken Sie auf den Pfeil neben einem beliebigen Spaltennamen, und wählen Sie **Neue Spalte** aus.

    :::image type="content" source="media/one-click-ingestion-existing-table/new-column.png" alt-text="Screenshot: Optionen zum Hinzufügen einer neuen Spalte – Registerkarte „Schema“ bei der 1-Klick-Erfassung – Azure Data Explorer":::

1. Geben Sie unter **Spaltenname** einen neuen Namen ein, und wählen Sie im Dropdownmenü **Spaltentyp** einen Eintrag aus.
1. Wählen Sie unter **Quelle** die Option **Neu erstellen** aus.

    :::image type="content" source="media/one-click-ingestion-existing-table/create-new-source.png" alt-text="Screenshot: Erstellen einer neuen Quelle zum Hinzufügen geschachtelter JSON-Daten bei der 1-Klick-Erfassung – Azure Data Explorer":::

1. Geben Sie die neue Quelle für diese Spalte ein, und klicken Sie auf **OK**. Diese Quelle kann aus einer beliebigen JSON-Ebene stammen.

    :::image type="content" source="media/one-click-ingestion-existing-table/name-new-source.png" alt-text="Screenshot: Popoutfenster zum Benennen der neuen Datenquelle für die hinzugefügte Spalte – 1-Klick-Erfassung in Azure Data Explorer":::

1. Klicken Sie auf **Erstellen**. Die neue Spalte wird am Ende der Tabelle hinzugefügt.

    :::image type="content" source="media/one-click-ingestion-existing-table/create-new-column.png" alt-text="Screenshot: Erstellen einer neuen Spalte während der 1-Klick-Erfassung in Azure Data Explorer":::

### <a name="edit-the-table"></a>Bearbeiten der Tabelle 

[!INCLUDE [data-explorer-one-click-column-table](includes/data-explorer-one-click-column-table.md)]

> [!NOTE]
> * Bei Tabellenformaten können Sie eine Spalte nicht zweimal zuordnen. Löschen Sie bei einer Zuordnung zu einer vorhandenen Spalte zunächst die neue Spalte.
> * Der Typ einer vorhandenen Spalte kann nicht geändert werden. Wenn Sie als Zuordnungsziel eine Spalte mit einem anderen Format verwenden, erhalten Sie ggf. leere Spalten.

[!INCLUDE [data-explorer-one-click-command-editor](includes/data-explorer-one-click-command-editor.md)]

## <a name="start-ingestion"></a>Starten der Erfassung

Wählen Sie **Erfassung starten** aus, um Tabelle und Zuordnung zu erstellen und die Datenerfassung zu starten.

:::image type="content" source="media/one-click-ingestion-existing-table/start-ingestion.png" alt-text="Starten der Erfassung":::

## <a name="complete-data-ingestion"></a>Abschließen der Datenerfassung

Im Fenster **Datenerfassung abgeschlossen** werden alle drei Schritte mit grünen Häkchen markiert, wenn die Datenerfassung erfolgreich abgeschlossen wurde.

:::image type="content" source="media/one-click-ingestion-existing-table/one-click-data-ingestion-complete.png" alt-text="1-Klick-Erfassung abgeschlossen":::

> [!IMPORTANT]
> Informationen zum Einrichten der kontinuierlichen Erfassung aus einem Container finden Sie unter [Erfassen von CSV-Daten aus einem Container in einer neuen Tabelle in Azure Data Explorer mithilfe der 1-Klick-Erfassung](one-click-ingestion-new-table.md#create-continuous-ingestion-for-container).

[!INCLUDE [data-explorer-one-click-ingestion-query-data](includes/data-explorer-one-click-ingestion-query-data.md)]

## <a name="next-steps"></a>Nächste Schritte

* [Abfragen von Daten auf der Azure Data Explorer-Webbenutzeroberfläche](web-query-data.md)
* [Schreiben von Abfragen für Azure Data Explorer mithilfe der Kusto-Abfragesprache](write-queries.md)
