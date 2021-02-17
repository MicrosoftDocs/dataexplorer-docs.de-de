---
title: Abfragegrenzwerte – Azure Data Explorer
description: In diesem Artikel werden Abfragegrenzwerte in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.localizationpriority: high
ms.openlocfilehash: a50900a5ea0f0c3d8f25e68a606572093af07432
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100359606"
---
# <a name="query-limits"></a>Abfragegrenzwerte

Kusto ist eine Ad-hoc-Abfrage-Engine, die große Datasets hostet und versucht, Abfragen zu erfüllen, indem alle relevanten Daten im Arbeitsspeicher gehalten werden.
Dabei besteht das inhärente Risiko, dass Abfragen die Dienstressourcen grenzenlos monopolisieren. Kusto bietet verschiedene integrierte Schutzmaßnahmen in Form von standardmäßigen Abfragegrenzwerten. Wenn Sie erwägen, diese Grenzwerte zu entfernen, ermitteln Sie zunächst, ob Sie dadurch tatsächlich einen Vorteil gewinnen.

## <a name="limit-on-request-concurrency"></a>Grenzwert für Anforderungsparallelität

**Anforderungsparallelität** ist ein Grenzwert, den ein Cluster für verschiedene Anforderungen auferlegt, die gleichzeitig ausgeführt werden.

* Der Standardwert des Grenzwerts hängt von der SKU ab, in der der Cluster ausgeführt wird, und wird berechnet als: `Cores-Per-Node x 10`.
  * Beispielsweise ist der Standardgrenzwert für einen Cluster, der für die D14v2-SKU eingerichtet ist, bei der jeder Computer über 16 virtuelle Kerne verfügt, `16 cores x10 = 160`.
* Der Standardwert kann geändert werden, indem Sie die [Richtlinie für die Anforderungsratenbegrenzung](../management/request-rate-limit-policy.md) der Arbeitsauslastungsgruppe `default` konfigurieren.
  * Die tatsächliche Anzahl von Anforderungen, die gleichzeitig in einem Cluster ausgeführt werden können, hängt von verschiedenen Faktoren ab. Die wichtigsten Faktoren sind Cluster-SKU, die verfügbaren Ressourcen des Clusters sowie Verwendungsmuster. Die Richtlinie kann auf Grundlage von Auslastungstests konfiguriert werden, die mit produktionsähnlichen Verwendungsmustern ausgeführt werden.

## <a name="limit-on-result-set-size-result-truncation"></a>Grenzwert für die Größe des Resultsets (Ergebniskürzung)

**Ergebniskürzung** ist ein Grenzwert, der standardmäßig für das von der Abfrage zurückgegebene Resultset festgelegt wird. Kusto begrenzt die Anzahl der an den Client zurückgegebenen Datensätze auf **500.000** und die Gesamtdatengröße für diese Datensätze auf **64 MB**. Wenn einer dieser Grenzwerte überschritten wird, tritt bei der Abfrage ein „teilweiser Abfragefehler“ auf. Das Überschreiten der Gesamtdatengröße generiert eine Ausnahme mit der folgenden Meldung:

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal data size limit 67108864 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

Das Überschreiten der Anzahl von Datensätzen schlägt mit folgender Ausnahme fehl:

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal record count limit 500000 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

Es gibt verschiedene Strategien für den Umgang mit diesem Fehler.

* Verringern Sie die Resultsetgröße, indem Sie die Abfrage so ändern, dass nur interessante Daten zurückgegeben werden. Diese Strategie ist nützlich, wenn die anfängliche fehlerhafte Abfrage zu „breit“ gefasst ist. Beispielsweise projiziert die Abfrage Datenspalten nicht weg, die nicht benötigt werden.
* Verringern Sie die Größe des Resultsets, indem Sie die Nachverarbeitung der Abfrage, z. B. Aggregationen, in die Abfrage selbst verschieben. Die Strategie ist in Szenarien nützlich, in denen die Ausgabe der Abfrage an ein anderes Verarbeitungssystem weitergeleitet wird, das dann andere Aggregationen durchführt.
* Wechseln Sie von Abfragen zur Verwendung des [Datenexports](../management/data-export/index.md), wenn Sie große Datenmengen aus dem Dienst exportieren möchten.
* Weisen Sie den Dienst an, diesen Abfragegrenzwert zu unterdrücken, indem Sie die unten aufgeführten `set`-Anweisungen oder Flags in [Eigenschaften von Clientanforderungen](../api/netfx/request-properties.md) verwenden.

