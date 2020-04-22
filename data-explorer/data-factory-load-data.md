---
title: Kopieren von Daten aus Azure Data Factory in Azure Data Explorer
description: In diesem Artikel wird erläutert, wie Sie Daten mithilfe des Azure Data Factory-Kopiertools in Azure Data Explorer erfassen (laden).
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: c937d69df73ed523d1e4f3f0614eadf249ced0f3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81493154"
---
# <a name="copy-data-to-azure-data-explorer-by-using-azure-data-factory"></a>Kopieren von Daten in Azure Data Explorer mithilfe von Azure Data Factory 

Azure Data Explorer ist ein schneller, vollständig verwalteter Datenanalysedienst. Er bietet Echtzeitanalysen großer Datenmengen, die aus vielfältigen Quellen wie Anwendungen, Websites und IoT-Geräten gestreamt werden. Mit Azure Data Explorer können Sie Daten interaktiv erkunden und Muster und Anomalien ermitteln, um Produkte zu verbessern, die Benutzerfreundlichkeit zu optimieren, Geräte zu überwachen und Vorgänge zu beschleunigen. Sie können neue Fragen überprüfen und Antworten in wenigen Minuten erhalten. 

Azure Data Factory ist ein vollständig verwalteter und cloudbasierter Datenintegrationsdienst. Mit diesem Dienst können Sie Ihre Azure Data Explorer-Datenbank mit Daten aus Ihrem vorhandenen System füllen. Dadurch können Sie beim Erstellen von Analyselösungen Zeit sparen.

Wenn Sie Daten in Azure Data Explorer laden, bietet Data Factory die folgenden Vorteile:

* **Mühelose Einrichtung:** Intuitiver Assistent mit fünf Schritten. Keine Skripterstellung erforderlich.
* **Unterstützung für umfangreiche Datenspeicher**: Integrierte Unterstützung für umfangreiche lokale und cloudbasierte Datenspeicher. Eine ausführliche Liste finden Sie in der Tabelle [Unterstützte Datenspeicher](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Sicher und kompatibel**: Daten werden über HTTPS oder Azure ExpressRoute übertragen. Globale Dienste stellen sicher, dass Ihre Daten nie die geografische Grenze verlassen.
* **Hohe Leistung**: Datenladegeschwindigkeit von bis zu 1 Gigabyte pro Sekunde (GBit/s) in Azure Data Explorer. Weitere Informationen finden Sie unter [Leistung der Kopieraktivität](/azure/data-factory/copy-activity-performance).

In diesem Artikel verwenden Sie das Data Factory-Tool „Daten kopieren“, um von Daten aus Amazon Simple Storage Service (S3) in Azure Data Explorer zu laden. Ähnliche Schritte können Sie zum Kopieren von Daten aus anderen Datenspeichern ausführen, z. B.:
* [Azure Blob Storage](/azure/data-factory/connector-azure-blob-storage)
* [Azure SQL-Datenbank](/azure/data-factory/connector-azure-sql-database)
* [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse)
* [Google BigQuery](/azure/data-factory/connector-google-bigquery)
* [Oracle](/azure/data-factory/connector-oracle)
* [Dateisystem](/azure/data-factory/connector-file-system)

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* [Schnellstart: Erstellen eines Azure Data Explorer-Clusters und einer Datenbank](create-cluster-database-portal.md).
* Eine Datenquelle

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com) an.

1. Wählen Sie im linken Bereich die Optionen **Ressource erstellen** > **Analysen** > **Data Factory** aus.

   ![Erstellen einer Data Factory im Azure-Portal](media/data-factory-load-data/create-adf.png)

1. Geben Sie im Bereich **Neue Data Factory** die Werte für die Felder in der folgenden Tabelle an:

   ![Bereich „Neue Data Factory“](media/data-factory-load-data/my-new-data-factory.png)  

   | Einstellung  | Einzugebender Wert  |
   |---|---|
   | **Name** | Geben Sie in diesem Feld einen global eindeutigen Namen für die Data Factory ein. Wenn die Fehlermeldung *Die Data Factory mit dem Namen \"LoadADXDemo\" ist nicht verfügbar* angezeigt wird, geben Sie einen anderen Namen für die Data Factory ein. Regeln zum Benennen von Data Factory-Artefakten finden Sie unter [Azure Data Factory – Benennungsregeln](/azure/data-factory/naming-rules).|
   | **Abonnement** | Wählen Sie in der Dropdownliste das Azure-Abonnement aus, in dem die Data Factory erstellt werden soll. |
   | **Ressourcengruppe** | Wählen Sie **Neu erstellen** aus, und geben Sie dann den Namen einer neuen Ressourcengruppe ein. Wenn Sie bereits über eine Ressourcengruppe verfügen, wählen Sie **Vorhandene verwenden** aus. |
   | **Version** | Wählen Sie in der Dropdownliste die Option **V2** aus. |    
   | **Location** | Wählen Sie in der Dropdownliste den Standort für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Die in der Data Factory verwendeten Datenspeicher können an anderen Standorten oder in anderen Regionen vorhanden sein. |

