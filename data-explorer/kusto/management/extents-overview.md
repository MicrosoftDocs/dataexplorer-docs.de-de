---
title: 'Blöcke (Daten-Shards): Azure Daten-Explorer'
description: In diesem Artikel werden die Blöcke (Daten-Shards) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/13/2020
ms.openlocfilehash: 6c7910a222227dbb6b22e1fc4f0f4136897a0ef9
ms.sourcegitcommit: be1bbd62040ef83c08e800215443ffee21cb4219
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84665111"
---
# <a name="extents-data-shards"></a>Blöcke (Daten-Shards)

## <a name="overview"></a>Übersicht

Kusto ist so aufgebaut, dass Tabellen mit einer großen Anzahl von Datensätzen (Zeilen) und großen Datenmengen unterstützt werden. Um so große Tabellen zu verarbeiten, werden die Daten der einzelnen Tabellen in kleinere "Tablets" unterteilt, die als **datenshards** oder **Blöcke** bezeichnet werden (die beiden Begriffe sind Synonym). Die Kombination aller Tabellen Blöcke enthält die Daten der Tabelle. Einzelne Blöcke werden kleiner als die Kapazität eines einzelnen Knotens gehalten, und die Blöcke werden über die Knoten des Clusters verteilt, wodurch die horizontale Skalierung erreicht wird.

Ein Block ist ein like-Typ von Mini Tabellen. Sie enthält Daten und Metadaten und Informationen, wie z. b. die Erstellungszeit und optionale Tags, die Ihren Daten zugeordnet sind. Darüber hinaus enthält der Umfang in der Regel Informationen, mit denen die Daten von Kusto effizient abgefragt werden können.
Beispielsweise ein Index für jede Datenspalte im Block und ein Codierungs Wörterbuch, wenn Spaltendaten codiert sind. Folglich sind die Daten der Tabelle die Vereinigung aller Daten in den Blöcken der Tabelle.

Blöcke sind unveränderlich und können nie geändert werden. Sie kann nur abgefragt, einem anderen Knoten neu zugewiesen oder aus der Tabelle gelöscht werden. Die Datenänderung erfolgt durch das Erstellen eines oder mehrerer neuer Blöcke und das transaktionell austauschen Alter Blöcke mit neuen Erweiterungen.

Blöcke enthalten eine Auflistung von Datensätzen, die physisch in Spalten angeordnet sind.
Diese Technik wird als **Spalten-Speicher**bezeichnet. Sie ermöglicht eine effiziente Codierung und Komprimierung der Daten, da unterschiedliche Werte aus derselben Spalte häufig einander ähneln. Außerdem wird das Abfragen großer Daten spannen effizienter, da nur die von der Abfrage verwendeten Spalten geladen werden müssen. Intern wird jede Datenspalte im Wertebereich in Segmente unterteilt, und die Segmente werden in Blöcke unterteilt. Diese Division ist für Abfragen nicht Observable und ermöglicht Kusto die Optimierung der Spalten Komprimierung und-Indizierung.

Um die Effizienz der Abfrage aufrechtzuerhalten, werden kleinere Blöcke in größere Blöcke zusammengeführt.
Die Zusammenführung erfolgt automatisch als Hintergrundprozess gemäß der konfigurierten [mergerichtlinie](mergepolicy.md) und der [Sharding-Richtlinie](shardingpolicy.md).
Durch das Zusammenführen von Blöcken wird der Verwaltungsaufwand reduziert, wenn eine große Anzahl von Blöcke nachverfolgt werden muss. Noch wichtiger ist, dass es Kusto ermöglicht, seine Indizes zu optimieren und die Komprimierung zu verbessern.

Das Zusammenführen von Blöcken wird beendet, sobald ein Block bestimmte Grenzwerte erreicht, wie z. b. die Größe, da nach einem bestimmten Punkt eine Zusammenführung reduziert wird, anstatt

Wenn eine [Richtlinie für die Daten Partitionierung](partitioningpolicy.md) für eine Tabelle definiert wird, werden Blöcke durch einen anderen Hintergrundprozess durchlaufen, nachdem Sie erstellt wurden (nach der Erfassung). Bei diesem Prozess werden die Daten aus den Quell Blöcken wieder eingefügt, und es werden *homogene* Blöcke erstellt, in denen die Werte der Spalte, die den *Partitions Schlüssel* der Tabelle darstellt, zur selben Partition gehören. Wenn die Richtlinie einen *Hash Partitions Schlüssel*enthält, werden alle homogenen Blöcke, die derselben Partition angehören, demselben Datenknoten im Cluster zugewiesen.