Zu den Methoden zum Verringern der von der Abfrage erzeugten Resultsetgröße gehören:

* Verwenden des [summarize-Operators](../query/summarizeoperator.md) zum Gruppieren und Aggregieren ähnlicher Datensätze in der Abfrageausgabe. Potenzielle Stichprobenentnahme einiger Spalten mithilfe [beliebiger Aggregationsfunktionen](../query/any-aggfunction.md).
* Verwenden eines [take-Operators](../query/takeoperator.md) zur Entnahme von Stichproben aus der Abfrageausgabe.
* Verwenden der [substring-Funktion](../query/substringfunction.md) zum Zuschneiden breiter Freitextspalten.
* Verwenden des [project-Operators](../query/projectoperator.md), um alle uninteressanten Spalten aus dem Resultset zu löschen.

Sie können die Ergebniskürzung deaktivieren, indem Sie die Anforderungsoption `notruncation` verwenden.
Wir empfehlen, dass irgendeine Art von Grenzwert aktiviert bleibt.

Zum Beispiel:

```kusto
set notruncation;
MyTable | take 1000000
```

Es ist auch möglich, eine präzisere Kontrolle über die Ergebniskürzung auszuüben, indem Sie den Wert von `truncationmaxsize` (maximale Datengröße in Bytes, standardmäßig 64 MB) und von `truncationmaxrecords` (maximale Anzahl von Datensätzen, Standardwert 500.000) festlegen. Mit der folgenden Abfrage wird z. B. festgelegt, dass die Ergebniskürzung bei 1.105 Datensätzen oder 1 MB erfolgt, je nachdem, welcher Wert überschritten wird.

```kusto
set truncationmaxsize=1048576;
set truncationmaxrecords=1105;
MyTable | where User=="UserId1"
```

Wenn Sie den Grenzwert für die Ergebniskürzung entfernen, bedeutet dies, dass Sie beabsichtigen, Massendaten aus Kusto zu verschieben.

Sie können den Grenzwert für die Ergebniskürzung entweder für Exportzwecke mithilfe des `.export`-Befehls oder für die spätere Aggregation entfernen. Wenn Sie sich zur späteren Aggregation entschließen, sollten Sie erwägen mittels Kusto zu aggregieren.

Kusto bietet eine Reihe von Clientbibliotheken, die „unendlich große“ Ergebnisse verarbeiten können, indem sie an den Aufrufer gestreamt werden. Verwenden Sie eine dieser Bibliotheken, und konfigurieren Sie sie für den Streamingmodus. Verwenden Sie beispielsweise den .NET Framework-Client (Microsoft.Azure.Kusto.Data), und legen Sie entweder die Streaming-Eigenschaft der Verbindungszeichenfolge auf *true* fest, oder verwenden Sie den *ExecuteQueryV2Async()* -Aufruf, der Ergebnisse immer streamt.

Ergebniskürzung wird standardmäßig angewendet, nicht nur auf den Ergebnisstream, der an den Client zurückgegeben wird. Sie wird auch standardmäßig mit ähnlichen Effekten auf jede Unterabfrage angewendet, die ein Cluster an einen anderen Cluster in einer clusterübergreifenden Abfrage ausgibt.

### <a name="setting-multiple-result-truncation-properties"></a>Festlegen der Eigenschaften für das Abschneiden mehrerer Ergebnisse

Bei der Verwendung von `set`-Anweisungen und/oder bei der Angabe von Flags in [Clientanforderungseigenschaften](../api/netfx/request-properties.md) gilt Folgendes:

* Wenn `truncationmaxsize`, `truncationmaxrecords` oder `query_take_max_records` zusätzlich zu `notruncation` festgelegt ist, wird `notruncation` ignoriert.
* Wenn `truncationmaxsize`, `truncationmaxrecords` und/oder `query_take_max_records` mehrmals festgelegt sind, gilt jeweils der *niedrigere* Wert für jede Eigenschaft.

## <a name="limit-on-memory-per-iterator"></a>Grenzwert für Arbeitsspeicher pro Iterator

