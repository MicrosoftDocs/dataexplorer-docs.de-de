---
title: Abfragelimits - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden Abfragelimits in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 437e9781b9e29db7496292ba6a416f6e0c769e8b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523068"
---
# <a name="query-limits"></a>Abfragegrenzwerte

Da Kusto ein Ad-hoc-Abfragemodul ist, das große Datensätze hostet und versucht, Abfragen zu erfüllen, indem alle relevanten Daten im Arbeitsspeicher gespeichert werden, besteht die Gefahr, dass Abfragen die Dienstressourcen ohne Grenzen monopolisieren. Kusto bietet eine Reihe integrierter Schutzvorrichtungen in Form von Standardabfragelimits.

## <a name="limit-on-query-concurrency"></a>Begrenzung der Abfrageparallelität

**Abfrageparallelität** ist ein Grenzwert, den ein Cluster für eine Reihe von Abfragen auferlegt, die gleichzeitig ausgeführt werden.
Der Standardwert des Abfrageparallelitätslimits hängt vom SKU-Cluster ab, `Cores-Per-Node x 10`auf dem er ausgeführt wird, und wird wie folgt berechnet: . Für einen Cluster, der auf D14v2-SKU eingerichtet ist und für den jeder Computer 16 vCores hat, lautet `16 cores x10 = 160`das standardmäßige Limit für die Abfrageparallelität .
Der Standardwert kann durch Erstellen eines Supporttickets geändert werden. In Zukunft wird dieses Steuerelement auch über einen Steuerbefehl verfügbar gemacht.

## <a name="limit-on-result-set-size-result-truncation"></a>Begrenzung der Ergebnissatzgröße (Ergebniskürzung)

**Ergebniskürzungen** sind standardmäßig für das von der Abfrage zurückgegebene Resultset. Kusto begrenzt die Anzahl der an den Client zurückgegebenen Datensätze auf **500.000**und den Gesamtspeicher für diese Datensätze auf **64 MB**. Wenn einer dieser Grenzwerte überschritten wird, schlägt die Abfrage mit einem "teilabfragefehler" fehl. Durch das Überschreiten des Gesamtspeichers wird eine Ausnahme mit der Meldung generiert:

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal data size limit 67108864 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

Wenn Sie die Anzahl der Datensätze überschreiten, schlägt dies mit einer Ausnahme fehl, die besagt:

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal record count limit 500000 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

Es gibt eine Reihe von Strategien, um mit diesem Fehler umzugehen:

* Verringern der Ergebnissatzgröße, indem Sie die Abfrage so ändern, dass keine uninteressanten Daten zurückgegeben werden. Dies ist häufig nützlich, wenn die anfängliche (fehlende) Abfrage zu "breit" ist (z. B. projiziert sie keine Nicht-Datenspalten, die nicht benötigt werden).
* Verringern der Ergebnissatzgröße durch Verschieben der Verarbeitung nach der Abfrage (z. B. Aggregationen) in die Abfrage selbst. Dies ist in Szenarien nützlich, in denen die Ausgabe der Abfrage an ein anderes Verarbeitungssystem zugeführt wird, das dann zusätzliche Aggregationen ausführt. 
* Wechseln von Abfragen zu [Datenexport](../management/data-export/index.md).
   Dies ist sinnvoll, wenn Sie große Datensätze aus dem Dienst exportieren möchten.
* Weisen Sie den Dienst an, diesen Abfragegrenzwert zu unterdrücken.

Häufige Methoden zum Reduzieren der von der Abfrage erzeugten Ergebnissatzgröße sind:

* Verwenden der [zusammengefassten Operatorgruppe](../query/summarizeoperator.md) und des Aggregats über ähnliche Datensätze in der Abfrageausgabe, wodurch möglicherweise einige Spalten mithilfe der [aggregationsvariablen Funktion](../query/any-aggfunction.md)abgegriffen werden.
* Verwenden eines [take-Operators](../query/takeoperator.md) zum Beispiel der Abfrageausgabe.
* Verwenden der [Teilzeichenfolgenfunktion](../query/substringfunction.md) zum Trimmen breiter Freitextspalten.
* Verwenden des [Projektoperators,](../query/projectoperator.md) um eine uninteressante Spalte aus dem Resultset zu löschen.

