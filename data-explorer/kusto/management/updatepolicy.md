---
title: 'Kusto-Aktualisierungs Richtlinie für Daten, die zu einer Quelle hinzugefügt werden: Azure Daten-Explorer'
description: Dieser Artikel beschreibt die Aktualisierungs Richtlinie in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/04/2020
ms.openlocfilehash: 9b2d35c796cfd1f41dc2fd8e9385a4c446000b86
ms.sourcegitcommit: ed902a5a781e24e081cd85910ed15cd468a0db1e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88072445"
---
# <a name="update-policy-overview"></a>Übersicht über Update Richtlinien

Die [Update Richtlinie](update-policy.md) weist Kusto an, automatisch Daten an eine Ziel Tabelle anzufügen, wenn neue Daten in die Quell Tabelle eingefügt werden, basierend auf einer Transformations Abfrage, die für die in die Quell Tabelle eingefügten Daten ausgeführt wird.

:::image type="content" source="images/updatepolicy/update-policy-overview.png" alt-text="Übersicht über die Update Richtlinie in Azure Daten-Explorer":::

Die Richtlinie ermöglicht beispielsweise das Erstellen einer Tabelle als gefilterte Sicht einer anderen Tabelle. Die neue Tabelle kann über ein anderes Schema, eine andere Beibehaltungs Richtlinie usw. verfügen. 

Die Update Richtlinie unterliegt den gleichen Einschränkungen und bewährten Methoden wie die reguläre Erfassung. Die Richtlinie wird mit der Größe des Clusters horizontal hochskaliert und arbeitet effizienter, wenn Ingestionen in großen Buli durchgeführt werden.

> [!NOTE]
> Die Quell Tabelle und die Tabelle, für die die Update Richtlinie definiert ist, müssen sich in derselben Datenbank befinden.
> Das Schema der Update Richtlinien Funktion und das Ziel Tabellen Schema müssen in ihren Spaltennamen, Typen und Reihenfolge entsprechen.

## <a name="update-policys-query"></a>Aktualisieren der Richtlinien Abfrage

Die Abfrage für die Update Richtlinie wird in einem speziellen Modus ausgeführt, in dem Sie automatisch so festgelegt wird, dass nur die neu erfassten Datensätze abgedeckt werden, und Sie können die bereits erfassten Daten der Quell Tabelle nicht als Teil dieser Abfrage Abfragen. Daten, die in der Grenze von transaktionalen Update Richtlinien erfasst wurden, werden jedoch für eine Abfrage in einer einzelnen Transaktion verfügbar. Da die Aktualisierungs Richtlinie für die Ziel Tabelle definiert ist, kann das Erfassen von Daten in eine Quell Tabelle dazu führen, dass mehrere Abfragen für diese Daten ausgeführt werden. Die Ausführungsreihenfolge mehrerer Update Richtlinien ist nicht definiert. 

### <a name="query-limitations"></a>Abfrageeinschränkungen 

* Die Abfrage kann gespeicherte Funktionen aufrufen, aber keine datenbankübergreifenden oder Cluster übergreifenden Abfragen einschließen. 
* Eine Abfrage, die als Teil einer Update Richtlinie ausgeführt wird, verfügt nicht über Lesezugriff auf Tabellen, für die die [restrictedviewaccess-Richtlinie](restrictedviewaccesspolicy.md) aktiviert ist, oder wenn eine [Sicherheit auf Zeilenebene Richtlinie](rowlevelsecuritypolicy.md) aktiviert ist.
* Beim Verweisen auf die `Source` Tabelle im `Query` Rahmen der Richtlinie oder in Funktionen, auf die vom Teil verwiesen wird `Query` :
   * Verwenden Sie nicht den qualifizierten Namen der Tabelle. Verwenden Sie stattdessen `TableName`. 
   * Verwenden Sie nicht `database("DatabaseName").TableName` oder `cluster("ClusterName").database("DatabaseName").TableName` .

> [!WARNING]
> Wenn Sie eine falsche Abfrage in der Update Richtlinie definieren, kann dies verhindern, dass Daten in die Quell Tabelle aufgenommen werden.

## <a name="the-update-policy-object"></a>Das Update-Richtlinien Objekt

Einer Tabelle können NULL, ein oder mehrere Update-Richtlinien Objekte zugeordnet sein.
Jedes dieser Objekte wird als JSON-Eigenschaften Behälter dargestellt, wobei die folgenden Eigenschaften definiert sind.