> [!NOTE]
> Vorgänge auf Blockebene, z. b. das Zusammenführen oder Ändern von Block-Tags, ändern vorhandene Blöcke nicht.
> Stattdessen werden in diesen Vorgängen auf der Grundlage vorhandener Quell Blöcke neue Blöcke erstellt. Die neuen Blöcke ersetzen Ihre forefathers in einer einzelnen Transaktion.

Der allgemeine Block Lebenszyklus ist:

1. Der Block wird **durch einen** Erfassungs Vorgang erstellt.
1. Der Block wird mit anderen Blöcken zusammengeführt. Wenn die Blöcke, die zusammengeführt werden, klein sind, führt Kusto tatsächlich einen Erfassungsprozess mit dem Namen **Rebuild**aus. Sobald die Blöcke eine bestimmte Größe erreichen, erfolgt die Zusammenführung nur für Indizes. Die Daten Artefakte der Blöcke im Speicher werden nicht geändert.
1. Der zusammengeführte Block (möglicherweise eine, der seine Herkunft auf andere zusammengeführte Blöcke nachverfolgt usw.) wird aufgrund einer Aufbewahrungs Richtlinie gelöscht. 
   Wenn Blöcke basierend auf der Zeit (älter x Stunden/Tage) gelöscht werden, wird das Erstellungsdatum des neuesten Wertebereichs innerhalb des zusammengeführten Bereichs in der Berechnung verwendet.

## <a name="extent-creation-time"></a>Erstellungszeit für Block

Eine der wichtigsten Informationen zu den einzelnen Blöcken ist die Erstellungszeit. Diese Zeit wird für Folgendes verwendet:

1. **Beibehaltungs** Dauer: Blöcke, die zuvor erstellt wurden, werden zuvor gelöscht.
1. **Caching** : Blöcke, die vor kurzem erstellt wurden, werden im [Hot Cache](cachepolicy.md)aufbewahrt.)
1. **Sampling** : letzte Blöcke werden bevorzugt, wenn Abfrage Vorgänge verwendet werden, z. b.`take`

Tatsächlich verfolgt Kusto zwei `datetime` Werte pro Block nach: `MinCreatedOn` und `MaxCreatedOn` .
Anfänglich sind die beiden Werte identisch. Wenn der Block mit anderen Blöcken zusammengeführt wird, entsprechen die neuen Werte den ursprünglichen minimalen und maximalen Werten der zusammengeführten Blöcke.

Normalerweise wird die Erstellungszeit eines Bereichs entsprechend der Zeit festgelegt, in der die Daten im Block erfasst werden. Clients können die Erstellungszeit für den Block optional überschreiben, indem Sie eine Alternative Erstellungszeit in den Erfassungs [Eigenschaften](../../ingestion-properties.md)angeben.
Das Überschreiben ist z. b. für Aufbewahrungs Zwecke hilfreich, wenn der Client Daten wieder aufnehmen möchte und nicht möchten, dass er in späterer Zeit angezeigt wird.

## <a name="extent-tagging"></a>Blockmarkierung