Man kann die Ergebniskürzung mit `notruncation` der Anforderungsoption deaktivieren. Es wird dringend empfohlen, dass in diesem Fall noch eine Form der Einschränkung eingeführt wird. Beispiel:

```kusto
set notruncation;
MyTable | take 1000000
```

Es ist auch möglich, eine verfeinerte Kontrolle über die `truncationmaxsize` Ergebniskürzung zu haben, indem sie den `truncationmaxrecords` Wert von (maximale Datengröße in Bytes, Standardwerte auf 64 MB) und (maximale Anzahl von Datensätzen, Standardmäßig auf 500.000) festlegen. Die folgende Abfrage legt beispielsweise fest, dass die Ergebniskürzung entweder bei 1.105 Datensätzen oder bei 1 MB erfolgt, je nachdem, welcher Wert überschritten wird:

```kusto
set truncationmaxsize=1048576;
set truncationmaxrecords=1105;
MyTable | where User=="Ploni"
```

Die Kusto-Clientbibliotheken gehen derzeit von dieser Grenze aus. Während Sie das Limit ohne Grenzen erhöhen können, erreichen Sie schließlich Client-Limits, die derzeit nicht konfigurierbar sind. Eine mögliche Problemumgehung besteht darin, direkt gegen den REST-API-Vertrag zu programmieren und einen Streaming-Parser für die Kusto-Abfrageergebnisse zu implementieren. Lassen Sie das Kusto-Team wissen, ob Sie auf dieses Problem stoßen, damit wir einen Streaming-Client entsprechend priorisieren können.

Die Ergebniskürzung wird standardmäßig angewendet, nicht nur auf den Ergebnisstream, der an den Client zurückgegeben wird. Sie wird standardmäßig auch auf jede Unterabfrage angewendet, die ein Kusto-Cluster in einem anderen Kusto-Cluster in einer clusterübergreifenden Abfrage mit ähnlichen Auswirkungen ausgibt.

## <a name="limit-on-memory-per-iterator"></a>Speicherbeschränkung pro Iterator

**Max. Arbeitsspeicher pro Resultset-Iterator** ist ein weiterer Grenzwert, der von Kusto zum Schutz vor "Runaway"-Abfragen verwendet wird. Dieser Grenzwert (dargestellt durch `maxmemoryconsumptionperiterator`die Anforderungsoption ) legt eine Obergrenze für die Speichermenge fest, die ein einzelner Abfrageplan-Ergebnissatz-Iterator enthalten kann. (Diese Grenze gilt für die spezifischen Iteratoren, `join`die nicht von Natur aus gestreamt werden, z. B. .) Hier sind einige Fehlermeldungen, die zurückgegeben werden, wenn dies geschieht:

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

Standardmäßig ist dieser Wert auf 5 GB festgelegt. Man kann diesen Wert bis zur Hälfte des physischen Speichers der Maschine erhöhen:

```kusto
set maxmemoryconsumptionperiterator=68719476736;
MyTable | ...
```

Wenn Sie erwägen, diese Grenzwerte zu entfernen, stellen Sie zunächst fest, ob Sie dadurch tatsächlich einen Wert gewinnen. Insbesondere bedeutet das Entfernen des Ergebnisabschneidelimits, dass Sie beabsichtigen, Massendaten aus Kusto zu verschieben -- `.export` entweder zu Exportzwecken (in diesem Fall werden Sie aufgefordert, den Befehl stattdessen zu verwenden) oder für eine spätere Aggregation (in diesem Fall erwägen Sie die Aggregation mit Kusto).
Lassen Sie das Kusto-Team wissen, ob Sie ein Geschäftsszenario haben, das von keiner dieser vorgeschlagenen Lösungen erfüllt werden kann.  

