---
title: 'Fortlaufender Datenexport: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt den fortlaufenden Datenexport in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/27/2020
ms.openlocfilehash: 7abcead19e0306853bc6a585a41b5b79657a6842
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617713"
---
# <a name="continuous-data-export"></a>Kontinuierlicher Datenexport

Fortlaufendes Exportieren von Daten aus Kusto in eine [externe Tabelle](../externaltables.md). Die externe Tabelle definiert das Ziel (z. b. Azure BLOB Storage) und das Schema der exportierten Daten. Die exportierten Daten werden durch eine regelmäßig durchgeführte Abfrage definiert. Die Ergebnisse werden in der externen Tabelle gespeichert. Durch den Prozess wird sichergestellt, dass alle Datensätze "genau einmal" exportiert werden (ausgenommen Dimensions Tabellen, in denen alle Datensätze in allen Ausführungen ausgewertet werden). 

Der fortlaufende Datenexport erfordert, dass Sie [eine externe Tabelle erstellen](../externaltables.md#create-or-alter-external-table) und dann [eine fortlaufende Export Definition erstellen](#create-or-alter-continuous-export) , die auf die externe Tabelle zeigt. 

> [!NOTE] 
> * Kusto unterstützt nicht das Exportieren von Verlaufs Datensätzen, die vor der Erstellung des fortlaufenden Exports erfasst wurden Verlaufs Datensätze können separat mit dem (nicht kontinuierlichen) [Export Befehl](export-data-to-an-external-table.md)exportiert werden. Weitere Informationen finden Sie unter [exportieren](#exporting-historical-data)von Verlaufs Daten. 
> * Der fortlaufende Export funktioniert nicht für Daten, die mit der streamingerfassung erfasst werden. 
> * Zurzeit kann der fortlaufende Export nicht für eine Tabelle konfiguriert werden, für die eine [Sicherheit auf Zeilenebene Richtlinie](../../management/rowlevelsecuritypolicy.md) aktiviert ist.
> * Der fortlaufende Export wird für externe Tabellen mit `impersonate` in den [Verbindungs](../../api/connection-strings/storage.md)Zeichenfolgen nicht unterstützt.
 
## <a name="notes"></a>Notizen

* Die Garantie für den "genau einmal"-Export ist *nur* für Dateien bestimmt, die im [Befehl Befehl zum Anzeigen exportierter Artefakte angezeigt](#show-continuous-export-exported-artifacts)werden. 
Der fortlaufende Export garantiert *nicht* , dass jeder Datensatz nur einmal in die externe Tabelle geschrieben wird. Wenn ein Fehler auftritt, nachdem der Export begonnen hat und einige Artefakte bereits in die externe Tabelle geschrieben wurden, _kann_ die externe Tabelle Duplikate (oder sogar beschädigte Dateien) enthalten, falls ein Schreibvorgang vor dem Abschluss abgebrochen wurde. In solchen Fällen werden Artefakte nicht aus der externen Tabelle gelöscht, sondern *nicht* im [Befehl "exportierte Artefakte anzeigen](#show-continuous-export-exported-artifacts)" angezeigt. Durch die Verwendung der exportierten `show exported artifacts command` Dateien mit wird sichergestellt, dass keine Duplikate (und natürlich keine Beschädigungen) verwendet werden.
* Der fortlaufende Export verwendet [Datenbankcursorn](../databasecursor.md), um den "genau einmal"-Export sicherzustellen. 
Die [ingestiontime-Richtlinie](../ingestiontime-policy.md) muss daher für alle Tabellen aktiviert werden, auf die in der Abfrage verwiesen wird, die im Export "genau einmal" verarbeitet werden sollen. Die Richtlinie ist für alle neu erstellten Tabellen standardmäßig aktiviert.
* Das Ausgabe Schema der Export Abfrage *muss* mit dem Schema der externen Tabelle, in die Sie exportieren, identisch sein. 
* Der fortlaufende Export unterstützt keine datenbankübergreifenden/Cluster Aufrufe.
* Der fortlaufende Export wird gemäß dem für ihn konfigurierten Zeitraum ausgeführt. Der empfohlene Wert für dieses Intervall beträgt mindestens einige Minuten, je nachdem, welche Wartezeiten Sie akzeptieren möchten. Der fortlaufende Export *ist nicht* für das ständig Streaming von Daten aus Kusto konzipiert. Sie wird in einem verteilten Modus ausgeführt, in dem alle Knoten gleichzeitig exportiert werden. Wenn der von jeder einzelnen Testlauf abgefragte Datenbereich klein ist, ist die Ausgabe des fortlaufenden Exports viele kleine Artefakte (die Zahl hängt von der Anzahl der Knoten im Cluster ab). 
* Die Anzahl der gleichzeitig ausgeführten Export Vorgänge wird durch die Datenexport Kapazität des Clusters eingeschränkt (siehe [Drosselung](../../management/capacitypolicy.md#throttling)). Wenn der Cluster nicht über genügend Kapazität verfügt, um alle kontinuierlichen Exporte zu verarbeiten, werden einige in den Rückstand gehen. 
 
* Standardmäßig wird davon ausgegangen, dass alle Tabellen, auf die in der Export Abfrage verwiesen wird, [Fakten Tabellen](../../concepts/fact-and-dimension-tables.md)sind. 
Daher sind *Sie auf den* Daten Bank Cursor beschränkt. In der Export Abfrage enthaltene Datensätze sind nur diejenigen, die seit der vorherigen Export Ausführung verknüpft sind. 
Die Export Abfrage kann [Dimensions Tabellen](../../concepts/fact-and-dimension-tables.md) enthalten, in denen *alle* Datensätze der Dimensions Tabelle in *alle* Export Abfragen eingeschlossen werden. 
    * Wenn Sie Joins zwischen Fakten-und Dimensions Tabellen beim fortlaufenden Export verwenden, beachten Sie, dass die Datensätze in der Fakten Tabelle nur einmal verarbeitet werden. wenn der Export ausgeführt wird, während die Datensätze in den Dimensions Tabellen für einige Schlüssel fehlen, werden Datensätze für die entsprechenden Schlüssel entweder übersehen oder enthalten NULL-Werte für die Dimensions Spalten in den exportierten Dateien (abhängig davon, ob die Abfrage inneren oder äußeren Join verwendet). Die forcedlatency-Eigenschaft in der Definition des fortlaufenden Exports kann für solche Fälle nützlich sein, bei denen die Fakten-und Dimensions Tabellen gleichzeitig erfasst werden (für übereinstimmende Datensätze).
    * Der fortlaufende Export von Dimensions Tabellen wird nicht unterstützt. Die Export Abfrage muss mindestens eine einzelne Fakten Tabelle enthalten.
    * Die Syntax deklariert explizit, welche Tabellen Bereichs bezogen (Fakt) sind und welche nicht Bereichs bezogen sind (Dimension). Weitere Informationen `over` finden Sie im Parameter im [Create-Befehl](#create-or-alter-continuous-export) .

* Die Anzahl der in jeder fortlaufenden Export Iterationen exportierten Dateien hängt davon ab, wie die externe Tabelle partitioniert ist. Weitere Informationen finden Sie im Abschnitt "Hinweise" unter [Exportieren in einen externen Tabellen Befehl](export-data-to-an-external-table.md) . Beachten Sie, dass jede fortlaufende Export Iterationen immer in *neue* Dateien schreibt und nie an vorhandene angefügt wird. Folglich hängt die Anzahl der exportierten Dateien auch von der Häufigkeit ab, in der der fortlaufende Export ausgeführt`intervalBetweenRuns` wird (-Parameter).

Für alle fortlaufenden Export Befehle sind [Datenbankadministrator Berechtigungen](../access-control/role-based-authorization.md)erforderlich.

## <a name="create-or-alter-continuous-export"></a>Erstellen oder Ändern von fortlaufendem Export

**Syntax:**

`.create-or-alter``continuous-export` *Continuousexportname* <br>
[ `over` `(` *T1*, *T2* `)`] <br>
`to``table` *Externaltablename* <br> [ `with` `(` *PropertyName* `=` *PropertyValue*`,`... `)`]<br>
\<| *Such*

**Eigenschaften**:

| Eigenschaft             | type     | BESCHREIBUNG   |
|----------------------|----------|---------------------------------------|
| Continuousexportname | String   | Name des fortlaufenden Exports. Der Name muss innerhalb der Datenbank eindeutig sein und wird verwendet, um den fortlaufenden Export regelmäßig auszuführen.      |
| Externaltablename    | String   | Name der [externen Tabelle](../externaltables.md) , in die exportiert werden soll.  |
| Abfrage                | String   | Die zu exportier Abfrage.  |
| Over (T1, T2)        | String   | Eine optionale durch Trennzeichen getrennte Liste von Fakten Tabellen in der Abfrage. Wenn nicht angegeben, wird davon ausgegangen, dass alle Tabellen, auf die in der Abfrage verwiesen wird, Fakten Tabellen sind. Wenn angegeben, werden Tabellen, die *nicht* in dieser Liste enthalten sind, als Dimensions Tabellen behandelt und werden nicht als Bereichs bezogen betrachtet (alle Datensätze werden an allen Exporten beteiligt). Weitere Informationen finden Sie im [Abschnitt "Hinweise](#notes) ". |
| intervalbetweaufausführungen  | Timespan | Die Zeitspanne zwischen fortlaufenden Export Ausführungen. Muss größer als 1 Minute sein.   |
| forcedlatency        | Timespan | Ein optionaler Zeitraum, um die Abfrage auf Datensätze zu beschränken, die nur vor diesem Zeitraum (relativ zur aktuellen Zeit) erfasst wurden. Dies ist hilfreich, wenn die Abfrage z. b. einige Aggregationen/Joins ausführt und Sie sicherstellen möchten, dass alle relevanten Datensätze bereits vor dem Ausführen des Exports erfasst wurden.

Zusätzlich zu den oben genannten werden alle Eigenschaften unterstützt, die im Befehl "in [externe Tabelle exportieren](export-data-to-an-external-table.md) " unterstützt werden. 

**Beispiel:**

```kusto
.create-or-alter continuous-export MyExport
over (T)
to table ExternalBlob
with
(intervalBetweenRuns=1h, 
 forcedLatency=10m, 
 sizeLimit=104857600)
<| T
```

| Name     | Externaltablename | Abfrage | Forcedlatency | Intervalbetweaufausführungen | Cursor scopedtables         | Export Properties                   |
|----------|-------------------|-------|---------------|---------------------|----------------------------|------------------------------------|
| Myexport | Externalblob      | E     | 00:10:00      | 01:00:00            | [<br>  "[' DB ']. ['] '<br>] | {<br>  "SizeLimit": 104857600<br>} |

## <a name="show-continuous-export"></a>Fortlaufenden Export anzeigen

**Syntax:**

`.show``continuous-export` *Continuousexportname*

Gibt die Eigenschaften des kontinuierlichen Exports von *continuousexportname*zurück. 

**Eigenschaften**

| Eigenschaft             | type   | BESCHREIBUNG                |
|----------------------|--------|----------------------------|
| Continuousexportname | String | Name des fortlaufenden Exports. |


`.show` `continuous-exports`

Gibt alle kontinuierlichen Exporte in der Datenbank zurück. 

**Ausgabe:**

| Output-Parameter    | type     | BESCHREIBUNG                                                             |
|---------------------|----------|-------------------------------------------------------------------------|
| Cursor scopedtables  | String   | Liste der explizit Bereichs bezogenen Tabellen (Fakten Tabellen) (JSON-serialisiert)               |
| Export Properties    | String   | Export Eigenschaften (JSON-Serialisierung)                                     |
| Exportedto          | Datetime | Der letzte Zeitpunkt, zu dem die Daten erfolgreich exportiert wurden.       |
| Externaltablename   | String   | Name der externen Tabelle                                              |
| Forcedlatency       | TimeSpan | Erzwungene Latenz (null, wenn nicht angegeben)                                   |
| Intervalbetweaufausführungen | TimeSpan | Intervall zwischen Ausführungen                                                   |
| IsDisabled          | Boolean  | True, wenn der fortlaufende Export deaktiviert ist.                               |
| IsRunning           | Boolean  | True, wenn der fortlaufende Export gerade ausgeführt wird.                      |
| Lastrauunresult       | String   | Die Ergebnisse der letzten fortlaufenden Export Laufzeit (`Completed` oder) `Failed` |
| Lastrauuntime         | Datetime | Der Zeitpunkt der letzten Ausführung des fortlaufenden Exports (Startzeit)           |
| Name                | String   | Name des fortlaufenden Exports                                           |
| Abfrage               | String   | Exportieren einer Abfrage                                                            |
| Startcursor         | String   | Startpunkt der ersten Ausführung dieses fortlaufenden Exports         |

## <a name="show-continuous-export-exported-artifacts"></a>Fortlaufende exportierte Artefakte anzeigen

**Syntax:**

`.show``continuous-export` *Continuousexportname*`exported-artifacts`

Gibt alle Artefakte zurück, die vom fortlaufenden Export in allen Ausführungen exportiert werden. Es wird empfohlen, die Ergebnisse nach der timestamp-Spalte im Befehl zu filtern, um nur die relevanten Datensätze anzuzeigen. Der Verlauf der exportierten Artefakte wird 14 Tage lang aufbewahrt. 

**Eigenschaften**

| Eigenschaft             | type   | BESCHREIBUNG                |
|----------------------|--------|----------------------------|
| Continuousexportname | String | Name des fortlaufenden Exports. |

**Ausgabe:**

| Output-Parameter  | type     | BESCHREIBUNG                            |
|-------------------|----------|----------------------------------------|
| Timestamp         | Datetime | Zeitstempel der fortlaufenden Export Laufzeit |
| Externaltablename | String   | Name der externen Tabelle             |
| `Path`              | String   | Ausgabepfad                            |
| Numrecords        | long     | Anzahl der in den Pfad exportierten Datensätze     |

**Beispiel:** 

```kusto
.show continuous-export MyExport exported-artifacts | where Timestamp > ago(1h)
```

| Timestamp                   | Externaltablename | `Path`             | Numrecords | SizeInBytes |
|-----------------------------|-------------------|------------------|------------|-------------|
| 2018-12-20 07:31:30.2634216 | Externalblob      | `http://storageaccount.blob.core.windows.net/container1/1_6ca073fd4c8740ec9a2f574eaa98f579.csv` | 10                          | 1024              |

## <a name="show-continuous-export-failures"></a>Fortlaufende Export Fehler anzeigen

**Syntax:**

`.show``continuous-export` *Continuousexportname*`failures`

Gibt alle Fehler zurück, die als Teil des fortlaufenden Exports protokolliert wurden. Filtern Sie die Ergebnisse nach der timestamp-Spalte im Befehl, um nur den gewünschten Zeitbereich anzuzeigen. 

**Eigenschaften**

| Eigenschaft             | type   | BESCHREIBUNG                |
|----------------------|--------|----------------------------|
| Continuousexportname | String | Name des fortlaufenden Exports  |

**Ausgabe:**

| Output-Parameter | type      | BESCHREIBUNG                                         |
|------------------|-----------|-----------------------------------------------------|
| Timestamp        | Datetime  | Zeitstempel des Fehlers.                           |
| OperationId      | String    | Vorgangs-ID des Fehlers.                    |
| Name             | String    | Fortlaufender Export Name.                             |
| Lastsuccess Run   | Timestamp | Die letzte erfolgreiche Ausführung des fortlaufenden Exports.   |
| Failurekind      | String    | Fehler/partialfailure. Partialfailure gibt an, dass einige Artefakte erfolgreich exportiert wurden, bevor der Fehler aufgetreten ist. |
| Details          | String    | Fehlerdetails.                              |

**Beispiel:** 

```kusto
.show continuous-export MyExport failures 
```

| Timestamp                   | OperationId                          | Name     | Lastsuccess Run              | Failurekind | Details    |
|-----------------------------|--------------------------------------|----------|-----------------------------|-------------|------------|
| 2019-01-01 11:07:41.1887304 | ec641435-2505-4532-ba19-d6ab88c96a9d | Myexport | 2019-01-01 11:06:35.6308140 | Fehler     | Details... |

## <a name="drop-continuous-export"></a>Fortlaufenden Export löschen

**Syntax:**

`.drop``continuous-export` *Continuousexportname*

**Eigenschaften**

| Eigenschaft             | type   | BESCHREIBUNG                |
|----------------------|--------|----------------------------|
| Continuousexportname | String | Name des fortlaufenden Exports |

**Ausgabe:**

Die verbleibenden kontinuierlichen Exporte in der Datenbank (nach dem Löschen). Ausgabe Schema wie im [Befehl Befehl zum fortlaufenden Export anzeigen](#show-continuous-export).

## <a name="disable-or-enable-continuous-export"></a>Deaktivieren oder Aktivieren von fortlaufendem Export

**Syntax:**

`.enable``continuous-export` *Continuousexportname* 

`.disable``continuous-export` *Continuousexportname* 

Sie können den fortlaufenden Exportauftrag deaktivieren oder aktivieren. Ein deaktivierter fortlaufender Export wird nicht ausgeführt, aber der aktuelle Zustand wird persistent gespeichert und kann fortgesetzt werden, wenn der fortlaufende Export aktiviert ist. Wenn Sie einen fortlaufenden Export aktivieren, der für einen längeren Zeitraum deaktiviert wurde, wird der Export Vorgang fortgesetzt, wo er zuletzt beendet wurde. Dies führt möglicherweise zu einem Export mit langer Ausführungszeit, der das Ausführen anderer Exporte blockiert, wenn nicht genügend Cluster Kapazität vorhanden ist, um alle Prozesse zu verarbeiten. Fortlaufende Exporte werden von der letzten Laufzeit in aufsteigender Reihenfolge ausgeführt (der älteste Export wird zuerst ausgeführt, bis der catch-Vorgang abgeschlossen ist). 

**Eigenschaften**

| Eigenschaft             | type   | BESCHREIBUNG                |
|----------------------|--------|----------------------------|
| Continuousexportname | String | Name des fortlaufenden Exports |

**Ausgabe:**

Das Ergebnis des [Befehls zum Anzeigen des fortlaufenden Exports](#show-continuous-export) des geänderten fortlaufenden Exports. 




## <a name="exporting-historical-data"></a>Exportieren von Verlaufs Daten

Beim fortlaufenden Export wird der Export von Daten nur ab dem Zeitpunkt der Erstellung gestartet. Datensätze, die vor diesem Zeitpunkt erfasst wurden, sollten separat mit dem (nicht kontinuierlichen) [Export Befehl](export-data-to-an-external-table.md)exportiert werden. Um Duplikate mit vom fortlaufenden Export exportierten Daten zu vermeiden, verwenden Sie den vom [Befehl Show Continuous Export](#show-continuous-export) zurückgegebenen startcursor, und exportieren Sie nur Datensätze, bei denen der Cursor Wert cursor_before_or_at. Betrachten Sie das folgende Beispiel. Verlaufs Daten sind möglicherweise zu groß, um in einem einzelnen Export Befehl exportiert zu werden. Partitionieren Sie die Abfrage daher in mehrere kleinere Batches. 

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
