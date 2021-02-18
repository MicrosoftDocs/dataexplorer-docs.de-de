---
title: Erfassen von Daten in Azure Data Explorer mithilfe der 1-Klick-Erfassung
description: Übersicht über das problemlose Erfassen (Laden) von Daten in Azure Data Explorer mit der 1-Klick-Erfassung.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/29/2020
ms.openlocfilehash: 5ebd3ee711baa24c6f73facfdab361e2de5ada20
ms.sourcegitcommit: abbcb27396c6d903b608e7b19edee9e7517877bb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100528144"
---
# <a name="what-is-one-click-ingestion"></a>Was ist die 1-Klick-Erfassung?

Die 1-Klick-Erfassung ermöglicht eine einfache, schnelle und intuitive Datenerfassung. Dank der 1-Klick-Erfassung können Sie schnell mit dem Erfassen von Daten, dem Erstellen von Datenbanktabellen und dem Zuordnen von Strukturen beginnen. Wählen Sie Daten aus unterschiedlichen Arten von Quellen in verschiedenen Datenformaten aus – entweder als einmaligen oder als kontinuierlichen Erfassungsprozess.

Die 1-Klick-Erfassung ist aufgrund der folgenden Funktionen überaus nützlich:

* Intuitiver Ablauf im Rahmen des Erfassungs-Assistenten
* Erfassung von Daten innerhalb weniger Minuten
* Erfassung von Daten aus verschiedenen Arten von Quellen: lokale Datei, Blobs und Container (bis zu 10.000 Blobs)
* Erfassung von Daten in verschiedenen [Formaten](#file-formats)
* Erfassung von Daten in neuen oder vorhandenen Tabellen
* Empfehlung und einfache Änderung von Tabellenzuordnung und -schema
* Einfache und schnelle Fortsetzung der Erfassung aus einem Container mit [Event Grid](one-click-ingestion-new-table.md#create-continuous-ingestion-for-container)

Die 1-Klick-Erfassung ist besonders nützlich, wenn Daten erstmalig erfasst werden oder Sie nicht mit dem Schema Ihrer Daten vertraut sind.

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Erstellen Sie [einen Azure Data Explorer-Cluster und eine Datenbank](create-cluster-database-portal.md).
* Melden Sie sich bei der [Azure Data Explorer-Webbenutzeroberfläche](https://dataexplorer.azure.com/) an, und [fügen Sie eine Verbindung mit Ihrem Cluster hinzu](web-query-data.md#add-clusters).

## <a name="access-the-one-click-wizard"></a>Zugreifen auf den 1-Klick-Assistenten

Der Assistent für die 1-Klick-Erfassung leitet Sie durch die 1-Klick-Erfassung.

* Auf der [Webbenutzeroberfläche von Azure Data Explorer](https://dataexplorer.azure.com/) können Sie wie folgt auf den Assistenten zugreifen:
    * Wählen Sie im linken Bereich die Option **Daten** aus. Wählen Sie auf der Seite **Datenverwaltung** einen Erfassungstyp aus, und klicken Sie auf **Erfassen**. 
      
      :::image type="content" source="media/ingest-data-one-click/data-management.png" alt-text="Erfassen von Daten über das Fenster „Datenverwaltung“ der Webbenutzeroberfläche: Azure Data Explorer" lightbox="media/ingest-data-one-click/data-management.png":::
   
     * Klicken Sie auf der Webbenutzeroberfläche von Azure Data Explorer im linken Menü mit der rechten Maustaste auf die Zeile **Datenbank** oder **Tabelle**, und wählen Sie **Neue Daten erfassen** aus.
        
        :::image type="content" source="media/ingest-data-one-click/one-click-ingestion-in-webui.png" alt-text="Auswählen der 1-Klick Erfassung auf der Webbenutzeroberfläche":::

* Wenn Sie über den Startbildschirm **Welcome to Azure Data Explorer** (Willkommen bei Azure Data Explorer) in Ihrem Cluster auf den Assistenten für die 1-Klick-Erfassung zugreifen möchten, führen Sie die beiden ersten Schritte ([Clustererstellung und Datenbankerstellung](#prerequisites)) aus, und wählen Sie dann **Neue Daten erfassen** aus.

    :::image type="content" source="media/ingest-data-one-click/welcome-ingestion.png" alt-text="Erfassen neuer Daten über den Startbildschirm von Azure Data Explorer":::


* Um über das Azure-Portal auf den Assistenten zuzugreifen, wählen Sie im linken Menü **Abfrage** aus, klicken Sie mit der rechten Maustaste auf die **Datenbank** oder **Tabelle**, und wählen Sie **Neue Daten erfassen** aus.

    :::image type="content" source="media/ingest-data-one-click/access-from-portal.png" alt-text="Zugreifen auf den Assistenten für die 1-Klick-Erfassung über das Azure-Portal":::

## <a name="one-click-ingestion-wizard"></a>Assistent für die 1-Klick-Erfassung

> [!NOTE]
> In diesem Abschnitt werden die allgemeinen Optionen des Assistenten beschrieben. Die ausgewählten Optionen hängen davon ab, welches Datenformat und welche Art von Datenquelle erfasst wird und ob die Erfassung in einer neuen oder vorhandenen Tabelle erfolgt.
>
> Beispielszenarien finden Sie unter folgenden Links:
> * Erfassen in einer [neuen Tabelle aus einem Container im CSV-Format](one-click-ingestion-new-table.md)
> * Erfassen in einer [vorhandenen Tabelle aus einer lokalen Datei im JSON-Format](one-click-ingestion-existing-table.md) 

Der Assistent führt Sie durch die folgenden Optionen:
   * Erfassung in einer [vorhandenen Tabelle](one-click-ingestion-existing-table.md)
   * Erfassung in einer [neuen Tabelle](one-click-ingestion-new-table.md)
   * Erfassung von Daten aus:
      * Blobspeicher: Bis zu zehn Blobs
      * [Lokale Datei:](one-click-ingestion-existing-table.md) Bis zu zehn Dateien
      * [Container](one-click-ingestion-new-table.md) (Blobcontainer, ADLS Gen1-Container, ADLS Gen2-Container)

### <a name="schema-mapping"></a>Schemazuordnung

Der Dienst generiert automatisch Schema- und Erfassungseigenschaften, die Sie ändern können. Abhängig davon, ob die Erfassung in einer neuen oder einer vorhandenen Tabelle erfolgt, können Sie eine vorhandene Zuordnungsstruktur verwenden oder eine neue erstellen.

Führen Sie auf der Registerkarte **Schema** die folgenden Aktionen aus:
   * Bestätigen Sie den automatisch generierten Komprimierungstyp.
   * Wählen Sie das [Format Ihrer Daten](#file-formats) aus. Unterschiedliche Formate ermöglichen weitere Änderungen.
   * Ändern Sie die Zuordnung im [Editor-Fenster](#editor-window).

#### <a name="file-formats"></a>Dateiformate

Die 1-Klick-Erfassung unterstützt die Erfassung von Quelldaten in allen [von Azure Data Explorer für die Erfassung unterstützten Datenformaten](ingestion-supported-formats.md).

### <a name="editor-window"></a>Editor-Fenster

Im Fenster **Editor** der Registerkarte **Schema** können Sie Datentabellenspalten nach Bedarf anpassen. 

[!INCLUDE [data-explorer-one-click-column-table](includes/data-explorer-one-click-column-table.md)]

>[!NOTE]
> Sie können jederzeit über dem Bereich **Editor** den [Befehls-Editor](one-click-ingestion-new-table.md#command-editor) öffnen. Im Befehls-Editor können Sie die aus Ihren Eingaben generierten automatischen Befehle anzeigen und kopieren.

#### <a name="mapping-transformations"></a>Zuordnungstransformationen

Einige der Datenformatzuordnungen (Parquet, JSON und Avro) unterstützen einfache Transformationen während der Erfassung. Erstellen oder aktualisieren Sie zum Anwenden von Zuordnungstransformationen eine Spalte im [Editorfenster](#editor-window).

Zuordnungstransformationen können für eine Spalte ausgeführt werden, für die als **Typ** der Wert „string“ oder „datetime“ und für **Quelle** der Datentyp „int“ oder „long“ angegeben ist. Die folgenden Zuordnungstransformationen werden unterstützt:
* DateTimeFromUnixSeconds
* DateTimeFromUnixMilliseconds
* DateTimeFromUnixMicroseconds
* DateTimeFromUnixNanoseconds

Weitere Informationen finden Sie unter [
Mapping von Transformationen](#mapping-transformations).

### <a name="data-ingestion"></a>Datenerfassung

Wenn die Schemazuordnung und die Spaltenbearbeitung abgeschlossen sind, startet der Erfassungs-Assistent die Datenerfassung. 

* Beim Erfassen von Daten aus Quellen, die **keine Container** sind, wird die Erfassung sofort ausgeführt.

* Wenn die Datenquelle ein **Container** ist:
    * Die [Richtlinie für die Batchverarbeitung](kusto/management/batchingpolicy.md) von Azure Data Explorer aggregiert Ihre Daten. 
    * Nach der Erfassung können Sie den Erfassungsbericht herunterladen und die Leistung jedes behandelten Blobs überprüfen. 
    * Sie können eine **kontinuierliche Erfassung erstellen** und die [kontinuierliche Erfassung mit Event Grid](one-click-ingestion-new-table.md#create-continuous-ingestion-for-container) einrichten.
 
### <a name="initial-data-exploration"></a>Anfängliche Datenuntersuchung
   
Nach der Erfassung bietet der Assistent Optionen zur Verwendung von **[Schnellbefehlen](one-click-ingestion-existing-table.md#explore-quick-queries-and-tools)** für die erste Untersuchung Ihrer Daten.


## <a name="next-steps"></a>Nächste Schritte

* [Erfassen von JSON-Daten aus einer lokalen Datei in einer vorhandenen Tabelle in Azure Data Explorer mithilfe der 1-Klick-Erfassung](one-click-ingestion-existing-table.md)
* [Erfassen von CSV-Daten aus einem Container in einer neuen Tabelle in Azure Data Explorer mithilfe der 1-Klick-Erfassung](one-click-ingestion-new-table.md)
* [Abfragen von Daten auf der Azure Data Explorer-Webbenutzeroberfläche](web-query-data.md)
* [Schreiben von Abfragen für Azure Data Explorer mithilfe der Kusto-Abfragesprache](write-queries.md)
