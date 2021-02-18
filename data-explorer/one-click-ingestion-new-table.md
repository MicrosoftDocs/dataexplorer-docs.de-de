---
title: Erfassen von CSV-Daten aus einem Container in einer neuen Tabelle in Azure Data Explorer mithilfe der 1-Klick-Erfassung
description: Hier finden Sie Informationen zum komfortablen Erfassen (Laden) von Daten in eine neue Azure Data Explorer-Tabelle mit der 1-Klick-Erfassung.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/29/2020
ms.openlocfilehash: 3ac9788eda7a75173778ce0533f59820cfd7e7dd
ms.sourcegitcommit: abbcb27396c6d903b608e7b19edee9e7517877bb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100528237"
---
# <a name="use-one-click-ingestion-to-ingest-csv-data-from-a-container-to-a-new-table-in-azure-data-explorer"></a>Erfassen von CSV-Daten aus einem Container in einer neuen Tabelle in Azure Data Explorer mithilfe der 1-Klick-Erfassung

> [!div class="op_single_selector"]
> * [Erfassen von CSV-Daten aus einem Container in einer neuen Tabelle](one-click-ingestion-new-table.md)
> * [Erfassen von JSON-Daten aus einer lokaler Datei in einer vorhandene Tabelle](one-click-ingestion-existing-table.md)

Mit der [1-Klick-Erfassung](ingest-data-one-click.md) können Sie Daten im JSON- und CSV-Format sowie in anderen Formaten schnell in einer Tabelle erfassen und auf einfache Weise Zuordnungsstrukturen erstellen. Die Daten können entweder aus dem Speicher, aus einer lokalen Datei oder aus einem Container als einmaliger oder kontinuierlicher Erfassungsprozess erfasst werden.  

