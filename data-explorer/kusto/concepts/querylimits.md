---
title: 'Abfrage Limits: Azure Daten-Explorer'
description: In diesem Artikel werden die Abfrage Limits in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: d0f815cd523e0e53111e791d8faaaf6c37c7bb7b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252846"
---
# <a name="query-limits"></a>Abfragegrenzwerte

Kusto ist ein Ad-hoc-Abfrage Modul, das große Datasets hostet und versucht, Abfragen zu erfüllen, indem alle relevanten Daten im Arbeitsspeicher gehalten werden.
Es gibt ein inhärentes Risiko, dass Abfragen die Dienst Ressourcen ohne Grenzen monopolisieren. Kusto bietet eine Reihe integrierter Schutzmaßnahmen in Form von standardmäßigen Abfrage Limits. Wenn Sie erwägen, diese Grenzwerte zu entfernen, müssen Sie zunächst ermitteln, ob Sie tatsächlich einen Wert erhalten.

## <a name="limit-on-query-concurrency"></a>Limit für Abfrage Parallelität

Die **Abfrage** Parallelität ist ein Limit, das ein Cluster für eine Reihe von Abfragen, die gleichzeitig ausgeführt werden, auferlegt.

* Der Standardwert des Abfrage Parallelitäts Limits hängt von dem SKU-Cluster ab, auf dem er ausgeführt wird, und wird wie folgt berechnet: `Cores-Per-Node x 10` .
  * Beispielsweise ist für einen Cluster, der auf der D14v2-SKU eingerichtet ist, wobei jeder Computer über 16 virtuelle Kerne verfügt, das Standard Limit für die Abfrage Parallelität `16 cores x10 = 160` .
* Der Standardwert kann durch Konfigurieren der [Richtlinie für die Abfrage Drosselung](../management/query-throttling-policy.md)geändert werden. 
  * Die tatsächliche Anzahl von Abfragen, die gleichzeitig in einem Cluster ausgeführt werden können, hängt von verschiedenen Faktoren ab. Die wichtigsten Faktoren sind Cluster-SKU, verfügbare Cluster Ressourcen und Abfrage Muster. Die Richtlinie für die Abfrage Drosselung kann basierend auf Auslastungs Tests konfiguriert werden, die für Produktions ähnliche Abfrage Muster ausgeführt werden.

## <a name="limit-on-result-set-size-result-truncation"></a>Grenzwert für die Größe des Resultsets (Ergebnis abschneiden)

Das **Abschneiden** von Ergebnissen ist ein Standardmäßiges Limit für das Resultset, das von der Abfrage zurückgegeben wird. Kusto schränkt die Anzahl der an den Client zurückgegebenen Datensätze auf **500.000**und die Gesamtgröße der Datensätze für diese Datensätze auf **64 MB**ein. Wenn eine dieser Grenzwerte überschritten wird, tritt bei der Abfrage ein Fehler mit dem Fehler "partielle Abfrage Fehler" auf. Wenn die Gesamtdaten Größe überschritten wird, wird eine Ausnahme mit der folgenden Meldung generiert:

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal data size limit 67108864 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

Das Überschreiten der Anzahl von Datensätzen schlägt mit einer Ausnahme fehl, die Folgendes besagt:

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal record count limit 500000 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

Es gibt eine Reihe von Strategien für den Umgang mit diesem Fehler.

* Reduzieren Sie die resultsetgröße, indem Sie die Abfrage so ändern, dass nur interessante Daten zurückgegeben werden Diese Strategie ist nützlich, wenn die anfängliche fehlerhafte Abfrage zu "breit" ist. Beispielsweise werden die Datenspalten, die nicht benötigt werden, von der Abfrage nicht entfernt.
* Verringern Sie die Größe des Resultsets, indem Sie die Verarbeitung nach Abfrage, z. b. Aggregationen, in die Abfrage selbst verschieben. Die Strategie ist in Szenarios nützlich, in denen die Ausgabe der Abfrage an ein anderes Verarbeitungssystem weitergeleitet wird und dann zusätzliche Aggregationen durchführt.
* Wechseln Sie von Abfragen zu mithilfe des [Datenexports](../management/data-export/index.md) , wenn Sie große Datenmengen aus dem Dienst exportieren möchten.
* Weisen Sie den Dienst an, dieses Abfragelimit mithilfe `set` der unten aufgeführten Anweisungen oder der Flags in den [Eigenschaften der Client Anforderung](../api/netfx/request-properties.md)zu unterdrücken.