**Maximaler Arbeitsspeicher pro Resultsetiterator** ist ein weiterer von Kusto verwendeter Grenzwert zum Schutz vor „Endlosabfragen“. Dieser Grenzwert, der von der Anforderungsoption `maxmemoryconsumptionperiterator` dargestellt wird, legt eine obere Grenze für die Menge an Arbeitsspeicher fest, die ein einzelner Abfrageplan-Resultsetiterator belegen kann. Dieser Grenzwert gilt für die spezifischen Iteratoren, die nicht von sich aus streamen, wie `join`. Im Folgenden sehen Sie ein paar Fehlermeldungen, die zurückgegeben werden, wenn diese Situation auftritt:

```
The ClusterBy operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete E_RUNAWAY_QUERY.

The DemultiplexedResultSetCache operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The ExecuteAndCache operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The HashJoin operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The Sort operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The Summarize operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The TopNestedAggregator operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The TopNested operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).
```

Dieser Wert ist standardmäßig auf 5 GB festgelegt. Sie können diesen Wert um bis zur Hälfte des physischen Arbeitsspeichers des Computers erhöhen:

```kusto
set maxmemoryconsumptionperiterator=68719476736;
MyTable | ...
```

Verwendet die Abfrage den Operator `summarize`, `join` oder `make-series`, können Sie die Strategie [Shuffleabfrage](../query/shufflequery.md) nutzen, um die Arbeitsspeicherauslastung auf einem einzelnen Computer zu reduzieren.

In anderen Fällen können Sie Stichproben für das Dataset entnehmen, um die Überschreitung dieses Grenzwerts zu vermeiden. Die folgenden zwei Abfragen zeigen, wie die Stichprobenentnahme erfolgt. Die erste Abfrage ist eine statistische Stichprobenentnahme, bei der ein Zufallszahlengenerator verwendet wird. Die zweite Abfrage ist eine deterministische Stichprobenentnahme, die durch das Hashing einer Spalte aus dem Dataset erfolgt, in der Regel eine ID.

```kusto
T | where rand() < 0.1 | ...

T | where hash(UserId, 10) == 1 | ...
```

Wird `maxmemoryconsumptionperiterator` mehrmals festgelegt (beispielsweise in Clientanforderungseigenschaften und mithilfe einer `set`-Anweisung), gilt der niedrigere Wert.


## <a name="limit-on-memory-per-node"></a>Grenzwert für Arbeitsspeicher pro Knoten

**Maximaler Arbeitsspeicher pro Abfrage und Knoten** ist ein weiterer verwendeter Grenzwert zum Schutz vor „Endlosabfragen“. Dieser Grenzwert, der von der Anforderungsoption `max_memory_consumption_per_query_per_node` dargestellt wird, legt eine obere Grenze für die Menge an Arbeitsspeicher fest, die auf einem einzelnen Knoten für eine bestimmte Abfrage verwendet werden kann.

```kusto
set max_memory_consumption_per_query_per_node=68719476736;
MyTable | ...
```

Wird `max_memory_consumption_per_query_per_node` mehrmals festgelegt (beispielsweise in Clientanforderungseigenschaften und mithilfe einer `set`-Anweisung), gilt der niedrigere Wert.

Verwendet die Abfrage den Operator `summarize`, `join` oder `make-series`, können Sie die Strategie [Shuffleabfrage](../query/shufflequery.md) nutzen, um die Arbeitsspeicherauslastung auf einem einzelnen Computer zu reduzieren.

## <a name="limit-execution-timeout"></a>Grenzwert für Ausführungstimeout

**Servertimeout** ist ein dienstseitiges Timeout, das auf alle Anforderungen angewendet wird.
Das Timeout für ausgeführte Anforderungen (Abfragen und Steuerungsbefehle) wird an mehreren Punkten in Kusto erzwungen:

* Clientbibliothek (falls verwendet)
* Dienstendpunkt, der die Anforderung akzeptiert
* Dienst-Engine, die die Anforderung verarbeitet

Standardmäßig ist das Timeout für Abfragen auf vier Minuten und für Steuerungsbefehle auf 10 Minuten festgelegt. Dieser Wert kann bei Bedarf erhöht werden (maximal auf eine Stunde).

* Wenn Sie eine Abfrage mittels Kusto.Explorer durchführen, verwenden Sie **Tools** &gt; **Optionen*** &gt; **Verbindungen** &gt; **Abfrageservertimeout**.
* Legen Sie die Clientanforderungseigenschaft `servertimeout`, ein Wert vom Typ `System.TimeSpan`, programmgesteuert auf eine Stunde fest.