|Eigenschaft |Typ |BESCHREIBUNG  |
|---------|---------|----------------|
|isEnabled                     |`bool`  |Gibt an, ob die Update Richtlinie aktiviert (true) oder deaktiviert (false) ist.                                                                                                                               |
|`Source`                        |`string`|Name der Tabelle, in der die Aktualisier Ende Aktualisierungs Richtlinie ausgelöst wird                                                                                                                                 |
|Abfrage                         |`string`|Eine Kusto-CSL-Abfrage, mit der die Daten für das Update erzeugt werden.                                                                                                                           |
|"IsTransactional"               |`bool`  |Gibt an, ob es sich um eine transaktionale Update Richtlinie handelt (Standardwert: false). Fehler beim Ausführen einer Richtlinie für transaktionales Update führt dazu, dass die Quell Tabelle nicht mit neuen Daten aktualisiert wird.   |
|Propagateingestionproperties  |`bool`  |Gibt an, ob Erfassungs Eigenschaften (Block-und Erstellungszeit), die während der Erfassung in der Quell Tabelle angegeben werden, auch auf die in der abgeleiteten Tabelle festgelegten Werte angewendet werden sollen.                 |

> [!NOTE]
> Kaskadierende Updates sind zulässig ( `TableA` → `TableB` → `TableC` →...).
>
> Wenn jedoch Update Richtlinien auf zirkuläre Weise für mehrere Tabellen definiert werden, wird die Kette der Updates abgeschnitten. Dieses Problem wurde zur Laufzeit erkannt. Die Daten werden nur einmal für jede Tabelle in der Kette der betroffenen Tabellen erfasst.

## <a name="update-policy-commands"></a>Aktualisieren von Richtlinien Befehlen

Folgende Befehle sind zum Steuern der Update Richtlinie zu berücksichtigen:

* [. Anzeigen der Tabelle *TableName* Richtlinien Aktualisierung](update-policy.md#show-update-policy) zeigt die aktuelle Aktualisierungs Richtlinie einer Tabelle an.
* [. Alter Table *TableName* Policy Update](update-policy.md#alter-update-policy) legt die aktuelle Aktualisierungs Richtlinie einer Tabelle fest.
* [. Alter-Merge Table *TableName* Policy Update](update-policy.md#alter-merge-table-tablename-policy-update) fügt an die aktuelle Aktualisierungs Richtlinie einer Tabelle an.
* [. Delete Table *TableName* Policy Update](update-policy.md#delete-table-tablename-policy-update) fügt an die aktuelle Aktualisierungs Richtlinie einer Tabelle an.

## <a name="update-policy-is-initiated-following-ingestion"></a>Die Aktualisierungs Richtlinie wird nach der Erfassung initiiert.

Update Richtlinien treten beim Erfassen oder Verschieben von Daten in eine definierte Quell Tabelle (Blöcke werden in erstellt) mithilfe eines der folgenden Befehle in Kraft:

* [. Erfassung (Pull)](../management/data-ingestion/ingest-from-storage.md)
* [. Erfassung (Inline)](../management/data-ingestion/ingest-inline.md)
* [. Set |. Append |. Set-or-Append |. Set-or-Replace](../management/data-ingestion/ingest-from-query.md)
  * Wenn die Update Richtlinie als Teil eines Befehls aufgerufen wird `.set-or-replace` , ist das Standardverhalten, dass Daten in abgeleiteten Tabellen wie in der Quell Tabelle ersetzt werden.
* [.move extents](../management/extents-commands.md#move-extents)
* [.replace extents](../management/extents-commands.md#replace-extents)
  * Der `PropagateIngestionProperties` Befehl wird nur bei Erfassungs Vorgängen wirksam. Wenn die Update Richtlinie als Teil eines-oder- `.move extents` Befehls ausgelöst wird `.replace extents` , hat diese Option keine Auswirkung.

## <a name="regular-ingestion-using-update-policy"></a>Reguläre Erfassung mit Update Richtlinie

Die Aktualisierungs Richtlinie verhält sich wie die reguläre Erfassung, wenn die folgenden Bedingungen erfüllt sind:

* Die Quell Tabelle ist eine stark frequtige Ablauf Verfolgungs Tabelle mit interessanten Daten, die als frei Text Spalte formatiert sind. 
* Die Ziel Tabelle, für die die Update Richtlinie definiert ist, akzeptiert nur bestimmte Ablauf Verfolgungs Zeilen.
* Die Tabelle verfügt über ein gut strukturiertes Schema, bei dem es sich um eine Transformation der ursprünglichen, vom Analyse [Operator](../query/parseoperator.md)erstellten frei Text Daten handelt.

## <a name="zero-retention-on-source-table"></a>Keine Beibehaltung der Quell Tabelle

In manchen Fällen werden Daten in einer Quell Tabelle nur als Schritt Stein für die Ziel Tabelle erfasst, und Sie möchten die Rohdaten nicht in der Quell Tabelle aufbewahren. Legen Sie in der [Beibehaltungs Richtlinie](retentionpolicy.md)der Quell Tabelle einen vorläufigen Lösch Zeitraum von 0 fest, und legen Sie die Aktualisierungs Richtlinie als transaktional fest. In dieser Situation: 

* Die Quelldaten sind nicht in der Quell Tabelle abfragbar. 
* Die Quelldaten werden im Rahmen des Erfassungs Vorgangs nicht dauerhaft im permanenten Speicher gespeichert. 
* Die Betriebsleistung wird verbessert. 
* Die nach Erfassung von Ressourcen für Hintergrund Bereinigungs Vorgänge werden verringert. Diese Vorgänge werden für [Blöcke](../management/extents-overview.md) in der Quell Tabelle durchgeführt.

## <a name="performance-impact"></a>Auswirkungen auf die Leistung

Update Richtlinien können sich auf die Leistung eines Kusto-Clusters auswirken. Die Update Richtlinie wirkt sich auf alle Erfassungsdaten in die Quell Tabelle aus. Die Erfassung einer Reihe von Datenblöcken wird mit der Anzahl der Ziel Tabellen multipliziert. Daher ist es wichtig, dass der `Query` Teil der Aktualisierungs Richtlinie für eine gute Funktion optimiert ist. Sie können die zusätzlichen Auswirkungen einer Update Richtlinie auf einen Erfassungs Vorgang testen. Rufen Sie die Richtlinie für bestimmte und bereits vorhandene Blöcke auf, bevor Sie die Richtlinie oder Funktion, die Sie verwendet, erstellen oder ändern `Query` .

### <a name="evaluate-resource-usage"></a>Ressourcenverwendung auswerten

Verwenden Sie [. Show Queries](../management/queries.md), um die Ressourcenverwendung (CPU, Arbeitsspeicher usw.) im folgenden Szenario auszuwerten:
* Der Name der Quell Tabelle (die- `Source` Eigenschaft der Update Richtlinie) ist `MySourceTable` .
* Die- `Query` Eigenschaft der Update Richtlinie Ruft eine Funktion mit dem Namen auf `MyFunction()` .

```kusto
.show table MySourceTable extents;
// The following line provides the extent ID for the not-yet-merged extent in the source table which has the most records
let extentId = $command_results | where MaxCreatedOn > ago(1hr) and MinCreatedOn == MaxCreatedOn | top 1 by RowCount desc | project ExtentId;
let MySourceTable = MySourceTable | where extent_id() == toscalar(extentId);
MyFunction()
```

## <a name="failures"></a>Fehler

Standardmäßig wirkt sich ein Fehler beim Ausführen der Update Richtlinie nicht auf die Erfassung von Daten in die Quell Tabelle aus. Wenn die Update Richtlinie jedoch als `IsTransactional` : true definiert ist, erzwingt die fehlerhafte Ausführung der Richtlinie, dass die Erfassung von Daten in die Quell Tabelle fehlschlägt. In einigen Fällen ist die Erfassung von Daten in die Quell Tabelle erfolgreich, aber die Update Richtlinie schlägt während der Erfassung in die Ziel Tabelle fehl.

Fehler, die auftreten, während die Richtlinien aktualisiert werden, können mit dem [Befehl ". Show](../management/ingestionfailures.md)Failure Failure" abgerufen werden.
 
```kusto
.show ingestion failures 
| where FailedOn > ago(1hr) and OriginatesFromUpdatePolicy == true
```

### <a name="treatment-of-failures"></a>Behandlung von Fehlern

#### <a name="non-transactional-policy"></a>Nicht transaktionale Richtlinie 

Der Fehler wird von Kusto ignoriert. Jeder Wiederholungsversuch liegt in der Verantwortung des Besitzers des Datenerfassungs Prozesses.  

#### <a name="transactional-policy"></a>Transaktionale Richtlinie

Der ursprüngliche Erfassungs Vorgang, der das Update ausgelöst hat, kann ebenfalls nicht ausgeführt werden. Die Quell Tabelle und die Datenbank werden nicht mit neuen Daten geändert.
Wenn die Erfassungs Methode `pull` (der Datenverwaltung Dienst von Kusto am Erfassungsprozess beteiligt ist) ist, gibt es einen automatisierten Wiederholungsversuch für den gesamten Erfassungs Vorgang, der vom Datenverwaltung-Dienst von Kusto gemäß der folgenden Logik orchestriert wird:
* Wiederholungen werden bis zum frühesten zwischen `DataImporterMaximumRetryPeriod` (Standardwert = 2 Tage) und `DataImporterMaximumRetryAttempts` (Standardwert = 10) erreicht.
* Beide oben aufgeführten Einstellungen können in der Konfiguration des Datenverwaltung Dienstanbieter geändert werden.
* Der Backoff-Zeitraum beginnt um 2 Minuten und wächst exponentiell (2-> 4-> 8-> 16... Minuten

In jedem anderen Fall ist jeder Wiederholungsversuch die Verantwortung für den Daten Besitzer.