Kusto unterstützt das Anfügen mehrerer Optionaler Block *Markierungen* als Teil der Metadaten. Ein Block-Tag (oder einfach ein *Tag*) ist eine Zeichenfolge, die dem Block zugeordnet ist. Sie können die [. Showblöcke](extents-commands.md#show-extents) -Befehle verwenden, um die einem Block zugeordneten Tags anzuzeigen, und die Funktion "Block [-Tags ()](../query/extenttagsfunction.md) ", um die den Datensätzen zugeordneten Tags in einem Block anzuzeigen.
Block Tags können verwendet werden, um Eigenschaften effizient zu beschreiben, die von allen Daten im Block gemeinsam verwendet werden.
Beispielsweise können Sie während der Erfassung ein Block-Tag hinzufügen, das die Quelle der erfassten Daten angibt, und dieses Tag später verwenden. Da die Blöcke Datenblöcke beschreiben, werden die zugehörigen Tags ebenfalls zusammengeführt, wenn Sie zwei oder mehr zusammenführen. Das resultierende Block-Tag ist die Vereinigung aller Tags dieser zusammengeführten Blöcke.

Kusto weist allen Block Tags, deren Wert das Format *Präfix* *Suffix*hat, eine besondere Bedeutung zu, wobei das *Präfix* einem der folgenden Werte entspricht:

* `drop-by:`
* `ingest-by:`

### <a name="drop-by-extent-tags"></a>"Drop-by:"-Block Tags

Tags, die mit einem `drop-by:` Präfix beginnen, können verwendet werden, um zu steuern, welche anderen Blöcke zusammengeführt werden sollen. Blöcke mit einem bestimmten `drop-by:` Tag können zusammengeführt werden, Sie werden jedoch nicht mit anderen Blöcken zusammengeführt. Sie können dann einen Befehl ausgeben, um Werte Blöcke gemäß ihrem Tag zu löschen `drop-by:` .

Beispiel:

```kusto
.ingest ... with @'{"tags":"[\"drop-by:2016-02-17\"]"}'

.drop extents <| .show table MyTable extents where tags has "drop-by:2016-02-17" 
```

#### <a name="performance-notes"></a>Leistungs Hinweise

* Überschreiben Sie keine `drop-by` Tags. Das Löschen von Daten in der oben erwähnten Weise ist für selten auftretende Ereignisse gedacht. Es dient nicht zum Ersetzen von Daten auf Datensatzebene, und es basiert darauf, dass die auf diese Weise markierten Daten sperrig sind. Der Versuch, ein anderes Tag für jeden Datensatz oder eine kleine Anzahl von Datensätzen anzugeben, kann zu schwerwiegenden Auswirkungen auf die Leistung führen.
* Wenn `drop-by` Tags nach dem Erfassen der Daten nicht länger benötigt werden, empfiehlt es sich, [die Tags zu löschen](extents-commands.md#drop-extent-tags).

### <a name="ingest-by-extent-tags"></a>"Erfassungs-nach:"-Block Tags

Tags, die mit einem `ingest-by:` Präfix beginnen, können verwendet werden, um sicherzustellen, dass Daten nur einmal erfasst werden. Sie können einen **`ingestIfNotExists`** Eigenschafts Befehl ausgeben, mit dem verhindert wird, dass die Daten erfasst werden, wenn bereits ein Block mit diesem bestimmten Tag vorhanden ist `ingest-by:` .
Die Werte für `tags` und `ingestIfNotExists` sind Arrays von Zeichen folgen, die als JSON serialisiert werden.

Im folgenden Beispiel werden Daten nur einmal erfasst. Der 2. und der dritte Befehl führen keine Aktionen aus:

```kusto
.ingest ... with (tags = '["ingest-by:2016-02-17"]')

.ingest ... with (ingestIfNotExists = '["2016-02-17"]')

.ingest ... with (ingestIfNotExists = '["2016-02-17"]', tags = '["ingest-by:2016-02-17"]')
```

> [!NOTE]
> Im Allgemeinen enthält ein Erfassungs Befehl wahrscheinlich sowohl ein `ingest-by:` -Tag als auch eine- `ingestIfNotExists` Eigenschaft, die auf denselben Wert festgelegt ist, wie im obigen dritten Befehl gezeigt.

#### <a name="performance-notes"></a>Leistungs Hinweise

* Das Überschreiben von `ingest-by` Tags wird nicht empfohlen.
Wenn die Pipeline, die Kusto nährt, Daten Duplizierungen hat, empfiehlt es sich, diese Duplikate so weit wie möglich zu beheben, bevor die Daten in Kusto erfasst werden. Verwenden Sie auch `ingest-by` Tags in Kusto nur dann, wenn der Teil, der zu Kusto gehört, möglicherweise Duplikate einführt (z. b. gibt es einen Wiederholungs Mechanismus, der sich mit bereits in Bearbeitung befindlichen Erfassungs aufrufen überlappen kann). Wenn Sie versuchen, ein eindeutiges Tag für jeden Erfassungs Befehl festzulegen, `ingest-by` kann dies schwerwiegende Auswirkungen auf die Leistung haben.
* Wenn solche Tags für einige Zeit nach dem Erfassen der Daten nicht erforderlich sind, wird empfohlen, dass Sie [die Tags löschen](extents-commands.md#drop-extent-tags).
 
