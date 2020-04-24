---
title: Aktualisierungsrichtlinie - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt Update-Richtlinien in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 812a5af932f69d898bb419631fa6ea3c6bc0795e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519396"
---
# <a name="update-policy"></a>Aktualisieren von Richtlinien

Die Aktualisierungsrichtlinie wird für eine **Zieltabelle** festgelegt, die Kusto anweist, Daten automatisch an sie anzuhängen, wenn neue Daten in die **Quelltabelle**eingefügt werden. Die **Abfrage** der Aktualisierungsrichtlinie wird für die in die Quelltabelle eingefügten Daten ausgeführt. Dies ermöglicht z. B. die Erstellung einer Tabelle als gefilterte Ansicht einer anderen Tabelle, möglicherweise mit einem anderen Schema, einer anderen Aufbewahrungsrichtlinie usw.

Standardmäßig wirkt sich das Nichtausführen der Aktualisierungsrichtlinie nicht auf die Erfassung von Daten in der Quelltabelle aus. Wenn die Aktualisierungsrichtlinie als **transaktional**definiert ist, erzwingt das Fehlschlagen der Aktualisierungsrichtlinie, dass die Erfassung von Daten in der Quelltabelle ebenfalls fehlschlägt. (Pflege muss verwendet werden, wenn dies geschieht, da einige Benutzerfehler, wie das Definieren einer falschen Abfrage in der Aktualisierungsrichtlinie, **möglicherweise** verhindern, dass Daten in die Quelltabelle aufgenommen werden.) Daten, die in der "Grenze" der Transaktionsaktualisierungsrichtlinien aufgenommen werden, werden für eine Abfrage in einer einzelnen Transaktion verfügbar.

Die Abfrage der Aktualisierungsrichtlinie wird in einem speziellen Modus ausgeführt, in dem sie nur die neu aufgenommenen Daten in die Quelltabelle "sieht". Es ist nicht möglich, die bereits aufgenommenen Daten der Quelltabelle als Teil dieser Abfrage abzufragen. Dies führt schnell zu quadratischen Zeiterfassungen.

Da die Aktualisierungsrichtlinie in der Zieltabelle definiert ist, kann das Einführen von Daten in eine Quelltabelle dazu führen, dass mehrere Abfragen für diese Daten ausgeführt werden. Die Reihenfolge der Ausführung von Aktualisierungsrichtlinien ist nicht definiert.

Angenommen, die Quelltabelle ist eine Hochkursverfolgungstabelle mit interessanten Daten, die als Freitextspalte formatiert sind. Angenommen, die Zieltabelle (für die die Aktualisierungsrichtlinie definiert ist) akzeptiert nur bestimmte Ablaufverfolgungslinien und mit einem gut strukturierten Schema, das eine Transformation der ursprünglichen Freitextdaten mithilfe des [Analyseoperators](../query/parseoperator.md)von Kusto darstellt.

Die Aktualisierungsrichtlinie verhält sich ähnlich wie bei der regulären Einnahme und unterliegt denselben Einschränkungen und bewährten Methoden. Beispielsweise wird es mit der Größe des Clusters horizontal hochskaliert und arbeitet effizienter, wenn die Einnahme in großen Mengen erfolgt.

## <a name="commands-that-trigger-the-update-policy"></a>Befehle, die die Aktualisierungsrichtlinie auslösen

Aktualisierungsrichtlinien werden wirksam, wenn Daten mit einem der folgenden Befehle in eine Tabelle aufgenommen oder verschoben werden (in der Ausdehnungen erstellt werden):

