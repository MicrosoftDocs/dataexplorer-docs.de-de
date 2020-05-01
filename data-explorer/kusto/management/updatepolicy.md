---
title: 'Kusto-Aktualisierungs Richtlinie für Daten, die zu einer Quelle hinzugefügt werden: Azure Daten-Explorer'
description: Dieser Artikel beschreibt die Aktualisierungs Richtlinie in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 7d2b89e0723bbecb29ffd582ae20c2ee41199e45
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618495"
---
# <a name="update-policy"></a>Aktualisieren von Richtlinien

Die Update Richtlinie wird für eine **Ziel Tabelle** festgelegt, die Kusto anweist, Daten automatisch an den Daten anzufügen, wenn neue Daten in die **Quell Tabelle**eingefügt werden. Die **Abfrage** der Update Richtlinie wird für die Daten ausgeführt, die in die Quell Tabelle eingefügt werden. Dies ermöglicht z. b. die Erstellung einer Tabelle als gefilterte Sicht einer anderen Tabelle, möglicherweise mit einem anderen Schema, einer Aufbewahrungs Richtlinie usw.

Standardmäßig wirkt sich ein Fehler beim Ausführen der Update Richtlinie nicht auf die Erfassung von Daten in die Quell Tabelle aus. Wenn die Update Richtlinie als **transaktional**definiert ist, erzwingt die Ausführung der Update Richtlinie nicht, dass die Erfassung von Daten in die Quell Tabelle ebenfalls fehlschlägt. (Vorsicht ist zu beachten, wenn dies geschieht, da einige Benutzerfehler, wie z. b. das Definieren einer falschen Abfrage in der Update Richtlinie, möglich **erweise verhindern, dass Daten** in die Quell Tabelle aufgenommen werden.) Daten, die in der Grenze von transaktionalen Update Richtlinien erfasst wurden, sind für eine Abfrage in einer einzelnen Transaktion verfügbar.

Die Abfrage der Update Richtlinie wird in einem speziellen Modus ausgeführt, in dem nur die neu erfassten Daten in der Quell Tabelle angezeigt werden. Es ist nicht möglich, die bereits erfassten Daten der Quell Tabelle als Teil dieser Abfrage abzufragen. Dies führt zu einem schnellen Auftreten von Ingestionen.

Da die Aktualisierungs Richtlinie für die Ziel Tabelle definiert ist, kann das Erfassen von Daten in eine Quell Tabelle dazu führen, dass mehrere Abfragen für diese Daten ausgeführt werden. Die Ausführungsreihenfolge der Update Richtlinien ist nicht definiert.

Angenommen, die Quell Tabelle ist eine hochwertige Ablauf Verfolgungs Tabelle mit interessanten Daten, die als frei Text Spalte formatiert sind. Angenommen, die Ziel Tabelle (für die die Update Richtlinie definiert ist) akzeptiert nur bestimmte Ablauf Verfolgungs Zeilen und ein gut strukturiertes Schema, bei dem es sich um eine Transformation der ursprünglichen frei Text Daten mithilfe des Kusto-Analyse [Operators](../query/parseoperator.md)handelt.

Die Aktualisierungs Richtlinie verhält sich ähnlich wie die reguläre Erfassung und unterliegt den gleichen Einschränkungen und bewährten Methoden. Beispielsweise wird die Größe des Clusters horizontal hochskaliert, und es ist effizienter, wenn Ingestionen in großen Buli durchgeführt werden.

## <a name="commands-that-trigger-the-update-policy"></a>Befehle, die die Aktualisierungs Richtlinie auslöst

Update Richtlinien treten in Kraft, wenn Daten erfasst oder verschoben werden (Blöcke werden in erstellt), indem eine der folgenden Befehle verwendet wird:

* [. Erfassung (Pull)](../management/data-ingestion/ingest-from-storage.md)
* [. Erfassung (Inline)](../management/data-ingestion/ingest-inline.md)
* [. Set |. Append |. Set-or-Append |. Set-or-Replace](../management/data-ingestion/ingest-from-query.md)
* [Verschiebungs Blöcke verschieben](../management/extents-commands.md#move-extents)
* [. Replace Blöcke](../management/extents-commands.md#replace-extents)

## <a name="the-update-policy-object"></a>Das Update-Richtlinien Objekt

Einer Tabelle können NULL, ein oder mehrere Update-Richtlinien Objekte zugeordnet sein.
Jedes dieser Objekte wird als JSON-Eigenschaften Behälter dargestellt, wobei die folgenden Eigenschaften definiert sind:

|Eigenschaft |type |BESCHREIBUNG  |
|---------|---------|----------------|
|isEnabled                     |`bool`  |Gibt an, ob die Update Richtlinie aktiviert (true) oder deaktiviert (false) ist.                                                                                                                               |
|`Source`                        |`string`|Name der Tabelle, in der die Aktualisier Ende Aktualisierungs Richtlinie ausgelöst wird                                                                                                                                 |
|Abfrage                         |`string`|Eine Kusto-CSL-Abfrage, mit der die Daten für das Update erzeugt werden.                                                                                                                           |
|"IsTransactional"               |`bool`  |Gibt an, ob es sich um eine transaktionale Update Richtlinie handelt (Standardwert: false). Fehler beim Ausführen einer Richtlinie für transaktionale Updates, weil die Quell Tabelle nicht ebenfalls mit neuen Daten aktualisiert wird.   |
|Propagateingestionproperties  |`bool`  |Gibt an, ob Erfassungs Eigenschaften (Block-und Erstellungszeit), die während der Erfassung in der Quell Tabelle angegeben werden, auch auf die in der abgeleiteten Tabelle festgelegten Werte angewendet werden sollen.                 |

> [!NOTE]
>
> * Die Quell Tabelle und die Tabelle, für die die Update Richtlinie definiert ist, **müssen sich in derselben Datenbank**befinden.
> * Die Abfrage enthält möglicherweise **keine** datenbankübergreifenden und Cluster übergreifenden Abfragen.
> * Die Abfrage kann gespeicherte Funktionen aufrufen.
> * Die Abfrage wird automatisch so festgelegt, dass nur die neu erfassten Datensätze abgedeckt werden.
> * Kaskadierende Updates sind zulässig (TableA--`[update]`-> TableB--`[update]`--> TableC--`[update]`->...)
> * Für den Fall, dass Update Richtlinien auf zirkuläre Weise für mehrere Tabellen definiert werden, wird diese zur Laufzeit erkannt, und die Kette der Updates wird abgeschnitten (d.h., die Daten werden nur einmal für jede Tabelle in der Kette der betroffenen Tabellen erfasst).
> * Wenn Sie im `Source` `Query` Rahmen der Richtlinie auf die Tabelle verweisen (oder in Funktionen, auf die letztere verweist), sollten Sie sicherstellen, dass Sie **nicht** den qualifizierten Namen der Tabelle verwenden `TableName` (d. h. use und **Not** `database("DatabaseName").TableName` or `cluster("ClusterName").database("DatabaseName").TableName`).
> * Die Abfrage der Update Richtlinie kann nicht auf eine Tabelle verweisen, bei der eine [Sicherheit auf Zeilenebene Richtlinie](./rowlevelsecuritypolicy.md) aktiviert ist.
> * Eine Abfrage, die als Teil einer Update Richtlinie ausgeführt wird, verfügt **nicht** über Lesezugriff auf Tabellen, für die die [restrictedviewaccess-Richtlinie](restrictedviewaccesspolicy.md) aktiviert ist.
> * `PropagateIngestionProperties`tritt nur bei Erfassungs Vorgängen in Kraft. Wenn die Update Richtlinie als Teil eines- `.move extents` oder `.replace extents` -Befehls ausgelöst wird, hat diese Option **keine** Auswirkung.
> * Wenn die Update Richtlinie als Teil eines `.set-or-replace` Befehls aufgerufen wird, ist das Standardverhalten, dass Daten in abgeleiteten Tabellen auch ersetzt werden, wie Sie in der Quell Tabelle enthalten sind.

## <a name="retention-policy-on-the-source-table"></a>Aufbewahrungs Richtlinie für die Quell Tabelle

Wenn Sie die Rohdaten in der Quell Tabelle nicht beibehalten möchten, können Sie in der [Beibehaltungs Richtlinie](retentionpolicy.md)der Quell Tabelle einen Zeitraum von 0 (null) festlegen, während die Aktualisierungs Richtlinie als transaktional festgelegt wird.

Dies führt zu folgenden Ergebnissen:
* Die Quelldaten, die nicht aus der Quell Tabelle abgefragt werden sollen.
* Die Quelldaten werden im Rahmen des Erfassungs Vorgangs nicht dauerhaft im permanenten Speicher gespeichert.
* Verbesserung der Leistung des Vorgangs.
* Verringerung der Ressourcen, die nach der Erfassung genutzt werden, für Hintergrund Bereinigungs Vorgänge, die für [Blöcke](../management/extents-overview.md) in der Quell Tabelle durchgeführt wurden.

## <a name="failures"></a>Fehler

In einigen Fällen ist die Erfassung von Daten in die Quell Tabelle erfolgreich, aber die Update Richtlinie schlägt während der Erfassung in die Ziel Tabelle fehl.

Fehler, die auftreten, während die Richtlinien aktualisiert werden, können mithilfe des [Befehls ". Show Erfassung](../management/ingestionfailures.md)Failure" abgerufen werden. gehen Sie folgendermaßen vor:
 
```kusto
.show ingestion failures 
| where FailedOn > ago(1hr) and OriginatesFromUpdatePolicy == true
```

Fehler werden wie folgt behandelt:

* **Nicht transaktionale Richtlinie**: der Fehler wird von Kusto ignoriert. Jeder Wiederholungsversuch ist verantwortlich für den Daten Besitzer.  
* **Transaktions Richtlinie**: der ursprüngliche Erfassungs Vorgang, der das Update ausgelöst hat, kann ebenfalls nicht ausgeführt werden. Die Quell Tabelle und die Datenbank werden nicht mit neuen Daten geändert.
  * Für den Fall, dass die Erfassungs Methode `pull` (der Datenverwaltung Dienst von Kusto am Erfassungsprozess beteiligt ist), gibt es einen automatisierten Wiederholungsversuch für den gesamten Erfassungs Vorgang, der durch den Datenverwaltung Dienst von Kusto gemäß der folgenden Logik orchestriert ist:
    * Wiederholungen werden ausgeführt, bis das früheste zwischen `DataImporterMaximumRetryPeriod` (Standardwert = 2 Tage) und `DataImporterMaximumRetryAttempts` (Standardwert = 10) erreicht wird.
    * Beide Einstellungen können in der Konfiguration des Datenverwaltung Dienstanbieter von kustoops geändert werden.
    * Der Backoff-Zeitraum beginnt bei 2 Minuten und wächst exponentiell (2-> 4-> 8-> 16... Minuten
  * In jedem anderen Fall ist jeder Wiederholungsversuch die Verantwortung für den Daten Besitzer.



## <a name="control-commands"></a>Steuerungsbefehle

* Verwenden Sie die [Richtlinien Aktualisierung der Tabellen Tabelle anzeigen](../management/update-policy.md#show-update-policy) , um die aktuelle Aktualisierungs Richtlinie einer Tabelle anzuzeigen.
* Legen Sie die aktuelle Aktualisierungs Richtlinie einer Tabelle mithilfe von " [. ALTER TABLE table Policy Update](../management/update-policy.md#alter-update-policy) " fest.
* Verwenden Sie das [Update ". Alter-Merge table table Policy Update](../management/update-policy.md#alter-merge-table-table-policy-update) ", um an die aktuelle Aktualisierungs Richtlinie einer Tabelle anzufügen.
* Verwenden Sie die Update-Richtlinie der [Tabellen Tabelle löschen](../management/update-policy.md#delete-table-table-policy-update) , um an die aktuelle Update Richtlinie einer Tabelle anzufügen.

## <a name="testing-an-update-policys-performance-impact"></a>Testen der Auswirkungen auf die Leistung einer Update Richtlinie

Das Definieren einer Update Richtlinie kann die Leistung eines Kusto-Clusters beeinträchtigen, da dies Auswirkungen auf die Erfassung in der Quell Tabelle hat. Es wird dringend empfohlen, den `Query` Teil der Aktualisierungs Richtlinie für eine gute Leistung zu optimieren.
Sie können die zusätzlichen Leistungseinbußen einer Update Richtlinie bei einem Erfassungs Vorgang testen, indem Sie Sie für bestimmte und bereits vorhandene Blöcke aufrufen, bevor Sie die Richtlinie und/oder eine Funktion, die Sie verwendet `Query` , erstellen oder ändern.

Bei diesem Beispiel wird Folgendes vorausgesetzt:

* Der Name der Quell Tabelle ( `Source` die-Eigenschaft der Update Richtlinie) `MySourceTable`ist.
* Die `Query` -Eigenschaft der Update Richtlinie Ruft eine Funktion mit `MyFunction()`dem Namen auf.

Mithilfe von können Sie [Abfragen anzeigen](../management/queries.md). Sie können die Ressourcenverwendung (CPU, Arbeitsspeicher usw.) der folgenden Abfrage und/oder mehrerer Ausführungen davon auswerten.

```kusto
.show table MySourceTable extents;
// The following line provides the extent ID for the not-yet-merged extent in the source table which has the most records
let extentId = $command_results | where MaxCreatedOn > ago(1hr) and MinCreatedOn == MaxCreatedOn | top 1 by RowCount desc | project ExtentId;
let MySourceTable = MySourceTable | where extent_id() == toscalar(extentId);
MyFunction()
```