Zu den Methoden zum Verringern der von der Abfrage erzeugten resultsetgröße gehören:

* Verwenden Sie die Gruppe zusammen [fassender Operator](../query/summarizeoperator.md) , und aggregieren Sie ähnliche Datensätze in der Abfrageausgabe. Möglicherweise werden einige Spalten mithilfe der [Aggregations Funktion](../query/any-aggfunction.md)als Stichprobe verwendet.
* Verwenden [Sie einen Take-Operator](../query/takeoperator.md) , um Stichproben der Abfrageausgabe auszuführen.
* Verwenden Sie die [Teil Zeichenfolge-Funktion](../query/substringfunction.md) , um Breite frei Textspalten zu kürzen.
* Verwenden Sie den [Project-Operator](../query/projectoperator.md) , um alle nicht interessanten Spalten aus dem Resultset zu löschen.

Sie können die Ergebnis Verkürzung mithilfe der `notruncation` Option Request deaktivieren.
Es wird empfohlen, dass eine bestimmte Art von Einschränkung immer noch vorhanden ist.

Beispiel:

```kusto
set notruncation;
MyTable | take 1000000
```

Es ist auch möglich, eine präzisetere Kontrolle über das Abschneiden von Ergebnissen zu haben, indem der Wert von `truncationmaxsize` (maximale Datengröße in Bytes, standardmäßig 64 MB) und `truncationmaxrecords` (maximale Anzahl von Datensätzen, Standardwert 500.000) festgelegt wird. Mit der folgenden Abfrage wird z. b. das Abschneiden von Ergebnissen auf 1.105-oder 1 MB festgelegt, je nachdem, welcher Wert überschritten wird.

```kusto
set truncationmaxsize=1048576;
set truncationmaxrecords=1105;
MyTable | where User=="UserId1"
```

Wenn Sie die Begrenzung für das Ergebnis abschneiden entfernen, ist es beabsichtigt, Massendaten aus Kusto zu verschieben.

Sie können den Grenzwert für das Abschneiden von Ergebnissen entweder für Exportzwecke mithilfe des- `.export` Befehls oder für die spätere Aggregation entfernen. Wenn Sie eine spätere Aggregation auswählen, sollten Sie ggf. mithilfe von Kusto aggregieren.

Kusto bietet eine Reihe von Client Bibliotheken, die "unendlich große" Ergebnisse verarbeiten können, indem Sie an den Aufrufer gestreamt werden. Verwenden Sie eine dieser Bibliotheken, und konfigurieren Sie Sie für den Streamingmodus. Verwenden Sie z. b. den .NET Framework Client (Microsoft. Azure. Kusto. Data), und legen Sie entweder die Streaming-Eigenschaft der Verbindungs Zeichenfolge auf *true*fest, oder verwenden Sie den *ExecuteQueryV2Async ()* -Befehl, der immer Ergebnisse streamt.

Die resulttrunzierung wird standardmäßig angewendet, nicht nur auf den resultstream, der an den Client zurückgegeben wird. Sie wird auch standardmäßig auf jede Unterabfrage angewendet, die ein Cluster mit ähnlichen Effekten an einen anderen Cluster in einer Cluster übergreifenden Abfrage ausgibt.

## <a name="limit-on-memory-per-iterator"></a>Limit für Arbeitsspeicher pro Iterator

Der **maximale Arbeitsspeicher pro resultsetiterator** ist ein weiterer Grenzwert, der von Kusto zum Schutz vor "Runaway"-Abfragen verwendet wird. Dieser Grenzwert, der durch die Option Request dargestellt `maxmemoryconsumptionperiterator` wird, legt eine obere Grenze für die Menge an Arbeitsspeicher fest, die ein einzelner Abfrageplan-resultsetiterator enthalten kann. Dieser Grenzwert gilt für die spezifischen Iteratoren, die nicht nach Natur gestreamt werden, z `join` . b..) Im folgenden finden Sie einige Fehlermeldungen, die zurückgegeben werden, wenn diese Situation auftritt:

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