1. Klicken Sie auf **Erstellen**.

1. Wählen Sie auf der Symbolleiste die Option **Benachrichtigungen** aus, um den Erstellungsvorgang zu überwachen. Nachdem Sie die Data Factory erstellt haben, wählen Sie sie aus.
   
   Der Bereich **Data Factory** wird geöffnet.

   ![Bereich „Data Factory“](media/data-factory-load-data/data-factory-home-page.png)

1. Wählen Sie die Kachel **Erstellen und überwachen** aus, um die Anwendung in einem separaten Bereich zu öffnen.

## <a name="load-data-into-azure-data-explorer"></a>Laden von Daten in Azure Data Explorer

Sie können Daten aus verschiedenen Typen von [Datenspeichern](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) in Azure Data Explorer laden. In diesem Artikel wird erläutert, wie Daten aus Amazon S3 geladen werden.

Sie können die Daten mit einer der folgenden Methoden laden:

* Wählen Sie auf der Azure Data Factory-Benutzeroberfläche im linken Bereich das Symbol **Autor** aus. Dies wird im Abschnitt „Erstellen einer Data Factory“ des Artikels zum [Erstellen einer Data Factory über die Azure Data Factory-Benutzeroberfläche](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory) gezeigt.
* Im Azure Data Factory-Tool „Daten kopieren“, wie unter [Kopieren von Daten mithilfe des Tools zum Kopieren von Daten](/azure/data-factory/quickstart-create-data-factory-copy-data-tool) erläutert.

### <a name="copy-data-from-amazon-s3-source"></a>Kopieren von Daten aus Amazon S3 (Quelle)

1. Öffnen Sie im Bereich **Erste Schritte** das Tool zum Kopieren von Daten durch Auswählen von **Daten kopieren**.

   ![Schaltfläche des Tools „Daten kopieren“](media/data-factory-load-data/copy-data-tool-tile.png)

1. Geben Sie im Bereich **Eigenschaften** im Feld **Aufgabenname** einen Namen ein, und wählen Sie dann **Weiter** aus.

    ![Bereich „Eigenschaften“ von „Daten kopieren“](media/data-factory-load-data/copy-from-source.png)

1. Wählen Sie im Bereich **Quelldatenspeicher** die Option **Neue Verbindung erstellen** aus.

    ![Bereich „Quelldatenspeicher“ von „Daten kopieren“](media/data-factory-load-data/source-create-connection.png)

