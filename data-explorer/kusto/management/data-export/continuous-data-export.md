---
title: 'Fortlaufender Datenexport: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt den fortlaufenden Datenexport in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: be16f33c649640ef92ed971665d4c7610c5501bf
ms.sourcegitcommit: c815c6ccf33864e21e1d3daff26a4f077dff88f7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95012200"
---
# <a name="continuous-data-export-overview"></a>Übersicht über den kontinuierlichen Datenexport

In diesem Artikel wird der fortlaufende Export von Daten aus Kusto in eine [externe Tabelle](../external-table-commands.md) mit einer regelmäßig laufenden Abfrage beschrieben. Die Ergebnisse werden in der externen Tabelle gespeichert, die das Ziel definiert, z. b. Azure BLOB Storage, und das Schema der exportierten Daten. Durch diesen Vorgang wird sichergestellt, dass alle Datensätze mit einigen [Ausnahmen](#exactly-once-export)"genau einmal" exportiert werden. 

Erstellen Sie zum Aktivieren des fortlaufenden Datenexports [eine externe Tabelle](../external-tables-azurestorage-azuredatalake.md#create-or-alter-external-table) , und erstellen Sie dann [eine Definition für fortlaufenden Export](create-alter-continuous.md) , die auf die externe Tabelle zeigt. 

> [!NOTE]
> Für alle fortlaufenden Export Befehle sind [Datenbankadministrator Berechtigungen](../access-control/role-based-authorization.md)erforderlich.

## <a name="continuous-export-guidelines"></a>Fortlaufende Exportrichtlinien

* **Ausgabe Schema**:
  * Das Ausgabe Schema der Export Abfrage muss mit dem Schema der externen Tabelle, in die Sie exportieren, identisch sein. 
* **Häufigkeit**:
  * Der fortlaufende Export wird gemäß dem für ihn konfigurierten Zeitraum in der- `intervalBetweenRuns` Eigenschaft ausgeführt. Der empfohlene Wert für dieses Intervall beträgt mindestens einige Minuten, je nachdem, welche Wartezeiten Sie akzeptieren möchten. Das Zeitintervall kann so niedrig wie eine Minute sein, wenn die Erfassungs Rate hoch ist.
* **Verteilung**:
  * Die Standardverteilung im fortlaufenden Export ist `per_node` , wobei alle Knoten gleichzeitig exportiert werden. 
  * Diese Einstellung kann in den Eigenschaften des Befehls "Continuous Export Create" überschrieben werden. Verwenden `per_shard` Sie die Verteilung, um die Parallelität zu erhöhen.
    > [!NOTE]
    > Diese Verteilung erhöht die Last für das Speicherkonto und hat die Möglichkeit, einschränungs Limits zu erreichen. 
  * Verwenden `single` Sie (oder `distributed` = `false` ), um die Verteilung vollständig zu deaktivieren. Mit dieser Einstellung kann der fortlaufende Export Vorgang erheblich verlangsamt und die Anzahl der Dateien, die in jeder fortlaufenden Export Iterationen erstellt werden, beeinträchtigt werden. 
* **Anzahl der Dateien**:
  * Die Anzahl der in jeder fortlaufenden Export Iterationen exportierten Dateien hängt davon ab, wie die externe Tabelle partitioniert ist. Weitere Informationen finden Sie unter [Exportieren in einen externen Tabellen Befehl](export-data-to-an-external-table.md#number-of-files). Jede fortlaufende Export Iterationen schreibt immer in neue Dateien und fügt Sie niemals an vorhandene an. Folglich hängt die Anzahl der exportierten Dateien auch von der Häufigkeit ab, mit der der fortlaufende Export ausgeführt wird. Der Frequency-Parameter ist `intervalBetweenRuns` .
* **Externe Speicher Konten für Tabellen**:
  * Für eine optimale Leistung sollten der Azure Daten-Explorer-Cluster und die Speicher Konten in derselben Azure-Region zusammengestellt werden.
  * Wenn das exportierte Daten Volume groß ist, empfiehlt es sich, mehrere Speicher Konten für die externe Tabelle zu konfigurieren, um eine Speicher Drosselung zu vermeiden. Weitere Informationen finden Sie unter [Speicherfehler während der Export Befehle](export-data-to-storage.md#failures-during-export-commands) .

## <a name="exactly-once-export"></a>Genau einmal exportieren

Um den "genau einmal"-Export sicherzustellen, verwendet der fortlaufende Export [Daten Bank Cursor](../databasecursor.md). Die [ingestiontime-Richtlinie](../ingestiontime-policy.md) muss für alle Tabellen aktiviert werden, auf die in der Abfrage verwiesen wird, die im Export "genau einmal" verarbeitet werden sollen. Die Richtlinie ist für alle neu erstellten Tabellen standardmäßig aktiviert.

Die Garantie für den "genau einmal"-Export ist nur für Dateien bestimmt, die im [Befehl zum Anzeigen exportierter Artefakte angezeigt](show-continuous-artifacts.md)werden. Der fortlaufende Export gewährleistet nicht, dass jeder Datensatz nur einmal in die externe Tabelle geschrieben wird. Wenn ein Fehler auftritt, nachdem der Export begonnen hat und einige Artefakte bereits in die externe Tabelle geschrieben wurden, kann die externe Tabelle Duplikate enthalten. Wenn ein Schreibvorgang vor dem Abschluss abgebrochen wurde, enthält die externe Tabelle möglicherweise beschädigte Dateien. In solchen Fällen werden Artefakte nicht aus der externen Tabelle gelöscht, sondern nicht im [Befehl "exportierte Artefakte anzeigen](show-continuous-artifacts.md)" angezeigt. Wenn Sie die exportierten Dateien mithilfe von verarbeiten, werden `show exported artifacts command` keine Duplikate und keine Beschädigungen sichergestellt.

## <a name="export-from-fact-and-dimension-tables"></a>Exportieren aus Fakten-und Dimensions Tabellen

Standardmäßig wird davon ausgegangen, dass alle Tabellen, auf die in der Export Abfrage verwiesen wird, [Fakten Tabellen](../../concepts/fact-and-dimension-tables.md)sind. Daher sind Sie auf den Daten Bank Cursor beschränkt. Die Syntax deklariert explizit, welche Tabellen Bereichs bezogen (Fakt) sind und welche nicht Bereichs bezogen sind (Dimension). Weitere Informationen finden Sie `over` im Parameter im [Create-Befehl](create-alter-continuous.md) .

Die Export Abfrage enthält nur die Datensätze, die seit der vorherigen Export Ausführung verknüpft sind. Die Export Abfrage kann [Dimensions Tabellen](../../concepts/fact-and-dimension-tables.md) enthalten, in denen alle Datensätze der Dimensions Tabelle in alle Export Abfragen eingeschlossen werden. Wenn Sie Joins zwischen Fakten-und Dimensions Tabellen beim fortlaufenden Export verwenden, denken Sie daran, dass die Datensätze in der Fakten Tabelle nur einmal verarbeitet werden. Wenn der Export ausgeführt wird, während die Datensätze in den Dimensions Tabellen für einige Schlüssel fehlen, werden Datensätze für die entsprechenden Schlüssel entweder übersehen oder enthalten NULL-Werte für die Dimensions Spalten in den exportierten Dateien. Das Zurückgeben von fehlenden oder NULL-Datensätzen hängt davon ab, ob die Abfrage inneren oder äußeren Join verwendet. `forcedLatency`In solchen Fällen kann die-Eigenschaft in der Definition des fortlaufenden Exports nützlich sein, in der die Fakten-und Dimensions Tabellen während der gleichen Zeit für übereinstimmende Datensätze erfasst werden.

> [!NOTE]
> Der fortlaufende Export von Dimensions Tabellen wird nicht unterstützt. Die Export Abfrage muss mindestens eine einzelne Fakten Tabelle enthalten.

## <a name="exporting-historical-data"></a>Exportieren von Verlaufs Daten

Beim fortlaufenden Export wird der Export von Daten nur ab dem Zeitpunkt der Erstellung gestartet. Datensätze, die vor diesem Zeitpunkt erfasst wurden, sollten separat mithilfe des Befehls nicht fortlaufender [Export](export-data-to-an-external-table.md)exportiert werden. 

Verlaufs Daten sind möglicherweise zu groß, um in einem einzelnen Export Befehl exportiert zu werden. Partitionieren Sie die Abfrage ggf. in mehrere kleinere Batches. 

Um Duplikate mit Daten zu vermeiden, die durch den fortlaufenden Export exportiert werden, wird `StartCursor` durch den [Befehl Befehl zum fortlaufenden Export anzeigen](show-continuous-export.md) und Exportieren nur `where cursor_before_or_at` der Cursor Wert aufgezeichnet. Beispiel:

```kusto
.show continuous-export MyExport | project StartCursor
```

| Startcursor        |
|--------------------|
| 636751928823156645 |

Gefolgt von: 

```kusto
.export async to table ExternalBlob
<| T | where cursor_before_or_at("636751928823156645")
```

## <a name="resource-consumption"></a>Ressourcenverbrauch

* Die Auswirkung des fortlaufenden Exports auf den Cluster hängt von der Abfrage ab, die der fortlaufende Export ausgeführt wird. Die meisten Ressourcen, z. b. CPU und Arbeitsspeicher, werden von der Abfrage Ausführung genutzt. 
* Die Anzahl von Export Vorgängen, die gleichzeitig ausgeführt werden können, wird durch die Datenexport Kapazität des Clusters beschränkt. Weitere Informationen finden Sie unter [Drosselung](../../management/capacitypolicy.md#throttling). Wenn der Cluster nicht über genügend Kapazität verfügt, um alle kontinuierlichen Exporte zu verarbeiten, werden einige in den Rückstand gehen.
* Der [Befehl Befehle anzeigen-und-Queries](../commands-and-queries.md) kann verwendet werden, um den Ressourcenverbrauch zu schätzen. 
  * Filtern `| where ClientActivityId startswith "RunContinuousExports"` Sie nach, um die dem fortlaufenden Export zugeordneten Befehle und Abfragen anzuzeigen.

## <a name="limitations"></a>Einschränkungen

* Der fortlaufende Export funktioniert nicht für Daten, die mit der streamingerfassung erfasst werden. 
* Der fortlaufende Export kann nicht für eine Tabelle konfiguriert werden, für die eine [Sicherheit auf Zeilenebene-Richtlinie](../../management/rowlevelsecuritypolicy.md) aktiviert ist.
* Der fortlaufende Export wird für externe Tabellen mit `impersonate` in den [Verbindungs](../../api/connection-strings/storage.md)Zeichenfolgen nicht unterstützt.
* Der fortlaufende Export unterstützt keine datenbankübergreifenden und Cluster übergreifenden Aufrufe.
* Der fortlaufende Export ist nicht für das ständig Streaming von Daten aus Azure Daten-Explorer konzipiert. Der fortlaufende Export wird in einem verteilten Modus ausgeführt, in dem alle Knoten gleichzeitig exportiert werden. Wenn der Datenbereich, der durch die einzelnen Testlauf abgefragt wird, gering ist, wäre die Ausgabe des fortlaufenden Exports viele kleine Artefakte. Die Anzahl der Artefakte hängt von der Anzahl der Knoten im Cluster ab.
* Wenn die vom fortlaufenden Export verwendeten Artefakte Event Grid Benachrichtigungen auslöst, finden Sie weitere Informationen [im Abschnitt "bekannte Probleme" in der Event Grid-Dokumentation](../../../ingest-data-event-grid-overview.md#known-event-grid-issues).
