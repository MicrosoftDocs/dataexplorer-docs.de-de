---
title: Erfassen von CSV-Daten aus einem Container in einer neuen Tabelle in Azure Data Explorer mithilfe der 1-Klick-Erfassung
description: Hier finden Sie Informationen zum komfortablen Erfassen (Laden) von Daten in eine neue Azure Data Explorer-Tabelle mit der 1-Klick-Erfassung.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 4a390e0501d12b6d70d924e9a5c5576e77cec435
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85265311"
---
# <a name="use-one-click-ingestion-to-ingest-csv-data-from-a-container-to-a-new-table-in-azure-data-explorer"></a>Erfassen von CSV-Daten aus einem Container in einer neuen Tabelle in Azure Data Explorer mithilfe der 1-Klick-Erfassung

Mit der [1-Klick-Erfassung](ingest-data-one-click.md) können Sie Daten im JSON- und CSV-Format sowie in anderen Formaten schnell in einer Tabelle erfassen und auf einfache Weise Zuordnungsstrukturen erstellen. Die Daten können entweder aus dem Speicher, aus einer lokalen Datei oder aus einem Container als einmaliger oder kontinuierlicher Erfassungsprozess erfasst werden.  

In diesem Dokument erfahren Sie, wie Sie mithilfe des intuitiven 1-Klick-Assistenten in einem bestimmten Anwendungsfall **CSV**-Daten aus einem **Container** in einer **neuen Tabelle** erfassen. Sie können den gleichen Prozess mit geringfügigen Anpassungen verwenden, um eine Vielzahl verschiedener Anwendungsfälle abzudecken.

Eine Übersicht über die 1-Klick-Erfassung sowie eine Liste der Voraussetzungen finden Sie unter [Was ist die 1-Klick-Erfassung?](ingest-data-one-click.md)
Informationen zum Erfassen von Daten in einer vorhandenen Tabelle in Azure Data Explorer finden Sie unter [Erfassen von Daten in einer vorhandenen Tabelle in Azure Data Explorer mithilfe der 1-Klick-Erfassung](one-click-ingestion-existing-table.md).

## <a name="ingest-new-data"></a>Erfassen neuer Daten

1. Klicken Sie im linken Menü der Webbenutzeroberfläche mit der rechten Maustaste auf eine *Datenbank*, und wählen Sie **Neue Daten erfassen (Vorschau)** aus.

    :::image type="content" source="media/one-click-ingestion-new-table/one-click-ingestion-in-web-ui.png" alt-text="Erfassen neuer Daten":::
 
1. Im Fenster **Neue Daten erfassen (Vorschau)** wird die Registerkarte **Quelle** ausgewählt. 

1. Wählen Sie **Neue Tabelle erstellen** aus, und geben Sie einen Namen für die neue Tabelle ein. Sie können alphanumerische Zeichen, Bindestriche und Unterstriche verwenden. Sonderzeichen werden nicht unterstützt.

    > [!NOTE]
    > Tabellennamen müssen zwischen einem und 1024 Zeichen lang sein.

:::image type="content" source="media/one-click-ingestion-new-table/create-new-table.png" alt-text="Erstellen einer neuen Tabelle: 1-Klick-Erfassung":::

## <a name="select-an-ingestion-type"></a>Auswahl eines Erfassungstyps