Standardmäßig ist dieser Wert auf 5 GB festgelegt. Sie können diesen Wert um die Hälfte des physischen Speichers des Computers erhöhen:

```kusto
set maxmemoryconsumptionperiterator=68719476736;
MyTable | ...
```

In vielen Fällen kann das Überschreiten dieses Limits vermieden werden, indem das DataSet Stichproben entnommen wird. Die folgenden beiden Abfragen zeigen, wie die Stichprobenentnahme durchzuführen ist. Der erste ist eine statistische Stichprobe, bei der ein Zufallszahlengenerator verwendet wird. Die zweite ist eine deterministische Stichprobe, durch die ein Hashwert für eine Spalte aus dem DataSet erfolgt (in der Regel eine ID).

```kusto
T | where rand() < 0.1 | ...

T | where hash(UserId, 10) == 1 | ...
```

## <a name="limit-on-memory-per-node"></a>Limit für Arbeitsspeicher pro Knoten

Der **maximale Arbeitsspeicher pro Abfrage pro Knoten** ist ein weiterer Grenzwert, der zum Schutz vor "Runaway"-Abfragen verwendet wird. Dieser Grenzwert, der durch die Option Request dargestellt wird `max_memory_consumption_per_query_per_node` , legt eine obere Grenze für die Menge an Arbeitsspeicher fest, die auf einem einzelnen Knoten für eine bestimmte Abfrage verwendet werden kann.

```kusto
set max_memory_consumption_per_query_per_node=68719476736;
MyTable | ...
```

## <a name="limit-on-accumulated-string-sets"></a>Limit für akkumulierte Zeichen folgen Sätze

In verschiedenen Abfrage Vorgängen muss Kusto Zeichen folgen Werte "erfassen" und intern Puffern, bevor die Ergebnisse erzeugt werden. Diese akkumulierten Zeichen folgen Sätze sind beschränkt auf die Größe und die Anzahl der Elemente, die Sie enthalten können. Außerdem sollte jede einzelne Zeichenfolge eine bestimmte Grenze nicht überschreiten.
Wenn Sie eine dieser Grenzwerte überschreiten, führt dies zu einem der folgenden Fehler:

```
Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the limit of ...GB (see https://aka.ms/kustoquerylimits)')

Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the maximum count of ..GB items (see http://aka.ms/kustoquerylimits)')
```

Zurzeit gibt es keinen Switch, um die maximale Größe der Zeichen folgen Menge zu erhöhen.
Um dieses Problem zu umgehen, müssen Sie die Abfrage neu formulieren, um die Menge der Daten zu verringern, die gepuffert werden müssen. Nicht benötigte Spalten können entfernt werden, bevor Sie von Operatoren wie "Join" und "Zusammenfassung" verwendet werden. Oder Sie können die Strategie zum [Mischen von Abfragen](../query/shufflequery.md) verwenden.

## <a name="limit-execution-timeout"></a>Ausführungs Timeout begrenzen

**Server Timeout** ist ein Dienst seitiges Timeout, das auf alle Anforderungen angewendet wird.
Timeout bei Ausführung von Anforderungen (Abfragen und Steuerungsbefehle) wird an mehreren Punkten im Kusto erzwungen:

* Client Bibliothek (sofern verwendet)
* Dienst Endpunkt, der die Anforderung akzeptiert
* die Dienst-Engine, die die Anforderung verarbeitet.

Standardmäßig ist Timeout für Abfragen auf vier Minuten und für Steuerungsbefehle 10 Minuten festgelegt. Dieser Wert kann bei Bedarf (begrenzt auf eine Stunde) erweitert werden.

* Wenn Sie die Abfrage mithilfe von "Kusto. Explorer" durchführen **, verwenden Sie** Extras &gt; **Optionen** *  &gt; **Verbindungen** &gt; **Abfrage Server Timeout**.
* Legen `servertimeout` Sie die Client Anforderungs Eigenschaft (einen Wert vom Typ) Programm gesteuert `System.TimeSpan` auf eine Stunde fest.

