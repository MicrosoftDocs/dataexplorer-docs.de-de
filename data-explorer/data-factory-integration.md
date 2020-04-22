---
title: Integration von Azure Data Explorer in Azure Data Factory
description: In diesem Thema wird beschrieben, wie Sie Azure Data Explorer in Azure Data Factory integrieren, um die Kopier-, Lookup- und Befehlsaktivitäten verwenden zu können.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tomersh26
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: 5b8e9844894df9c49c1abd703ebc5a14b4c7050c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81493142"
---
# <a name="integrate-azure-data-explorer-with-azure-data-factory"></a>Integration von Azure Data Explorer und Azure Data Factory

[Azure Data Factory](/azure/data-factory/) (ADF) ist ein cloudbasierter Datenintegrationsdienst, der die Integration verschiedener Datenspeicher und die Durchführung von Aktivitäten für die Daten ermöglicht. Mit ADF können Sie datengesteuerte Workflows zur Orchestrierung und Automatisierung der Datenverschiebung und Transformation von Daten erstellen. Azure Data Explorer ist einer der [unterstützten Datenspeicher](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) in Azure Data Factory. 

## <a name="azure-data-factory-activities-for-azure-data-explorer"></a>Azure Data Factory-Aktivitäten für Azure Data Explorer

Für Azure Data Explorer-Benutzer sind verschiedene Azure Data Factory-Integrationen verfügbar:

### <a name="copy-activity"></a>Copy-Aktivität
 
Die Kopieraktivität von Azure Data Factory wird verwendet, um Daten zwischen Datenspeichern zu übertragen. Azure Data Explorer wird als Quelle, aus der Daten in Azure Data Explorer in einen beliebigen unterstützten Datenspeicher kopiert werden, und als Senke unterstützt, in die Daten aus einem beliebigen unterstützten Datenspeicher in Azure Data Explorer kopiert werden. Weitere Informationen finden Sie unter [Kopieren von Daten in oder aus Azure Data Explorer mithilfe von Azure Data Factory](/azure/data-factory/connector-azure-data-explorer). Eine ausführliche exemplarische Vorgehensweise finden Sie unter [Laden von Daten aus Azure Data Factory in Azure Data Explorer](data-factory-load-data.md).
Azure Data Explorer wird von Azure IR (Integration Runtime) unterstützt, um Daten innerhalb Azure zu kopieren. Azure Data Explorer wird von einer selbstgehosteten IR unterstützt, um von Daten aus/in Datenspeicher zu kopieren, die sich lokal oder in einem Netzwerk mit Zugriffssteuerung, z. B. in einem virtuellen Azure-Netzwerk, befinden. Weitere Informationen finden Sie unter [Zu verwendende Integration Runtime](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use).
 
> [!TIP]
> Wenn Sie die Kopieraktivität verwenden und einen **verknüpften Dienst** oder ein **Dataset** erstellen, wählen Sie den Datenspeicher **Azure Data Explorer(Kusto)** und nicht den alten Datenspeicher **Kusto** aus.  

### <a name="lookup-activity"></a>Lookup-Aktivität
 