1. Wählen Sie **Amazon S3** und anschließend **Weiter** aus.

    ![Bereich „Neuer verknüpfter Dienst“](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. Gehen Sie im Bereich **Neuer verknüpfter Dienst (Amazon S3)** wie folgt vor:

    ![Mit Amazon S3 verknüpften Dienst angeben](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    a. Geben Sie im Feld **Name** den Namen des neuen verknüpften Diensts ein.

    b. Wählen Sie in der Dropdownliste **Connect via integration runtime** (Verbindung per Integration Runtime herstellen) den entsprechenden Wert aus.

    c. Geben Sie im Feld **Zugriffsschlüssel-ID** den entsprechenden Wert ein.
    
    > [!NOTE]
    > Wählen Sie in Amazon S3 zum Suchen Ihres Zugriffsschlüssels Ihren Amazon-Benutzernamen auf der Navigationsleiste aus, und navigieren Sie dann zu **My Security Credentials** (Meine Sicherheitsanmeldeinformationen).
    
    d. Geben Sie im Feld **Secret Access Key** (Geheimer Zugriffsschlüssel) einen Wert ein.

    e. Wählen Sie **Verbindung testen** aus, um die erstellte Verbindung mit dem verknüpften Dienst zu testen.

    f. Wählen Sie **Fertig stellen** aus.
    
      Im Bereich **Quelldatenspeicher** wird die neue Verbindung „AmazonS31“ angezeigt. 

1. Wählen Sie **Weiter** aus.

   ![Erstellte Verbindung mit Quelldatenspeicher](media/data-factory-load-data/source-data-store-created-connection.png)

1. Führen Sie im Bereich **Eingabedatei oder -ordner auswählen** die folgenden Schritte aus:

    a. Navigieren Sie zu der Datei oder dem Ordner, die bzw. den Sie kopieren möchten, und wählen Sie sie bzw. ihn aus.

    b. Wählen Sie das gewünschte Kopierverhalten aus. Stellen Sie sicher, dass das Kontrollkästchen **Binary Copy** (Binärkopie) deaktiviert ist.

    c. Wählen Sie **Weiter** aus.

    ![Auswählen der Eingabedatei bzw. des Eingabeordners](media/data-factory-load-data/source-choose-input-file.png)

1. Wählen Sie im Bereich **File format settings** (Einstellungen für Dateiformate) die relevanten Einstellungen für die Datei aus, und wählen Sie dann **Weiter** aus.

   ![Bereich „File format settings“ (Dateiformateinstellungen)](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Kopieren von Daten in Azure Data Explorer (Ziel)

Der neue verknüpfte Azure Data Explorer-Dienst wird erstellt, um die Daten in die in diesem Abschnitt angegebene Azure Data Explorer-Zieltabelle (Senke) zu kopieren.

> [!NOTE]
> Verwenden Sie die [Azure Data Factory-Befehlsaktivität zum Ausführen von Azure Data Explorer-Steuerungsbefehlen](data-factory-command-activity.md), und verwenden Sie einen [Befehl für die Erfassung aus einer Abfrage](kusto/management/data-ingestion/ingest-from-query.md), z. B. `.set-or-replace`.

#### <a name="create-the-azure-data-explorer-linked-service"></a>Erstellen des verknüpften Azure Data Explorer-Diensts

Führen Sie zum Erstellen des verknüpften Azure Data Explorer-Diensts die folgenden Schritte aus:

1. Zum Verwenden einer vorhandenen Datenspeicherverbindung oder Angeben eines neuen Datenspeichers wählen Sie im Bereich **Zieldatenspeicher** die Option **Neue Verbindung erstellen** aus.

    ![Bereich „Zieldatenspeicher“](media/data-factory-load-data/destination-create-connection.png)

1. Wählen Sie im Bereich **Neuer verknüpfter Dienst** die Option **Azure Data Explorer** und dann **Weiter** aus.

    ![Bereich „Neuer verknüpfter Dienst“](media/data-factory-load-data/adx-select-new-linked-service.png)

1. Führen Sie im Bereich **Neuer verknüpfter Dienst (Azure Data Explorer)** die folgenden Schritte aus:

    ![Azure Data Explorer – Bereich „Neuer verknüpfter Dienst“](media/data-factory-load-data/adx-new-linked-service.png)

   a. Geben Sie im Feld **Name** einen Namen für den verknüpften Azure Data Explorer-Dienst ein.

   b. Wählen Sie unter **Kontoauswahlmethode** eine der folgenden Optionen aus: 

    * Wählen Sie **From Azure subscription** (Aus Azure-Abonnement) und dann in den jeweiligen Dropdownlisten Ihr **Azure-Abonnement** und Ihren **Cluster** aus. 

        > [!NOTE]
        > In der Dropdownliste **Cluster** sind nur Cluster aufgeführt, die Ihrem Abonnement zugeordnet sind.

    * Wählen Sie **Manuell eingeben** aus, und geben Sie dann den **Endpunkt** ein.

   c. Geben Sie im Feld **Mandant** den Namen des Mandanten ein.

   d. Geben Sie im Feld **Dienstprinzipal-ID** die Dienstprinzipal-ID ein.

   e. Wählen Sie **Dienstprinzipalschlüssel** aus, und geben Sie dann im Feld **Dienstprinzipalschlüssel** den Wert für den Schlüssel ein.

   f. Wählen Sie in der Dropdownliste **Datenbank** den Namen der Datenbank aus. Alternativ können Sie das Kontrollkästchen **Bearbeiten** aktivieren und dann den Namen der Datenbank eingeben.

   g. Wählen Sie **Verbindung testen** aus, um die erstellte Verbindung mit dem verknüpften Dienst zu testen. Wenn Sie eine Verbindung mit dem verknüpften Dienst herstellen können, wird im Bereich ein grünes Häkchensymbol und die Meldung **Verbindung erfolgreich** angezeigt.

   h. Wählen Sie **Fertig stellen** aus, um die Erstellung des verknüpften Diensts abzuschließen.

    > [!NOTE]
    > Der Dienstprinzipal wird von Azure Data Factory für den Zugriff auf den Azure Data Explorer-Dienst verwendet. Informationen zum Erstellen eines Dienstprinzipals finden Sie unter [Erstellen eines Azure Active Directory-Dienstprinzipals (Azure AD)](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal). Verwenden Sie nicht die Azure Key Vault-Methode.

#### <a name="configure-the-azure-data-explorer-data-connection"></a>Konfigurieren der Azure Data Explorer-Datenverbindung

Nach dem Erstellen der Verbindung für den verknüpften Dienst wird der Bereich **Zieldatenspeicher** geöffnet, und die erstellte Verbindung ist zur Verwendung verfügbar. Führen Sie zum Konfigurieren der Verbindung die folgenden Schritte aus:

1. Wählen Sie **Weiter** aus.

    ![Azure Data Explorer – Bereich „Zieldatenspeicher“](media/data-factory-load-data/destination-data-store.png)

1. Legen Sie im Bereich **Tabellenmapping** den Namen der Zieltabelle fest, und wählen Sie **Weiter** aus.

    ![Zieldataset – Bereich „Tabellenmapping“](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. Im Bereich **Spaltenzuordnung** werden die folgenden Zuordnungen durchgeführt:

    a. Die erste Zuordnung erfolgt in Azure Data Factory entsprechend der [Azure Data Factory-Schemazuordnung](/azure/data-factory/copy-activity-schema-and-type-mapping). Gehen Sie folgendermaßen vor:

    * Legen Sie die **Column mappings** (Spaltenzuordnungen) für die Azure Data Factory-Zieltabelle fest. Die Standardzuordnung von der Quelle zur Azure Data Factory-Zieltabelle wird angezeigt.

    * Heben Sie die Auswahl der Spalten auf, die Sie zum Definieren der Spaltenzuordnung nicht benötigen.

    b. Die zweite Zuordnung erfolgt, wenn diese tabellarischen Daten in Azure Data Explorer erfasst werden. Die Zuordnung erfolgt gemäß den [CSV-Zuordnungsregeln](kusto/management/mappings.md#csv-mapping). Selbst wenn die Quelldaten nicht im CSV-Format vorliegen, werden sie in Azure Data Factory in ein Tabellenformat umgewandelt. Daher ist die CSV-Zuordnung in dieser Phase die einzige relevante Zuordnung. Gehen Sie folgendermaßen vor:

    * (Optional:) Fügen Sie unter **Azure Data Explorer (Kusto) sink properties** (Azure Data Explorer-Senkeneigenschaften (Kusto)) den relevanten **Ingestion mapping name** (Name der Erfassungszuordnung) hinzu, sodass die Spaltenzuordnung verwendet werden kann.

    * Wenn kein Wert für **Ingestion mapping name** (Name der Erfassungszuordnung) angegeben wird, wird die Zuordnungsreihenfolge *nach Name* angewandt, die im Abschnitt **Spaltenzuordnungen** definiert wurde. Wenn bei der Zuordnung *nach Name* Fehler auftreten, versucht Azure Data Explorer, die Daten mittels Zuordnung *nach Spaltenposition* zu erfassen (d. h., standardmäßig werden Zuordnungen nach Position vorgenommen).

    * Wählen Sie **Weiter** aus.

    ![Zieldataset – Bereich „Spaltenzuordnung“](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. Führen Sie im Bereich **Einstellungen** die folgenden Schritte aus:

    a. Geben Sie unter **Fault tolerance settings** (Fehlertoleranzeinstellungen) die relevanten Einstellungen ein.

    b. Unter **Leistungseinstellungen** ist **Enable staging** (Staging aktivieren) nicht anwendbar, und **Erweiterte Einstellungen** schließt Kostenberücksichtigungen ein. Sofern keine spezifischen Anforderungen vorliegen, lassen Sie die Einstellungen unverändert.

    c. Wählen Sie **Weiter** aus.

    ![Daten kopieren – Bereich „Einstellungen“](media/data-factory-load-data/copy-data-settings.png)

1. Überprüfen Sie im Bereich **Zusammenfassung** die Einstellungen, und wählen Sie anschließend **Weiter** aus.

    ![Daten kopieren – Bereich „Zusammenfassung“](media/data-factory-load-data/copy-data-summary.png)

1. Führen Sie im Bereich **Deployment complete** (Bereitstellung abgeschlossen) die folgenden Schritte aus:

    a. Wählen Sie **Überwachung** aus, um zur Registerkarte **Überwachung** zu wechseln und den Status der Pipeline (Fortschritt, Fehler und Datenfluss) zu überwachen.

    b. Wählen Sie **Pipeline bearbeiten** aus, um verknüpfte Dienste, Datasets und Pipelines zu bearbeiten.

    c. Wählen Sie **Fertig stellen** aus, um die Aufgabe „Daten kopieren“ abzuschließen.

    ![Bereich „Deployment complete“ (Bereitstellung abgeschlossen)](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über den [Azure Data Explorer-Connector](/azure/data-factory/connector-azure-data-explorer) in Azure Data Factory.
* Informieren Sie sich über das Bearbeiten von verknüpften Diensten, Datasets und Pipelines in der [Data Factory-Benutzeroberfläche](/azure/data-factory/quickstart-create-data-factory-portal).
* Erfahren Sie mehr über [Azure Data Explorer-Abfragen](/azure/data-explorer/web-query-data) zum Abfragen von Daten.