In vielen Fällen kann eine Überschreitung dieses Grenzwerts vermieden werden, indem der Datensatz auf 10 % abgesenkt wird. Die beiden folgenden Abfragen zeigen, wie dieses Sampling ausgeführt wird. Die erste ist eine statistische Stichprobe (mit Hilfe eines Zufallszahlengenerators). Die zweite ist deterministische Sampel (durch Hashing einer Spalte aus dem Datensatz, in der Regel eine ID):

```kusto
T | where rand() < 0.1 | ...

T | where hash(UserId, 10) == 1 | ...
```

## <a name="limit-on-memory-per-node"></a>Beschränkung des Arbeitsspeichers pro Knoten

**Der maximale Arbeitsspeicher pro Abfrage pro Knoten** ist ein weiterer Grenzwert, der von Kusto zum Schutz vor "Runaway"-Abfragen verwendet wird. Dieser Grenzwert (dargestellt durch `max_memory_consumption_per_query_per_node`die Anforderungsoption ) legt eine Obergrenze für die Speichermenge fest, die auf einem einzelnen Knoten für eine bestimmte Abfrage zugewiesen werden kann. 


```kusto
set max_memory_consumption_per_query_per_node=68719476736;
MyTable | ...
```

## <a name="limit-on-accumulated-string-sets"></a>Limit für akkumulierte Zeichenfolgensätze

Bei verschiedenen Abfragevorgängen muss Kusto Zeichenfolgenwerte "sammeln" und intern puffern, bevor die Ergebnisse erstellt werden können. Diese akkumulierten Zeichenfolgensätze sind in der Größe und in der Anzahl der Elemente, die sie aufnehmen können, begrenzt. Darüber hinaus darf jede einzelne Zeichenfolge einen bestimmten Grenzwert nicht überschreiten.
Das Überschreiten einer dieser Grenzwerte führt zu einem der folgenden Fehler:

```
Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the limit of ...GB (see https://aka.ms/kustoquerylimits)')

Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the maximum count of 2G items (see http://aka.ms/kustoquerylimits)')

Runaway query (E_RUNAWAY_QUERY). (message: 'Single string size shouldn't exceed the limit of 2GB (see http://aka.ms/kustoquerylimits)')
```

Derzeit gibt es keinen Schalter, um die maximale Größe des Zeichenfolgensatzes zu erhöhen.
Um die summieren Sie die Abfrage, um die Datenmenge zu reduzieren, die gepuffert werden muss. Zum Beispiel, indem sie nicht benötigte Spalten vor dem Eingeben von Operatoren wie Join und Summarize "eintragen". Oder z. B. mithilfe der [Shuffle-Abfragestrategie.](../query/shufflequery.md)

## <a name="limit-on-request-execution-time-timeout"></a>Limit bei Anforderung Ausführungszeit (Timeout)

**Das Servertimeout** ist ein dienstseitiges Timeout, das auf alle Anforderungen angewendet wird.
Timeout für ausgeführte Anforderungen (Abfragen und Steuerbefehle) wird an mehreren Punkten erzwungen:

* In der Kusto-Clientbibliothek (falls verwendet)
* Im Kusto-Serviceendpunkt, der die Anforderung annimmt
* Im Kusto-Servicemodul, das die Anforderung verarbeitet

Standardmäßig ist das Timeout für Abfragen auf vier Minuten und für Steuerbefehle auf zehn Minuten festgelegt. Dieser Wert kann bei Bedarf erhöht werden (begrenzt auf eine Stunde):

* Wenn Sie mit Kusto.Explorer abfragen, verwenden Sie **Tools** &gt; **Options** *  &gt; **Connections** &gt; **Query Server Timeout**.
* Legen Sie programmgesteuert `servertimeout` die Clientanforderungseigenschaft `System.TimeSpan`fest (ein Wert vom Typ bis zu einer Stunde).

Hinweise zu Timeouts:

* Auf der Clientseite wird das Timeout von der zu erstellenden Anforderung bis zu dem Zeitpunkt angewendet, zu dem die Antwort beim Client eintrifft. Die Zeit, die zum Zurücklesen der Nutzlast am Client benötigt wird, wird nicht als Teil des Timeouts behandelt (da es davon abhängt, wie schnell der Aufrufer die Daten aus dem Stream abruft).
* Auch auf der Clientseite ist der tatsächlich verwendete Timeoutwert etwas höher als der vom Benutzer angeforderte Servertimeoutwert. Dadurch können Netzwerklatenermöglicht werden.
* Auf der Dienstseite berücksichtigen nicht alle Abfrageoperatoren den Timeoutwert.
   Wir fügen diese Unterstützung schrittweise hinzu.
* Damit Kusto das maximal zulässige Anforderungstimeout automatisch `norequesttimeout` `true`verwendet, legen Sie die Clientanforderungseigenschaft auf fest.

<!--
  Request timeout can also be set using a set statement, but we don't mention
  it here as it should not be used in production scenarios.
-->

## <a name="limit-on-query-cpu-resource-usage"></a>Begrenzung der CPU-Ressourcenauslastung von Abfragecpa

Standardmäßig ermöglicht Kusto ausführenden Abfragen, so viele CPU-Ressourcen wie der Cluster zu verwenden, und versucht, einen fairen Roundrobin zwischen Abfragen auszuführen, wenn mehr als eine ausgeführt wird. In vielen Fällen liefert dies die beste Leistung für Ad-hoc-Abfragen.
In anderen Fällen kann es vorkommen, dass die CPU-Ressourcen für eine bestimmte Abfrage eingeschränkt werden. Wenn man z. B. einen "Hintergrundjob" ausführt, kann man höhere Latenzen tolerieren, um gleichzeitigen Ad-hoc-Abfragen hohe Priorität einzuräumen.

Kusto unterstützt die Angabe von zwei [Clientanforderungseigenschaften](../api/netfx/request-properties.md) beim Ausführen einer Abfrage, **query_fanout_threads_percent** und **query_fanout_nodes_percent**.
Beide sind ganze Zahlen, die standardmäßig den Maximalwert (100) haben, aber für eine bestimmte Abfrage auf einen bestimmten Wert reduziert werden können. Die erste steuert den Fanout-Faktor für die Threadverwendung. Wenn es 100 % beträgt, weist der Cluster der Abfrage alle CPUs auf jedem Knoten zu (z. B. auf einem Cluster, der auf Azure D14-Knoten bereitgestellt wird, 16 CPUs), wenn es 50 % mehr ist als die Hälfte der CPUs verwendet wird usw. (Die Zahlen werden auf eine ganze CPU aufgerundet, sodass es sicher ist, sie auf 0 festzulegen.) Der zweite steuert, wie viele Knoten im Cluster pro Unterabfrageverteilungsvorgang verwendet werden sollen, und funktioniert auf ähnliche Weise.

## <a name="limit-on-query-complexity"></a>Begrenzung der Abfragekomplexität

Während der Abfrageausführung wird der Abfragetext in eine Struktur relationaler Operatoren umgewandelt, die die Abfrage darstellen.
Falls die Baumtiefe einen internen Schwellenwert (mehrere tausend Ebenen) überschreitet, wird die Abfrage als zu komplex für die Verarbeitung betrachtet und schlägt fehl mit einem Fehlercode, der angibt, dass die Struktur für relationale Operatoren Die Grenzwerte überschreitet.
In den meisten Fällen wird dies durch eine Abfrage verursacht, die eine lange Liste von binären Operatoren enthält, die miteinander verkettet sind, z. B.:

```kusto
T 
| where Column == "value1" or 
        Column == "value2" or 
        .... or
        Column == "valueN"
```

Für diesen speziellen Fall wird empfohlen, die [`in()`](../query/inoperator.md) Abfrage mithilfe des Operators neu zu schreiben. 

```kusto
T 
| where Column in ("value1", "value2".... "valueN")
```