Die Lookup-Aktivität wird zum Ausführen von Abfragen in Azure Data Explorer verwendet. Das Ergebnis der Abfrage wird als Ausgabe der Lookup-Aktivität zurückgegeben und kann in der nächsten Aktivität in der Pipeline verwendet werden (siehe Beschreibung in der [ADF-Lookup-Dokumentation](/azure/data-factory/control-flow-lookup-activity#use-the-lookup-activity-result-in-a-subsequent-activity)).  
Neben der Größenbeschränkung von 5.000 Zeilen und 2 MB für Antworten weist die Aktivität auch ein Abfragezeitlimit von 1 Stunde auf.

### <a name="command-activity"></a>Befehlsaktivität

Die Befehlsaktivität ermöglicht die Ausführung von Azure Data Explorer-[Steuerungsbefehlen](kusto/concepts/index.md#control-commands). Im Gegensatz zu Abfragen sind Steuerungsbefehle in der Lage, Daten oder Metadaten zu ändern. Einige der Steuerungsbefehle dienen zum Erfassen von Daten in Azure Data Explorer mithilfe von Befehlen wie `.ingest` oder `.set-or-append` bzw. zum Kopieren von Daten aus Azure Data Explorer in externe Datenspeicher mithilfe von Befehlen wie `.export`.
Eine ausführliche exemplarische Vorgehensweise für die Befehlsaktivität finden Sie unter [Verwenden der Azure Data Factory-Befehlsaktivität zum Ausführen von Azure Data Explorer-Steuerungsbefehlen](data-factory-command-activity.md).  Die Verwendung eines Steuerungsbefehls zum Kopieren von Daten kann manchmal eine schnellere und kostengünstigere Option als die Kopieraktivität sein. Weitere Informationen zum Bestimmen, wann die Befehlsaktivität und wann die Kopieraktivität verwendet werden sollte, finden Sie unter [Auswählen zwischen Kopier- und Befehlsaktivitäten beim Kopieren von Daten](#select-between-copy-and-azure-data-explorer-command-activities-when-copy-data).

### <a name="copy-in-bulk-from-a-database-template"></a>Massenkopieren aus einer Datenbankvorlage

Die [Azure Data Factory-Vorlage zum Massenkopieren aus einer Datenbank in Azure Data Explorer](data-factory-template.md) ist eine vordefinierte Azure Data Factory-Pipeline. Die Vorlage wird zum Erstellen einer Vielzahl von Pipelines für eine Datenbank oder Tabelle verwendet, damit Daten schneller kopiert werden. 

### <a name="mapping-data-flows"></a>Zuordnen von Datenflüssen 

[Azure Data Factory-Zuordnungsdatenflüsse](/azure/data-factory/concepts-data-flow-overview) sind visuell gestaltete Datentransformationen, mit denen Datentechniker eine grafische Datentransformationslogik entwickeln können, ohne Code schreiben zu müssen. Verwenden Sie zum Erstellen eines Datenflusses und zum Erfassen von Daten in Azure Data Explorer die folgende Methode:

1. Erstellen Sie den [Zuordnungsdatenfluss](/azure/data-factory/data-flow-create).
1. [Exportieren Sie die Daten in Azure-Blob](/azure/data-factory/data-flow-sink). 
1. Definieren Sie [Event Grid](/azure/data-explorer/ingest-data-event-grid) oder die [ADF-Kopieraktivität](/azure/data-explorer/data-factory-load-data), um die Daten in Azure Data Explorer zu erfassen.

## <a name="select-between-copy-and-azure-data-explorer-command-activities-when-copy-data"></a>Auswählen zwischen Kopier- und Azure Data Explorer-Befehlsaktivitäten beim Kopieren von Daten 

Dieser Abschnitt unterstützt Sie bei der Auswahl der richtigen Aktivität für die jeweiligen Datenkopieranforderungen.

Beim Kopieren von Daten aus oder in Azure Data Explorer stehen in Azure Data Factory zwei Optionen zur Verfügung:
* Kopieraktivität.
* Azure Data Explorer-Befehlsaktivität, die einen der Steuerungsbefehle ausführt, die Daten in Azure Data Explorer übertragen. 

### <a name="copy-data-from-azure-data-explorer"></a>Kopieren von Daten aus Azure Data Explorer
  
Sie können Daten aus Azure Data Explorer mit der Kopieraktivität oder mit dem Befehl [`.export`](kusto/management/data-export/index.md) kopieren. Der `.export`-Befehl führt eine Abfrage aus und exportiert dann die Ergebnisse der Abfrage. 

In der folgenden Tabelle finden Sie einen Vergleich zwischen der Kopieraktivität und dem `.export`-Befehl zum Kopieren von Daten aus Azure Data Explorer.

| | Copy-Aktivität | .export-Befehl |
|---|---|---|
| **Beschreibung des Ablaufs** | ADF führt eine Abfrage in Kusto aus, verarbeitet das Ergebnis und sendet es an den Zieldatenspeicher. <br>(**ADX > ADF > Senkendatenspeicher**) | ADF sendet einen `.export`-Steuerungsbefehl an Azure Data Explorer, der den Befehl ausführt, und sendet die Daten direkt an den Zieldatenspeicher. <br>(**ADX > Senkendatenspeicher**) |
| **Unterstützte Zieldatenspeicher** | Eine Vielzahl von [unterstützten Datenspeichern](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLSv2, Azure-Blob, SQL-Datenbank |
| **Leistung** | Zentralisiert | <ul><li>Verteilt (Standard), gleichzeitiger Export von Daten aus mehreren Knoten</li><li>Schneller und COGS-effizient.</li></ul> |
| **Servereinschränkungen** | [Abfragelimits](kusto/concepts/querylimits.md) können erweitert/deaktiviert werden. Standardmäßig gilt für ADF-Abfragen: <ul><li>Größenbeschränkung von 500.000 Datensätzen oder 64 MB.</li><li>Zeitlimit von 10 Minuten.</li><li>`noTruncation` auf FALSE festgelegt.</li></ul> | Erweitert oder deaktiviert standardmäßig die Abfragelimits: <ul><li>Größenbeschränkungen sind deaktiviert.</li><li>Das Serverzeitlimit wird auf 1 Stunde verlängert.</li><li>`MaxMemoryConsumptionPerIterator` und `MaxMemoryConsumptionPerQueryPerNode` werden auf das Maximum (5 GB, Hälfte des gesamten physischen Arbeitsspeichers) erweitert.</li></ul>

> [!TIP]
> Wenn das Kopierziel einer der vom `.export`-Befehl unterstützten Datenspeicher ist und keine Funktion der Kopieraktivität für die jeweiligen Anforderungen entscheidend ist, wählen Sie den `.export`-Befehl aus.

### <a name="copying-data-to-azure-data-explorer"></a>Kopieren von Daten nach Azure Data Explorer

Sie können Daten mit der Kopieraktivität oder mit den Erfassungsbefehlen, z. B. [Erfassung aus Abfrage](kusto/management/data-ingestion/ingest-from-query.md) (`.set-or-append`, `.set-or-replace`, `.set`, `.replace)` und [Erfassung aus Speicher](kusto/management/data-ingestion/ingest-from-storage.md) (`.ingest`) in Azure Data Explorer kopieren. 

In der folgenden Tabelle finden Sie einen Vergleich zwischen der Kopieraktivität und dem Erfassungsbefehl zum Kopieren von Daten nach Azure Data Explorer.

| | Copy-Aktivität | Erfassung aus der Abfrage<br> `.set-or-append` / `.set-or-replace` / `.set` / `.replace` | Erfassung aus dem Speicher <br> `.ingest` |
|---|---|---|---|
| **Beschreibung des Ablaufs** | ADF ruft die Daten aus dem Quelldatenspeicher ab, konvertiert sie in ein Tabellenformat und führt die erforderlichen Schemazuordnungsänderungen durch. ADF lädt die Daten dann in Azure-Blobs hoch, teilt sie in Blöcke auf und lädt die Blobs dann herunter, um sie in der ADX-Tabelle zu erfassen. <br> (**Quelldatenspeicher > ADF > Azure-Blob > ADX**) | Diese Befehle können eine Abfrage oder einen `.show`-Befehl ausführen und die Ergebnisse der Abfrage in einer Tabelle erfassen (**ADX > ADX**). | Mit diesem Befehl werden Daten in einer Tabelle erfasst, indem die Daten aus mindestens einem Cloudspeicherartefakt abgerufen werden (Pull). |
| **Unterstützte Quelldatenspeicher** |  [Vielzahl verschiedener Optionen](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLS Gen 2, Azure-Blob, SQL (mit dem sql_request-Plug-in), Cosmos (mit dem cosmosdb_sql_request-Plug-in) und jeder andere Datenspeicher, der HTTP- oder Python-APIs bereitstellt. | Dateisystem, Azure Blob Storage, ADLS Gen 1, ADLS Gen 2 |
| **Leistung** | Erfassungen werden in die Warteschlange eingereiht und verwaltet. Dadurch werden Erfassungen mit geringer Größe sichergestellt sowie durch Lastenausgleich, Wiederholungsversuchen und Fehlerbehandlung hohe Verfügbarkeit gewährleistet. | <ul><li>Die Befehle wurden nicht für den Import großer Datenmengen konzipiert.</li><li>Funktioniert erwartungsgemäß und günstiger. Verwenden Sie jedoch für Produktionsszenarien und bei großen Datenverkehrsraten sowie umfangreichen Daten die Kopieraktivität.</li></ul> |
| **Servereinschränkungen** | <ul><li>Kein Größenlimit.</li><li>Maximales Zeitlimit: 1 Stunde für jedes erfasste Blob. |<ul><li>Es gibt lediglich eine Größenbeschränkung für den Abfrageteil, die durch Angeben von `noTruncation=true` ausgesetzt werden kann.</li><li>Maximales Zeitlimit: 1 Stunde.</li></ul> | <ul><li>Kein Größenlimit.</li><li>Maximales Zeitlimit: 1 Stunde.</li></ul>|

> [!TIP]
> * Verwenden Sie beim Kopieren von Daten aus ADF nach Azure Data Explorer die `ingest from query`-Befehle.  
> * Verwenden Sie für große Datasets (> 1 GB) die Kopieraktivität.  

## <a name="required-permissions"></a>Erforderliche Berechtigungen

In der folgenden Tabelle sind die erforderlichen Berechtigungen für verschiedene Schritte bei der Integration mit Azure Data Factory aufgeführt.

| Schritt | Vorgang | Minimale Berechtigungsebene | Notizen |
|---|---|---|---|
| **Erstellen eines verknüpften Diensts** | Datenbanknavigation | *Anzeigender Datenbankbenutzer* <br>Der angemeldete Benutzer, der ADF verwendet, muss über die Berechtigung verfügen, Datenbankmetadaten zu lesen. | Der Benutzer kann den Datenbanknamen manuell angeben. |
| | Testen der Verbindung | *Datenbankmonitor* oder *Tabelleningestor* <br>Der Dienstprinzipal muss über die Berechtigung verfügen, `.show`-Befehle auf Datenbankebene oder eine Erfassung auf Tabellenebene auszuführen. | <ul><li>TestConnection überprüft die Verbindung zum Cluster und nicht die Verbindung zur Datenbank. Die Überprüfung kann auch dann erfolgreich sein, wenn die Datenbank nicht vorhanden ist.</li><li>Berechtigungen als Tabellenadministrator sind nicht ausreichend.</li></ul>|
| **Erstellen eines Datasets** | Tabellennavigation | *Datenbankmonitor* <br>Der angemeldete Benutzer, der ADF verwendet, muss über die Berechtigung verfügen, `.show`-Befehle auf Datenbankebene auszuführen. | Der Benutzer kann den Tabellennamen manuell angeben.|
| **Erstellen eines Datasets** oder **Kopieraktivität** | Datenvorschau | *Anzeigender Datenbankbenutzer* <br>Der Dienstprinzipal muss über die Berechtigung verfügen, Datenbankmetadaten zu lesen. | | 
|   | Schema importieren | *Anzeigender Datenbankbenutzer* <br>Der Dienstprinzipal muss über die Berechtigung verfügen, Datenbankmetadaten zu lesen. | Wenn ADX die Quelle eines Kopiervorgangs von einer Tabelle in eine andere Tabelle ist, importiert ADF automatisch das Schema – auch dann, wenn der Benutzer das Schema nicht explizit importiert hat. |
| **ADX als Senke** | Erstellen einer Spaltenzuordnung anhand der Namen | *Datenbankmonitor* <br>Der Dienstprinzipal muss über die Berechtigung verfügen, `.show`-Befehle auf Datenbankebene auszuführen. | <ul><li>Alle obligatorischen Vorgänge werden mit dem *Tabelleningestor* erledigt.</li><li> Einige optionale Vorgänge können fehlschlagen.</li></ul> |
|   | <ul><li>Erstellen einer CSV-Zuordnung für die Tabelle</li><li>Löschen der Zuordnung</li></ul>| *Tabelleningestor* oder *Datenbankadministrator* <br>Der Dienstprinzipal muss über die Berechtigung verfügen, Änderungen an einer Tabelle vorzunehmen. | |
|   | Erfassen von Daten | *Tabelleningestor* oder *Datenbankadministrator* <br>Der Dienstprinzipal muss über die Berechtigung verfügen, Änderungen an einer Tabelle vorzunehmen. | | 
| **ADX als Quelle** | Abfrage ausführen | *Anzeigender Datenbankbenutzer* <br>Der Dienstprinzipal muss über die Berechtigung verfügen, Datenbankmetadaten zu lesen. | |
| **Kusto-Befehl** | | Gemäß Berechtigungsstufe des jeweiligen Befehls. |

## <a name="performance"></a>Leistung 

Wenn Azure Data Explorer die Quelle ist und Sie eine Lookup-, Kopier- oder Befehlsaktivität verwenden, die eine Abfrage enthält, finden Sie unter [Bewährte Methoden für Abfragen](kusto/query/best-practices.md) Informationen zur Leistung. Weitere Informationen finden Sie darüber hinaus in der [ADF-Dokumentation für die Kopieraktivität](/azure/data-factory/copy-activity-performance).
  
In diesem Abschnitt wird die Verwendung der Kopieraktivität behandelt, wenn Azure Data Explorer die Senke ist. Der geschätzte Durchsatz für eine Azure Data Explorer-Senke beträgt 11-13 Mbit/s. In der folgenden Tabelle sind die Parameter aufgeführt, die die Leistung der Azure Data Explorer-Senke beeinflussen.

| Parameter | Notizen |
|---|---|
| **Geografische Anordnung der Komponenten** | Platzieren Sie alle Komponenten in derselben Region:<ul><li>Quellen- und Senkendatenspeicher.</li><li>ADF Integration Runtime.</li><li>Ihr ADX-Cluster.</li></ul>Stellen Sie sicher, dass sich zumindest die Integration Runtime in derselben Region wie Ihr ADX-Cluster befindet. |
| **Anzahl von DIUs** | 1 VM für jeweils 4 DIUs, die von ADF verwendet werden. <br>Eine Erhöhung der Anzahl von DIUs ist nur hilfreich, wenn es sich bei der Quelle um einen dateibasierten Speicher mit mehreren Dateien handelt. Die verschiedenen Dateien werden dann parallel von einer VM verarbeitet. Daher weist das Kopieren einer einzelnen großen Datei eine höhere Latenz auf als das Kopieren mehrerer kleinerer Dateien.|
|**Umfang und SKU des ADX-Clusters** | Eine hohe Anzahl von ADX-Knoten steigert die Verarbeitungszeit für die Erfassung.|
| **Parallelität** |    Wenn Sie sehr viele Daten aus einer Datenbank kopieren möchten, partitionieren Sie die Daten und verwenden Sie dann eine ForEach-Schleife, um die einzelnen Partitionen parallel zu kopieren. Sie können auch die [Vorlage zum Massenkopieren aus der Datenbank in Azure Data Explorer](data-factory-template.md) verwenden. Hinweis: **Einstellungen** > **Parallelitätsgrad** in der Kopieraktivität ist für ADX nicht relevant. |
| **Komplexität der Datenverarbeitung** | Die Latenz hängt jeweils vom Format der Quelldatei, von der Spaltenzuordnung und von der Komprimierung ab.|
| **Die VM, auf der die Integration Runtime ausgeführt wird** | <ul><li>Für Azure-interne Kopiervorgänge können ADF-VMs und Computer-SKUs nicht geändert werden.</li><li> Bei Kopiervorgängen aus der lokalen Umgebung nach Azure muss sichergestellt sein, dass der virtuelle Hostcomputer für die selbstgehostete IR leistungsfähig genug ist.</li></ul>|

## <a name="tips-and-common-pitfalls"></a>Tipps und häufige Probleme

### <a name="monitor-activity-progress"></a>Überwachen des Aktivitätsstatus

* Bei der Überwachung des Aktivitätsstatus ist die Eigenschaft *Daten geschrieben* möglicherweise wesentlich größer als die Eigenschaft *Daten gelesen*, da *Daten gelesen* basierend auf der Größe der Binärdatei berechnet wird, während *Daten geschrieben* basierend auf der Größe im Arbeitsspeicher berechnet wird, nachdem die Daten deserialisiert und dekomprimiert wurden.

* Bei der Überwachung des Aktivitätsstatus können Sie sehen, dass Daten in die Azure Data Explorer-Senke geschrieben werden. Wenn Sie die Azure Data Explorer-Tabelle abfragen, sehen Sie, dass die Daten nicht angekommen sind. Dies liegt daran, dass das Kopieren in Azure Data Explorer in zwei Phasen erfolgt. 
    * In der ersten Phase werden die Quelldaten gelesen, in Blöcke zu je 900 MB aufgeteilt und die einzelnen Blöcke in ein Azure-Blob hochgeladen. Die erste Phase ist in der Statusansicht der ADF-Aktivität sichtbar. 
    * Die zweite Phase beginnt, sobald alle Daten in Azure-Blobs hochgeladen wurden. Die Knoten der Azure Data Explorer-Engine laden die Blobs herunter und erfassen die Daten in der Senkentabelle. Die Daten sind dann in der Azure Data Explorer-Tabelle sichtbar.

### <a name="failure-to-ingest-csv-files-due-to-improper-escaping"></a>Fehler beim Erfassen von CSV-Dateien aufgrund einer unsachgemäßen Verwendung von Escapezeichen

Azure Data Explorer erwartet, dass CSV-Dateien [RFC 4180](https://www.ietf.org/rfc/rfc4180.txt) entsprechen.
Voraussetzungen:
* Felder mit Zeichen, die mit einem Escapezeichen versehen werden müssen (z. B. " und Zeilenumbrüche), müssen mit einem Anführungszeichen ( **"** ) ohne Leerzeichen beginnen und enden. Für alle Anführungszeichen ( **"** ) *innerhalb* des Felds wird ein weiteres Anführungszeichen ( **"** ) als Escapezeichen verwendet ( **""** ). Beispielsweise ist _"Hello, ""World"""_ eine gültige CSV-Datei mit einem einzelnen Datensatz, der aus einer einzelnen Spalte oder einem einzelnen Feld mit dem Inhalt _Hello, "World"_ besteht.
* Alle Datensätze in der Datei müssen über dieselbe Anzahl von Spalten und Feldern verfügen.

Azure Data Factory unterstützt den umgekehrten Schrägstrich (Escapezeichen). Wenn Sie mit Azure Data Factory eine CSV-Datei mit umgekehrtem Schrägstrich generieren, schlägt die Erfassung der Datei in Azure Data Explorer fehl.

#### <a name="example"></a>Beispiel

Die Textwerte Hello, "World"<br/>
ABC   DEF<br/>
"ABC\D"EF<br/>
"ABC DEF<br/>

müssen in einer funktionsfähigen CSV-Datei wie folgt aussehen: "Hello, ""World"""<br/>
"ABC   DEF"<br/>
"""ABC DEF"<br/>
"""ABC\D""EF"<br/>

Wenn Sie das standardmäßige Escapezeichen (umgekehrter Schrägstrich) verwenden, wird die folgende CSV-Datei von Azure Data Explorer nicht unterstützt: "Hello, \"World\""<br/>
"ABC   DEF"<br/>
"\"ABC DEF"<br/>
"\"ABC\D\"EF"<br/>

### <a name="nested-json-objects"></a>Geschachtelte JSON-Objekte

Beachten Sie beim Kopieren einer JSON-Datei in Azure Data Explorer Folgendes:
* Arrays werden nicht unterstützt.
* Wenn Ihre JSON-Struktur Objektdatentypen enthält, werden die untergeordneten Elemente des Objekts von Azure Data Factory vereinfacht. Anschließend wird versucht, jedes untergeordnete Element einer anderen Spalte in der Azure Data Explorer-Tabelle zuzuordnen. Wenn das gesamte Objektelement einer einzelnen Spalte in Azure Data Explorer zugeordnet werden soll, gehen Sie wie folgt vor:
    * Erfassen Sie die gesamte JSON-Zeile in einer einzelnen dynamischen Spalte in Azure Data Explorer.
    * Bearbeiten Sie die Pipelinedefinition manuell mit dem JSON-Editor von Azure Data Factory. Unter **Mappings**
       * Entfernen Sie die verschiedenen Zuordnungen, die für jedes untergeordnete Element erstellt wurden, und fügen Sie eine einzelne Zuordnung hinzu, durch die der Objekttyp der Tabellenspalte zugeordnet wird.
       * Fügen Sie nach der schließenden eckigen Klammer ein Komma hinzu, auf das nachstehende Zeichenfolge folgt:<br/>
       `"mapComplexValuesToString": true`.

### <a name="specify-additionalproperties-when-copying-to-azure-data-explorer"></a>Angeben von „AdditionalProperties“ beim Kopieren in Azure Data Explorer

> [!NOTE]
> Diese Funktion ist zurzeit nur verfügbar, wenn die JSON-Nutzlast manuell bearbeitet wird. 

Fügen Sie im Abschnitt „sink“ der Kopieraktivität wie folgt eine einzelne Zeile hinzu:

```json
"sink": {
    "type": "AzureDataExplorerSink",
    "additionalProperties": "{\"tags\":\"[\\\"drop-by:account_FiscalYearID_2020\\\"]\"}"
},
```

Es kann schwierig sein, die richtigen Escapezeichen für den Wert zu verwenden. Der folgende Codeausschnitt soll als Referenz dienen:

```csharp
static void Main(string[] args)
{
       Dictionary<string, string> additionalProperties = new Dictionary<string, string>();
       additionalProperties.Add("ignoreFirstRecord", "false");
       additionalProperties.Add("csvMappingReference", "Table1_mapping_1");
       IEnumerable<string> ingestIfNotExists = new List<string> { "Part0001" };
       additionalProperties.Add("ingestIfNotExists", JsonConvert.SerializeObject(ingestIfNotExists));
       IEnumerable<string> tags = new List<string> { "ingest-by:Part0001", "ingest-by:IngestedByTest" };
       additionalProperties.Add("tags", JsonConvert.SerializeObject(tags));
       var additionalPropertiesForPayload = JsonConvert.SerializeObject(additionalProperties);
       Console.WriteLine(additionalPropertiesForPayload);
       Console.ReadLine();
}
```

Der ausgegebene Wert:

```json
{"ignoreFirstRecord":"false","csvMappingReference":"Table1_mapping_1","ingestIfNotExists":"[\"Part0001\"]","tags":"[\"ingest-by:Part0001\",\"ingest-by:IngestedByTest\"]"}
```

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [Daten mithilfe von Azure Data Factory in Azure Data Explorer kopieren](data-factory-load-data.md).
* Erfahren Sie, wie Sie die [Azure Data Factory-Vorlage zum Massenkopieren aus der Datenbank in Azure Data Explorer](data-factory-template.md) verwenden.
* Erfahren Sie, wie Sie die [Azure Data Factory-Befehlsaktivität zum Ausführen von Azure Data Explorer-Steuerungsbefehlen](data-factory-command-activity.md) verwenden.
* Erfahren Sie mehr über [Azure Data Explorer-Abfragen](/azure/data-explorer/web-query-data) zum Abfragen von Daten.