**Hinweise zu Timeouts**

* Auf der Clientseite wird das Timeout von der erstellten Anforderung bis zum Startzeitpunkt der Antwort an den Client angewendet. Die Zeit, die zum Lesen der Nutzlast auf dem Client benötigt wird, wird nicht als Teil des Timeouts behandelt. Dies hängt davon ab, wie schnell der Aufrufer die Daten aus dem Stream abruft.
* Außerdem ist der tatsächliche Timeout Wert auf Clientseite geringfügig höher als der vom Benutzer angeforderte Server Timeout Wert. Dieser Unterschied besteht darin, Netzwerklatenzen zuzulassen.
* Um das maximal zulässige Anforderungs Timeout automatisch zu verwenden, legen Sie die Client Anforderungs Eigenschaft `norequesttimeout` auf fest `true` .

<!--
  Request timeout can also be set using a set statement, but we don't mention
  it here since it shouldn't be used in production scenarios.
-->

## <a name="limit-on-query-cpu-resource-usage"></a>Limit für Abfrage-CPU-Ressourcennutzung

Kusto ermöglicht das Ausführen von Abfragen und das Verwenden von so viel CPU-Ressourcen wie der Cluster. Es wird versucht, einen angemessenen Roundrobin zwischen Abfragen durchzuführen, wenn mehr als eine ausgeführt wird. Diese Methode ergibt die beste Leistung bei Ad-hoc-Abfragen.
Zu anderen Zeitpunkten können Sie die CPU-Ressourcen, die für eine bestimmte Abfrage verwendet werden, einschränken. Wenn Sie z. b. einen "Hintergrund Auftrag" ausführen, toleriert das System möglicherweise höhere Wartezeiten, um gleichzeitige Ad-hoc-Abfragen mit hoher Priorität zu versehen.

Kusto unterstützt das Angeben von zwei [Client Anforderungs Eigenschaften](../api/netfx/request-properties.md) , wenn eine Abfrage ausgeführt wird. Die Eigenschaften sind  *query_fanout_threads_percent* und *query_fanout_nodes_percent*.
Beide Eigenschaften sind ganze Zahlen, die standardmäßig den maximalen Wert (100) aufweisen, jedoch für eine bestimmte Abfrage auf einen anderen Wert reduziert werden können. 

Der erste, *query_fanout_threads_percent*, steuert den Verzweigungen angibt-Faktor für die Thread Verwendung. Bei 100% weist der Cluster alle CPUs auf jedem Knoten zu. Beispielsweise 16 CPUs in einem Cluster, der auf Azure D14-Knoten bereitgestellt ist. Wenn der Wert 50% beträgt, wird die Hälfte der CPUs verwendet usw. Die Zahlen werden auf eine ganze CPU aufgerundet, sodass Sie sicher auf 0 festgelegt werden kann. Mit dem zweiten *query_fanout_nodes_percent*wird gesteuert, wie viele Abfrage Knoten im Cluster pro Unterabfrage-Verteilungs Vorgang verwendet werden sollen. Es funktioniert auf ähnliche Weise.

## <a name="limit-on-query-complexity"></a>Beschränkung der Abfrage Komplexität

Während der Abfrage Ausführung wird der Abfragetext in eine Struktur von relationalen Operatoren transformiert, die die Abfrage darstellen.
Wenn die Baum Tiefe einen internen Schwellenwert von mehreren tausend Ebenen überschreitet, wird die Abfrage als zu komplex für die Verarbeitung betrachtet und schlägt mit einem Fehlercode fehl. Der Fehler zeigt an, dass die Struktur der relationalen Operatoren die Grenzen überschreitet.
Limits werden aufgrund von Abfragen mit langen Listen binärer Operatoren, die miteinander verkettet sind, überschritten. Beispiel:

```kusto
T 
| where Column == "value1" or 
        Column == "value2" or 
        .... or
        Column == "valueN"
```

Schreiben Sie für diesen speziellen Fall die Abfrage mit dem- [`in()`](../query/inoperator.md) Operator um.

```kusto
T 
| where Column in ("value1", "value2".... "valueN")
```