* [.ingest (ziehen)](../management/data-ingestion/ingest-from-storage.md)
* [.ingest (inline)](../management/data-ingestion/ingest-inline.md)
* [.set | .append | .set-or-append | .set-or-replace](../management/data-ingestion/ingest-from-query.md)
* [.move-Ausdehnungen](../management/extents-commands.md#move-extents)
* [.ersetzen Ausdehnungen](../management/extents-commands.md#replace-extents)

## <a name="the-update-policy-object"></a>Das Aktualisierungsrichtlinienobjekt

Einer Tabelle sind möglicherweise null, ein oder mehrere Aktualisierungsrichtlinienobjekte zugeordnet.
Jedes dieser Objekte wird als JSON-Eigenschaftsbeutel dargestellt, wobei die folgenden Eigenschaften definiert sind:

|Eigenschaft |type |BESCHREIBUNG  |
|---------|---------|----------------|
|isEnabled                     |`bool`  |Zustände, wenn die Aktualisierungsrichtlinie aktiviert (true) oder deaktiviert (false) ist                                                                                                                               |
|`Source`                        |`string`|Name der Tabelle, die die Aufrufrichtlinie der Aktualisierung sausiert                                                                                                                                 |
|Abfrage                         |`string`|Eine Kusto CSL-Abfrage, die zum Erstellen der Daten für die Aktualisierung verwendet wird                                                                                                                           |
|IsTransactional               |`bool`  |Zustände, ob die Aktualisierungsrichtlinie transaktional ist oder nicht (Standardwert false). Wenn eine Transaktionsaktualisierungsrichtlinie nicht ausgeführt wird, wird die Quelltabelle nicht ebenfalls mit neuen Daten aktualisiert.   |
|PropagateIngestionProperties  |`bool`  |Zustände, in dem während der Aufnahme in die Quelltabelle angegebene Aufnahmeeigenschaften (Ausdehnungs-Tags und Erstellungszeit) auch für die in der abgeleiteten Tabelle angegebenen Eigenschaften gelten sollten.                 |

> [!NOTE]
>
> * Die Quelltabelle und die Tabelle, für die die Aktualisierungsrichtlinie definiert ist, **müssen sich in derselben Datenbank befinden.**
> * Die Abfrage darf **keine** datenbankübergreifenden Abfragen oder Clusterübergreifende Abfragen enthalten.
> * Die Abfrage kann gespeicherte Funktionen aufrufen.
> * Die Abfrage wird automatisch nur auf die neu aufgenommenen Datensätze beschränkt.
> * Kaskadierende Aktualisierungen sind zulässig`[update]`(TableA --`[update]`--> TableB`[update]`-- --> TableC -- --> ...)
> * Wenn Aktualisierungsrichtlinien über mehrere Tabellen kreisförmig definiert werden, wird dies zur Laufzeit erkannt, und die Kette der Aktualisierungen wird ausgeschnitten (d. h., Daten werden nur einmal in jede Tabelle in der Kette der betroffenen Tabellen aufgenommen).
> * Wenn Sie auf `Source` die `Query` Tabelle im Teil der Richtlinie (oder in Funktionen, auf die von der Richtlinie verwiesen wird) verweisen, stellen Sie sicher, dass Sie den qualifizierten Namen der Tabelle **nicht** verwenden (bedeutet, verwenden `TableName` Sie und **nicht** `database("DatabaseName").TableName` oder `cluster("ClusterName").database("DatabaseName").TableName`).
> * Die Abfrage der Aktualisierungsrichtlinie kann nicht auf eine Tabelle mit einer aktivierten Richtlinie für [die Zeilenebene](./rowlevelsecuritypolicy.md) verweisen.
> * Eine Abfrage, die als Teil einer Aktualisierungsrichtlinie ausgeführt wird, hat **keinen** Lesezugriff auf Tabellen, für die die [RestrictedViewAccess-Richtlinie](restrictedviewaccesspolicy.md) aktiviert ist.
> * `PropagateIngestionProperties`wird nur bei Aufnahmevorgängen wirksam. Wenn die Aktualisierungsrichtlinie als `.move extents` Teil `.replace extents` eines Befehls oder Befehls ausgelöst wird, hat diese Option **keine** Auswirkungen.
> * Wenn die Aktualisierungsrichtlinie als Teil `.set-or-replace` eines Befehls aufgerufen wird, ist das Standardverhalten, dass Daten in abgeleiteten Tabellen auch ersetzt werden, wie es in der Quelltabelle der Fall ist.

## <a name="retention-policy-on-the-source-table"></a>Aufbewahrungsrichtlinie für die Quelltabelle

Um die Rohdaten in der Quelltabelle nicht beizubehalten, können Sie in der [Aufbewahrungsrichtlinie](retentionpolicy.md)der Quelltabelle einen Soft-Delete-Zeitraum von 0 festlegen und gleichzeitig die Aktualisierungsrichtlinie als transaktional festlegen.

Dies ergibt sich aus:
* Die Quelldaten können nicht aus der Quelltabelle abgefragt werden.
* Die Quelldaten werden im Rahmen des Aufnahmevorgangs nicht auf dauerhaften Speicher gespeichert.
* Verbesserung der Leistung des Vorgangs.
* Reduzierung der ressourcennutzung nach der Einnahme, für Hintergrundoptimierungsvorgänge, die zu [Ausdehnungen](../management/extents-overview.md) in der Quelltabelle durchgeführt werden.

## <a name="failures"></a>Fehler

In einigen Fällen ist die Erfassung von Daten in der Quelltabelle erfolgreich, aber die Aktualisierungsrichtlinie schlägt während der Aufnahme in die Zieltabelle fehl.

Fehler, die beim Aktualisieren der Richtlinien aufgetreten sind, können wie folgt mit dem [Befehl .show-Einsungsfehler](../management/ingestionfailures.md)abgerufen werden:
 
```kusto
.show ingestion failures 
| where FailedOn > ago(1hr) and OriginatesFromUpdatePolicy == true
```

Fehler werden wie folgt behandelt:

* **Nicht-transaktionale Richtlinie**: Der Fehler wird von Kusto ignoriert. Jeder Wiederholungsversuch liegt in der Verantwortung des Dateneigentümers.  
* **Transaktionsrichtlinie**: Der ursprüngliche Aufnahmevorgang, der die Aktualisierung ausgelöst hat, schlägt ebenfalls fehl. Die Quelltabelle und die Datenbank werden nicht mit neuen Daten geändert.
  * Falls die Aufnahmemethode (Kustos `pull` Data Management-Dienst ist in den Aufnahmeprozess involviert) ist, gibt es einen automatisierten Wiederholungsvorgang für den gesamten Aufnahmevorgang, orchestriert durch Kustos Data Management-Dienst, gemäß der folgenden Logik:
    * Wiederholungen werden bis zum `DataImporterMaximumRetryPeriod` Erreichen der frühesten zwischen `DataImporterMaximumRetryAttempts` (Standard = 2 Tage) und (Standard = 10) durchgeführt.
    * Beide oben genannten Einstellungen können in der Konfiguration des Datenverwaltungsdienstes durch KustoOps geändert werden.
    * Die Backoff-Periode beginnt bei 2 Minuten und wächst exponentiell (2 -> 4 -> 8 -> 16 ... Minuten)
  * In jedem anderen Fall liegt jeder Wiederholungsversuch in der Verantwortung des Dateneigentümers.



## <a name="control-commands"></a>Steuerbefehle

* Verwenden Sie [.show Tabelle TABELLENrichtlinienaktualisierung,](../management/update-policy.md#show-update-policy) um die aktuelle Aktualisierungsrichtlinie einer Tabelle anzuzeigen.
* Verwenden Sie [.alter Tabelle TABLE-Richtlinienaktualisierung,](../management/update-policy.md#alter-update-policy) um die aktuelle Aktualisierungsrichtlinie einer Tabelle festzulegen.
* Verwenden Sie [.alter-merge tabelle-Richtlinienaktualisierung,](../management/update-policy.md#alter-merge-table-table-policy-update) um sie an die aktuelle Aktualisierungsrichtlinie einer Tabelle anzuhängen.
* Verwenden Sie [.delete table policy update,](../management/update-policy.md#delete-table-table-policy-update) um an die aktuelle Aktualisierungsrichtlinie einer Tabelle anzuhängen.

## <a name="testing-an-update-policys-performance-impact"></a>Testen der Auswirkungen einer Aktualisierungsrichtlinie auf die Leistung

Das Definieren einer Aktualisierungsrichtlinie kann sich auf die Leistung eines Kusto-Clusters auswirken, da er sich auf jede Aufnahme in die Quelltabelle auswirkt. Es wird dringend `Query` empfohlen, dass der Teil der Aktualisierungsrichtlinie optimiert ist, um eine gute Leistung zu erbringen.
Sie können die zusätzlichen Auswirkungen einer Aktualisierungsrichtlinie auf einen Aufnahmevorgang testen, indem Sie sie auf bestimmte und bereits vorhandene Ausdehnungen aufrufen, bevor Sie die Richtlinie und/oder eine Funktion, die sie in ihrem `Query` Teil verwendet, erstellen oder ändern.

Bei diesem Beispiel wird Folgendes vorausgesetzt:

* Der Name der `Source` Quelltabelle (eigenschaft der `MySourceTable`Aktualisierungsrichtlinie) ist .
* Die `Query` Eigenschaft der Aktualisierungsrichtlinie `MyFunction()`ruft eine Funktion mit dem Namen auf.

Mit [.show-Abfragen](../management/queries.md)können Sie die Ressourcennutzung (CPU, Arbeitsspeicher usw.) der folgenden Abfrage und/oder mehrere Ausführungen davon auswerten.

```kusto
.show table MySourceTable extents;
// The following line provides the extent ID for the not-yet-merged extent in the source table which has the most records
let extentId = $command_results | where MaxCreatedOn > ago(1hr) and MinCreatedOn == MaxCreatedOn | top 1 by RowCount desc | project ExtentId;
let MySourceTable = MySourceTable | where extent_id() == toscalar(extentId);
MyFunction()
```