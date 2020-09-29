---
title: Erstellen einer Tabelle in Azure Data Explorer
description: Hier erfahren Sie, wie Sie ganz einfach und mit nur einem Klick eine Tabelle in Azure Data Explorer erstellen.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/06/2020
ms.openlocfilehash: 3302e7cb94a2184be64664f3c3d8698b8bea7643
ms.sourcegitcommit: 92b8057a36bd7daa16226f1526b29253bceb3602
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2020
ms.locfileid: "91402740"
---
# <a name="create-a-table-in-azure-data-explorer-preview"></a>Erstellen einer Tabelle in Azure Data Explorer (Vorschauversion)

Das Erstellen einer Tabelle ist ein wichtiger Schritt bei der [Datenerfassung](ingest-data-overview.md) und [Abfrage](write-queries.md) in Azure Data Explorer. Nach der [Erstellung eines Clusters und einer Datenbank in Azure Data Explorer](create-cluster-database-portal.md) können Sie eine Tabelle erstellen. In diesem Artikel erfahren Sie, wie Sie über die Webbenutzeroberfläche von Azure Data Explorer schnell eine Tabelle und eine Schemazuordnung erstellen. 

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Erstellen Sie [einen Azure Data Explorer-Cluster und eine Datenbank](create-cluster-database-portal.md).
* Melden Sie sich bei der [Azure Data Explorer-Webbenutzeroberfläche](https://dataexplorer.azure.com/) an, und [fügen Sie eine Verbindung mit Ihrem Cluster hinzu](web-query-data.md#add-clusters).

## <a name="create-a-table"></a>Erstellen einer Tabelle

1. Klicken Sie im linken Menü der Webbenutzeroberfläche mit der rechten Maustaste auf **ExampleDB** (der Name Ihrer Datenbank), und wählen Sie **Tabelle erstellen (Vorschau)** aus.

    :::image type="content" source="./media/one-click-table/create-table.png" alt-text="Erstellen einer Tabelle über die Webbenutzeroberfläche von Azure Data Explorer":::

Das Fenster **Tabelle erstellen** wird geöffnet, und die Registerkarte **Quelle** ist ausgewählt.
1. Das Feld **Datenbank** wird automatisch mit Ihrer Datenbank aufgefüllt. Über das Dropdownmenü kann eine andere Datenbank ausgewählt werden.
1. Geben Sie unter **Tabellenname** einen Namen für Ihre Tabelle ein. 
    > [!TIP]
    >  Tabellennamen können bis zu 1.024 Zeichen umfassen und alphanumerische Zeichen, Bindestriche und Unterstriche enthalten. Sonderzeichen werden nicht unterstützt.

### <a name="select-source-type"></a>Auswählen des Quelltyps

1. Wählen Sie unter **Quelltyp** die Datenquelle aus, die Sie zum Erstellen der Tabellenzuordnung verwenden möchten. Sie können zwischen folgenden Optionen wählen: **Aus Blob**, **Aus Datei** und **Aus Container**.
   
    
    * Gehen Sie bei Verwendung eines **Containers** wie folgt vor:
        * Geben Sie die Speicher-URL des Blobs und optional die Stichprobengröße ein. 
        * Filtern Sie Ihre Dateien mithilfe der **Dateifilter**. 
        * Wählen Sie eine Datei aus, die im nächsten Schritt zum Definieren des Schemas verwendet wird.

        :::image type="content" source="media/one-click-table/storage.png" alt-text="Erstellen einer Tabelle unter Verwendung eines Blobs zum Erstellen einer Schemazuordnung":::
    
    * Gehen Sie bei Verwendung einer **lokalen Datei** wie folgt vor:
        * Wählen Sie **Durchsuchen** aus, um nach der Datei zu suchen, oder ziehen Sie die Datei in das Feld.

        :::image type="content" source="./media/one-click-table/data-from-file.png" alt-text="Erstellen einer Tabelle auf der Grundlage von Daten aus einer lokalen Datei":::

    * Gehen Sie bei Verwendung eines **Blobs** wie folgt vor:
        * Fügen Sie im Feld **Mit Speicher verknüpfen** die [SAS-URL](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) des Containers hinzu, und geben Sie optional die Stichprobengröße ein. 

1. Wählen Sie **Schema bearbeiten** aus, um zur Registerkarte **Schema** zu gelangen.

### <a name="edit-schema"></a>Bearbeiten des Schemas

Auf der Registerkarte **Schema** werden Ihr [Datenformat](ingest-data-one-click.md#file-formats) und die Komprimierung automatisch im linken Bereich angegeben. Sollten die Angaben nicht korrekt sein, wählen Sie im Dropdownmenü **Datenformat** das richtige Format aus.

   * Wenn es sich um Daten im JSON-Format handelt, müssen auch JSON-Ebenen (von 1 bis 10) ausgewählt werden. Die Aufteilung der Tabellenspaltendaten wird durch die Ebenen bestimmt.
   * Wenn es sich um Daten im CSV-Format handelt, aktivieren Sie das Kontrollkästchen **Enthält Spaltennamen**, damit die Überschriftenzeile der Datei ignoriert wird.

        :::image type="content" source="./media/one-click-table/schema-tab.png" alt-text="Registerkarte „Schema bearbeiten“ bei der Tabellenerstellung mit nur einem Klick in Azure Data Explorer":::
 
1. Geben Sie unter **Zuordnung** einen Namen für die Schemazuordnung dieser Tabelle ein. 
    > [!TIP]
    >  Tabellennamen können alphanumerische Zeichen und Unterstriche enthalten. Leerzeichen, Sonderzeichen und Bindestriche werden nicht unterstützt.
1. Klicken Sie auf **Erstellen**.

## <a name="create-table-completed-window"></a>Fenster „Tabellenerstellung abgeschlossen“

Im Fenster **Tabellenerstellung abgeschlossen** werden beide Schritte mit einem grünen Häkchen versehen, wenn die Tabellenerstellung erfolgreich abgeschlossen wurde.

* Wählen Sie **Befehl anzeigen** aus, um den Editor für den jeweiligen Schritt zu öffnen. 
    * Im Editor können Sie die aus Ihren Eingaben generierten automatischen Befehle anzeigen und kopieren.
    
    :::image type="content" source="./media/one-click-table/table-completed.png" alt-text="„Tabellenerstellung abgeschlossen“ bei der Tabellenerstellung mit nur einem Klick in Azure Data Explorer":::
 
Erkunden Sie auf den Kacheln unter dem Tabellenerstellungsstatus die **Schnellabfragen** oder **Tools** **:**

* **Schnellabfragen** enthält Links zur Webbenutzeroberfläche mit Beispielabfragen.

* Unter **Tools** finden Sie Links zum **Rückgängigmachen** der relevanten Befehle vom Typ „.drop“.

> [!NOTE]
> Wenn Sie Befehle vom Typ „.drop“ verwenden, gehen möglicherweise Daten verloren.<br>
> Mit den Befehlen vom Typ „.drop“ in diesem Workflow werden nur die Änderungen zurückgesetzt, die im Rahmen des Tabellenerstellungsprozesses vorgenommen wurden (neue Tabelle und Schemazuordnung).

## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über die Datenerfassung](ingest-data-overview.md)
* [1-Klick-Erfassung](ingest-data-one-click.md)
* [Schreiben von Abfragen für den Azure-Daten-Explorer](write-queries.md)  