**Hinweise zu Timeouts**

* Clientseitig wird das Timeout vom Zeitpunkt der Erstellung der Anforderung bis zum Zeitpunkt, an dem die Antwort beim Client einzugehen beginnt, angewendet. Die Zeit, die das Lesen der Nutzlast auf dem Client erfordert, wird nicht als Teil des Timeouts behandelt. Sie hängt davon ab, wie schnell der Aufrufer die Daten aus dem Stream abruft.
* Ebenfalls clientseitig ist der tatsächlich verwendete Timeoutwert geringfügig höher als der vom Benutzer angeforderte Servertimeoutwert. Diese Differenz dient zum Abfangen von Netzwerkwartezeiten.
* Um automatisch das maximal zulässige Anforderungstimeout zu verwenden, legen Sie die Clientanforderungseigenschaft `norequesttimeout` auf `true` fest.

## <a name="limit-on-query-cpu-resource-usage"></a>Grenzwert für die Abfrage-CPU-Ressourcennutzung

Kusto ermöglicht Ihnen das Ausführen von Abfragen und das Verwenden von so viel CPU-Ressourcen, wie der Cluster besitzt. Es versucht, ein faires Roundrobin zwischen Abfragen durchzuführen, wenn mehr als eine ausgeführt wird. Diese Methode liefert die beste Leistung bei Ad-hoc-Abfragen.
Zu anderen Zeitpunkten können Sie die CPU-Ressourcen, die für eine bestimmte Abfrage verwendet werden, begrenzen. Wenn Sie z. B. einen „Hintergrundauftrag“ ausführen, toleriert das System möglicherweise höhere Wartezeiten, um gleichzeitigen Ad-hoc-Abfragen eine hohe Priorität einzuräumen.

Beim Ausführen einer Abfrage unterstützt Kusto die Angabe von zwei [Clientanforderungseigenschaften](../api/netfx/request-properties.md).
Die Eigenschaften sind *query_fanout_threads_percent* und *query_fanout_nodes_percent*.
Beide Eigenschaften sind ganze Zahlen, die standardmäßig den maximalen Wert (100) annehmen, jedoch für eine bestimmte Abfrage auf einen anderen Wert verringert werden können.

Die erste, *query_fanout_threads_percent*, steuert den Auffächerungsfaktor für die Threadverwendung.
Wenn diese Eigenschaft auf 100 % festgelegt wird, weist der Cluster alle CPUs auf jedem Knoten zu. Beispielsweise 16 CPUs in einem Cluster, der auf Azure D14-Knoten bereitgestellt ist.
Wenn diese Eigenschaft auf 50 % festgelegt wird, wird die Hälfte der CPUs verwendet usw.
Die Zahlen werden auf eine ganze CPU aufgerundet, sodass der Eigenschaftswert problemlos auf 0 festgelegt werden kann.

Die zweite, *query_fanout_nodes_percent*, steuert, wie viele der Abfrageknoten im Cluster pro Verteilungsvorgang für Unterabfragen verwendet werden sollen.
Sie funktioniert auf ähnliche Weise.

Wird `query_fanout_nodes_percent` oder `query_fanout_threads_percent` mehrmals festgelegt (beispielsweise in Clientanforderungseigenschaften und mithilfe einer `set`-Anweisung), gilt der niedrigere Wert für jede Eigenschaft.

## <a name="limit-on-query-complexity"></a>Grenzwert für Abfragekomplexität

Während der Abfrageausführung wird der Abfragetext in eine Struktur relationaler Operatoren transformiert, die die Abfrage darstellen.
Wenn die Strukturtiefe einen internen Schwellenwert von mehreren tausend Ebenen überschreitet, wird die Abfrage als zu komplex für die Verarbeitung betrachtet und schlägt mit einem Fehlercode fehl. Der Fehler zeigt an, dass die Struktur der relationalen Operatoren ihre Grenzwerte überschreitet.
Grenzwerte werden aufgrund von Abfragen mit langen Listen binärer Operatoren überschritten, die miteinander verkettet sind. Zum Beispiel:

```kusto
T 
| where Column == "value1" or 
        Column == "value2" or 
        .... or
        Column == "valueN"
```

Schreiben Sie in diesem Spezialfall die Abfrage mit dem [`in()`](../query/inoperator.md)-Operator neu.

```kusto
T 
| where Column in ("value1", "value2".... "valueN")
```