* Führen Sie unter **Erfassungstyp** die folgenden Schritte aus:
   
  1. Wählen Sie **from container** (aus Container) aus. 
  1. Fügen Sie im Feld **Mit Speicher verknüpfen** die [SAS-URL](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) des Containers hinzu, und geben Sie optional die Stichprobengröße ein.

      :::image type="content" source="media/one-click-ingestion-new-table/from-container.png" alt-text="1-Klick-Erfassung aus Container":::

 > [!TIP] 
 > Informationen zur Erfassung **aus einer Datei** finden Sie unter [Erfassen von JSON-Daten aus einer lokalen Datei in einer vorhandenen Tabelle in Azure Data Explorer mithilfe der 1-Klick-Erfassung](one-click-ingestion-existing-table.md#select-an-ingestion-type).

* Es wird eine Stichprobe der Daten angezeigt. Sie können diese Anzeige filtern, um nur Dateien zu erfassen, die mit bestimmten Zeichen beginnen und/oder auf bestimmte Zeichen enden. Die Vorschau wird automatisch aktualisiert, sobald Sie die Filter anpassen.
  
  Sie können die Anzeige beispielsweise so filtern, dass alle Dateien aufgelistet werden, die mit dem Begriff *Daten* beginnen und auf die Erweiterung *.csv.gz* enden.

  :::image type="content" source="media/one-click-ingestion-new-table/from-container-with-filter.png" alt-text="Filter bei der 1-Klick-Erfassung":::
  
## <a name="edit-the-schema"></a>Bearbeiten des Schemas

Wählen Sie **Schema bearbeiten** aus, um Ihre Tabellenspaltenkonfiguration anzuzeigen und zu bearbeiten. Vom System wird willkürlich eines der Blobs ausgewählt, und das Schema wird auf der Grundlage dieses Blobs generiert. Anhand des Namens der Quelle identifiziert der Dienst automatisch, ob sie komprimiert ist.

### <a name="schema-tab"></a>Registerkarte „Schema“

1. Auf der Registerkarte **Schema**:

    * Wählen Sie **Datenformat** aus:

        In diesem Fall ist das Datenformat **CSV**.

    > [!TIP]
    > Wenn Sie **JSON**-Dateien verwenden möchten, lesen Sie die Informationen unter [Erfassen von JSON-Daten aus einer lokalen Datei in einer vorhandenen Tabelle in Azure Data Explorer mithilfe der 1-Klick-Erfassung](one-click-ingestion-existing-table.md#edit-the-schema).

    * Aktivieren Sie das Kontrollkästchen **Spaltennamen einschließen**, damit die Überschriftenzeile der Datei ignoriert wird.

        :::image type="content" source="media/one-click-ingestion-new-table/non-json-format.png" alt-text="Aktivieren des Kontrollkästchens zum Einschließen der Spaltennamen":::

1. Geben Sie im Feld **Mapping name** (Zuordnungsname) einen Zuordnungsnamen ein. Sie können alphanumerische Zeichen und Unterstriche verwenden. Leerzeichen, Sonderzeichen und Bindestriche werden nicht unterstützt.

    :::image type="content" source="media/one-click-ingestion-new-table/table-mapping.png" alt-text="Name der Tabellenzuordnung: 1-Klick-Erfassung":::

### <a name="table"></a>Tabelle

1. In der Tabelle: 
    * Doppelklicken Sie zur Bearbeitung auf den Namen der neuen Spalte.
    * Wählen Sie neue Spaltenüberschriften aus, und führen Sie beliebige der folgenden Aktionen aus:

[!INCLUDE [data-explorer-one-click-column-table](includes/data-explorer-one-click-column-table.md)]

> [!NOTE]
> Bei Tabellenformaten kann jede Spalte in einer Spalte in Azure Data Explorer erfasst werden.

[!INCLUDE [data-explorer-one-click-command-editor](includes/data-explorer-one-click-command-editor.md)]

## <a name="start-ingestion"></a>Starten der Erfassung

Wählen Sie **Erfassung starten** aus, um Tabelle und Zuordnung zu erstellen und die Datenerfassung zu starten.

:::image type="content" source="media/one-click-ingestion-new-table/start-ingestion.png" alt-text="Erfassung starten: 1-Klick-Erfassung":::

## <a name="data-ingestion-completed"></a>Datenerfassung abgeschlossen

Im Fenster **Datenerfassung abgeschlossen** werden alle drei Schritte mit grünen Häkchen markiert, wenn die Datenerfassung erfolgreich abgeschlossen wurde.

:::image type="content" source="media/one-click-ingestion-new-table/one-click-data-ingestion-complete.png" alt-text="1-Klick-Erfassung abgeschlossen"::: 

[!INCLUDE [data-explorer-one-click-ingestion-query-data](includes/data-explorer-one-click-ingestion-query-data.md)]

## <a name="continuous-ingestion---container-only"></a>Fortlaufende Erfassung – nur Container

Mithilfe der kontinuierlichen Erfassung können Sie eine Event Grid-Instanz erstellen, die auf neue Dateien im Quellcontainer lauscht. Alle neuen Dateien, die die Kriterien der vordefinierten Parameter (Präfix, Suffix usw.) erfüllen, werden automatisch in der Zieltabelle erfasst. 

1. Wählen Sie in der Ecke unten rechts die Schaltfläche **Create continuous ingestion** (Kontinuierliche Erfassung erstellen) aus, um das Azure-Portal zu öffnen. Daraufhin wird die Datenverbindungsseite mit dem Event Grid-Datenconnector und bereits eingegebenen Quell- und Zielparametern (Quellcontainer, Tabellen und Zuordnungen) geöffnet.
    
    :::image type="content" source="media/one-click-ingestion-new-table/continuous-button.png" alt-text="Schaltfläche für kontinuierliche Erfassung":::

1. Wählen Sie **Erstellen** aus, um eine Datenverbindung zu erstellen, die auf Änderungen, Updates oder neue Daten in diesem Container lauscht. 

    :::image type="content" source="media/one-click-ingestion-new-table/event-hub-create.png" alt-text="Herstellen einer Event Hub-Verbindung":::

## <a name="next-steps"></a>Nächste Schritte

* [Abfragen von Daten auf der Azure Data Explorer-Webbenutzeroberfläche](web-query-data.md)
* [Schreiben von Abfragen für Azure Data Explorer mithilfe der Kusto-Abfragesprache](write-queries.md)