In diesem Dokument erfahren Sie, wie Sie mithilfe des intuitiven 1-Klick-Assistenten in einem bestimmten Anwendungsfall **CSV**-Daten aus einem **Container** in einer **neuen Tabelle** erfassen. Nach der Erfassung können Sie [eine Event Grid-Erfassungspipeline einrichten](#create-continuous-ingestion-for-container), die auf neue Dateien im Quellcontainer lauscht und qualifizierende Daten in der neuen Tabelle erfasst. Sie können den gleichen Prozess mit geringfügigen Anpassungen verwenden, um eine Vielzahl verschiedener Anwendungsfälle abzudecken.

Eine Übersicht über die 1-Klick-Erfassung sowie eine Liste der Voraussetzungen finden Sie unter [Was ist die 1-Klick-Erfassung?](ingest-data-one-click.md)
Informationen zum Erfassen von Daten in einer vorhandenen Tabelle in Azure Data Explorer finden Sie unter [Erfassen von Daten in einer vorhandenen Tabelle in Azure Data Explorer mithilfe der 1-Klick-Erfassung](one-click-ingestion-existing-table.md).

## <a name="ingest-new-data"></a>Erfassen neuer Daten

1. Klicken Sie im linken Menü der Webbenutzeroberfläche mit der rechten Maustaste auf eine *Datenbank*, und wählen Sie **Neue Daten erfassen** aus.

    :::image type="content" source="media/one-click-ingestion-new-table/one-click-ingestion-in-web-ui.png" alt-text="Erfassen neuer Daten":::

1. Im Fenster **Neue Daten erfassen** wird die Registerkarte **Quelle** ausgewählt. Die Felder **Cluster** und **Datenbank** werden automatisch aufgefüllt.

    [!INCLUDE [one-click-cluster](includes/one-click-cluster.md)]

1. Wählen Sie **Tabelle** > **Neu erstellen** aus, und geben Sie einen Namen für die neue Tabelle ein. Sie können alphanumerische Zeichen, Bindestriche und Unterstriche verwenden. Sonderzeichen werden nicht unterstützt.

    > [!NOTE]
    > Tabellennamen müssen zwischen einem und 1024 Zeichen lang sein.

    :::image type="content" source="media/one-click-ingestion-new-table/create-new-table.png" alt-text="Erstellen einer neuen Tabelle: 1-Klick-Erfassung":::

## <a name="select-an-ingestion-type"></a>Auswahl eines Erfassungstyps

Führen Sie unter **Quelltyp** die folgenden Schritte aus:
   
  1. Wählen Sie **Aus Blobcontainer** (Blobcontainer, ADLS Gen1-Container, ADLS Gen2-Container) aus. Sie können bis zu 1.000 Blobs aus einem einzelnen Container erfassen.
  1. Fügen Sie im Feld **Mit Speicher verknüpfen** die [SAS-URL](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) des Containers hinzu, und geben Sie optional die Stichprobengröße ein. Informationen, wie Sie aus einem Ordner innerhalb dieses Containers erfassen, finden Sie unter [Erfassen aus einem Ordner in einem Container](#ingest-from-folder-in-a-container).

      :::image type="content" source="media/one-click-ingestion-new-table/from-container.png" alt-text="1-Klick-Erfassung aus Container":::

     > [!TIP] 
     > Informationen zur Erfassung **aus einer Datei** finden Sie unter [Erfassen von JSON-Daten aus einer lokalen Datei in einer vorhandenen Tabelle in Azure Data Explorer mithilfe der 1-Klick-Erfassung](one-click-ingestion-existing-table.md#select-an-ingestion-type).

### <a name="ingest-from-folder-in-a-container"></a>Erfassen aus einem Ordner in einem Container

Wenn Sie Daten aus einem bestimmten Ordner innerhalb eines Containers erfassen möchten, [generieren Sie eine Zeichenfolge im folgenden Format](kusto/api/connection-strings/storage.md#azure-data-lake-store):

*container_path*`/`*folder_path*`;`*access_key_1*

Sie verwenden diese Zeichenfolge anstelle der SAS-URL in [Erfassungstyp auswählen](#select-an-ingestion-type).

1. Navigieren Sie zu dem Speicherkonto, und wählen Sie **Storage-Explorer > Blob-Container auswählen** aus.

    :::image type="content" source="media/one-click-ingestion-new-table/blob-containers.png" alt-text="Screenshot des Zugriffs auf Blob-Container im Azure Storage-Konto":::

1. Wechseln Sie zu dem ausgewählten Ordner, und wählen Sie **URL kopieren** aus. Fügen Sie diesen Wert in eine temporäre Datei ein, und fügen Sie am Ende dieser Zeichenfolge `;` hinzu.

    :::image type="content" source="media/one-click-ingestion-new-table/copy-url.png" alt-text="Screenshot des Kopierens der URL im Ordner im Blob-Container – Azure Storage-Konto":::

1. Wählen Sie im linken Menü unter **Einstellungen** die Option **Zugriffsschlüssel** aus.

    :::image type="content" source="media/one-click-ingestion-new-table/copy-key-1.png" alt-text="Screenshot des Kopierens der Schlüsselzeichenfolge in „Zugriffsschlüssel“ im Speicherkonto":::

1. Kopieren Sie unter **Schlüssel 1** die Zeichenfolge **Schlüssel**. Fügen Sie diesen Wert am Ende der Zeichenfolge von Schritt 2 ein. 

### <a name="storage-subscription-error"></a>Speicherabonnementfehler

Wenn die folgende Fehlermeldung beim Erfassen aus einem Speicherkonto angezeigt wird:

> Der Speicher unter Ihren ausgewählten Abonnements wurde nicht gefunden. Fügen Sie das Speicherkontoabonnement *`storage_account_name`* Ihren ausgewählten Abonnements im Portal hinzu.

1. Wählen Sie im Menü oben rechts das :::image type="icon" source="media/ingest-data-one-click/directory-subscription-icon.png" border="false":::-Symbol aus. Ein Bereich **Verzeichnis und Abonnement** wird geöffnet.

1. Fügen Sie in der Dropdownliste **Alle Abonnements** das Abonnement Ihres Speicherkontos der ausgewählten Liste hinzu. 

    :::image type="content" source="media/ingest-data-one-click/subscription-dropdown.png" alt-text="Screenshot des Bereichs „Verzeichnis + Abonnement“ mit durch einen roten Rahmen hervorgehobener Dropdownliste „Abonnement“.":::

## <a name="sample-data"></a>Beispieldaten

Es wird eine Stichprobe der Daten angezeigt. Sie können diese Anzeige filtern, um nur Dateien zu erfassen, die mit bestimmten Zeichen beginnen und/oder auf bestimmte Zeichen enden. Die Vorschau wird automatisch aktualisiert, sobald Sie die Filter anpassen.

Filtern Sie beispielsweise nach allen Dateien, die mit dem Wort *.csv* beginnen.

:::image type="content" source="media/one-click-ingestion-new-table/from-container-with-filter.png" alt-text="Filter bei der 1-Klick-Erfassung":::

Vom System wird nach dem Zufallsprinzip eine der Dateien ausgewählt, und das Schema wird auf der Grundlage dieser **Schemadefinitionsdatei** generiert. Sie können eine andere Datei auswählen.

## <a name="edit-the-schema"></a>Bearbeiten des Schemas

Wählen Sie **Schema bearbeiten** aus, um Ihre Tabellenspaltenkonfiguration anzuzeigen und zu bearbeiten.  Anhand des Namens der Quelle identifiziert der Dienst automatisch, ob sie komprimiert ist.

Auf der Registerkarte **Schema**:

   1. Wählen Sie **Datenformat** aus:

        In diesem Fall ist das Datenformat **CSV**.

        > [!TIP]
        > Wenn Sie **JSON**-Dateien verwenden möchten, lesen Sie die Informationen unter [Erfassen von JSON-Daten aus einer lokalen Datei in einer vorhandenen Tabelle in Azure Data Explorer mithilfe der 1-Klick-Erfassung](one-click-ingestion-existing-table.md#edit-the-schema).

   1. Aktivieren Sie das Kontrollkästchen **Spaltennamen einschließen**, damit die Überschriftenzeile der Datei ignoriert wird.

        :::image type="content" source="media/one-click-ingestion-new-table/non-json-format.png" alt-text="Aktivieren des Kontrollkästchens zum Einschließen der Spaltennamen":::

Geben Sie im Feld **Mapping name** (Zuordnungsname) einen Zuordnungsnamen ein. Sie können alphanumerische Zeichen und Unterstriche verwenden. Leerzeichen, Sonderzeichen und Bindestriche werden nicht unterstützt.

:::image type="content" source="media/one-click-ingestion-new-table/table-mapping.png" alt-text="Name der Tabellenzuordnung: 1-Klick-Erfassung":::

### <a name="edit-the-table"></a>Bearbeiten der Tabelle

Ändern Sie bei der Erfassung in einer neuen Tabelle verschiedene Aspekte der Tabelle bei ihrer Erstellung.

[!INCLUDE [data-explorer-one-click-column-table](includes/data-explorer-one-click-column-table.md)]

> [!NOTE]
> Bei Tabellenformaten können Sie eine Spalte nicht zweimal zuordnen. Löschen Sie bei einer Zuordnung zu einer vorhandenen Spalte zunächst die neue Spalte.

[!INCLUDE [data-explorer-one-click-command-editor](includes/data-explorer-one-click-command-editor.md)]

## <a name="start-ingestion"></a>Starten der Erfassung

Wählen Sie **Erfassung starten** aus, um Tabelle und Zuordnung zu erstellen und die Datenerfassung zu starten.

:::image type="content" source="media/one-click-ingestion-new-table/start-ingestion.png" alt-text="Erfassung starten: 1-Klick-Erfassung":::

## <a name="complete-data-ingestion"></a>Abschließen der Datenerfassung

Im Fenster **Datenerfassung abgeschlossen** werden alle drei Schritte mit grünen Häkchen markiert, wenn die Datenerfassung erfolgreich abgeschlossen wurde.

:::image type="content" source="media/one-click-ingestion-new-table/one-click-data-ingestion-complete.png" alt-text="1-Klick-Erfassung abgeschlossen"::: 

[!INCLUDE [data-explorer-one-click-ingestion-query-data](includes/data-explorer-one-click-ingestion-query-data.md)]

## <a name="create-continuous-ingestion-for-container"></a>Erstellen von fortlaufender Erfassung für Container

Mithilfe der kontinuierlichen Erfassung können Sie eine Event Grid-Instanz erstellen, die auf neue Dateien im Quellcontainer lauscht. Alle neuen Dateien, die die Kriterien der vordefinierten Parameter (Präfix, Suffix usw.) erfüllen, werden automatisch in der Zieltabelle erfasst. 

1. Wählen Sie auf der Kachel **Fortlaufende Erfassung** die Option **Event Grid** aus, um das Azure-Portal zu öffnen. Daraufhin wird die Datenverbindungsseite mit dem Event Grid-Datenconnector und bereits eingegebenen Quell- und Zielparametern (Quellcontainer, Tabellen und Zuordnungen) geöffnet.
    
    :::image type="content" source="media/one-click-ingestion-new-table/continuous-button.png" alt-text="Schaltfläche für kontinuierliche Erfassung":::

1. Wählen Sie **Erstellen** aus, um eine Datenverbindung zu erstellen, die auf Änderungen, Updates oder neue Daten in diesem Container lauscht. 

    :::image type="content" source="media/one-click-ingestion-new-table/event-hub-create.png" alt-text="Herstellen einer Event Hub-Verbindung":::

## <a name="next-steps"></a>Nächste Schritte

* [Abfragen von Daten auf der Azure Data Explorer-Webbenutzeroberfläche](web-query-data.md)
* [Schreiben von Abfragen für Azure Data Explorer mithilfe der Kusto-Abfragesprache](write-queries.md)
