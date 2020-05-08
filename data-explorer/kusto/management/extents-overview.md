---
title: 'Blöcke (Daten-Shards): Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel werden die Blöcke (Daten-Shards) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/13/2020
ms.openlocfilehash: 839042b50fce6409de29c4cf979a253a7485fb7f
ms.sourcegitcommit: ef009294b386cba909aa56d7bd2275a3e971322f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82977149"
---
# <a name="extents-data-shards"></a>Blöcke (Daten-Shards)

## <a name="overview"></a>Übersicht

Kusto ist so aufgebaut, dass Tabellen mit einer großen Anzahl von Datensätzen (Zeilen) und vielen Daten unterstützt werden. Um solche großen Tabellen verarbeiten zu können, dividiert Kusto die Daten der einzelnen Tabellen in kleinere "Tablets", die als **datenshards** oder **Blöcke** bezeichnet werden (die beiden Begriffe sind Synonyme), sodass die Gesamtmenge der Blöcke der Tabelle die Daten der Tabelle enthält. Einzelne Blöcke werden dann kleiner als die Kapazität eines einzelnen Knotens gehalten, und die Blöcke werden über die Knoten des Clusters verteilt, um eine horizontale Skalierung zu erreichen. 

Sie können sich einen Block als Art von Mini Tabelle vorstellen. Der Block enthält Metadaten (gibt das Schema der Daten im Block und zusätzliche Informationen, wie z. b. die Erstellungszeit und optionale Tags, die den Daten im Block zugeordnet sind, und die Daten an. Darüber hinaus enthält der Umfang in der Regel Informationen, die es Kusto ermöglichen, die Daten effizient abzufragen, z. b. einen Index für jede Datenspalte im Block, und ein Codierungs Wörterbuch, wenn Spaltendaten codiert werden. Folglich sind die Daten der Tabelle die Vereinigung aller Daten in den Blöcken der Tabelle.

Blöcke sind *unveränderlich*. Nach der Erstellung wird ein Block nie geändert, und der Block kann nur abgefragt, einem anderen Knoten neu zugewiesen oder aus der Tabelle gelöscht werden. Die Datenänderung erfolgt durch Erstellen eines oder mehrerer neuer Blöcke und des transaktionalen Austauschs von alten Blöcken mit neuen Blöcken.

Blöcke enthalten eine Auflistung von Datensätzen, die physisch in Spalten angeordnet sind.
Diese Technik (als **Spalten-Speicher**bezeichnet) ermöglicht die effiziente Codierung und Komprimierung der Daten (da unterschiedliche Werte aus derselben Spalte häufig einander ähneln) und das Abfragen von großen Daten spannen effizienter ist, da nur die von der Abfrage verwendeten Spalten geladen werden müssen. Intern wird jede Datenspalte im Wertebereich in Segmente unterteilt, und die Segmente werden in Blöcke unterteilt. Diese Division (die nicht Observable für Abfragen ist) ermöglicht Kusto die Optimierung der Spalten Komprimierung und-Indizierung.

Um die Effizienz der Abfrage aufrechtzuerhalten, werden kleinere Blöcke in größere Blöcke zusammengeführt.
Dies wird automatisch von Kusto als Hintergrundprozess gemäß der konfigurierten [mergerichtlinie](mergepolicy.md) und der [Sharding-Richtlinie](shardingpolicy.md)ausgeführt.
Durch das Zusammenführen von Blöcken wird der Verwaltungsaufwand reduziert, wenn eine große Anzahl von Blöcke nachverfolgt werden muss, aber noch wichtiger ist, dass es Kusto ermöglicht, seine Indizes zu optimieren und die Komprimierung zu verbessern. Die Block Zusammenführung wird beendet, sobald ein Block bestimmte Grenzwerte erreicht, z. b. Größe

Wenn eine [Richtlinie für die Daten Partitionierung](partitioningpolicy.md) für eine Tabelle definiert wird, werden Blöcke durch einen anderen Hintergrundprozess durchlaufen, nachdem Sie erstellt wurden (nach der Erfassung). Bei diesem Prozess werden die Daten aus den Quell Blöcken neu erfasst und *homogene* Blöcke erstellt, in denen die Werte der Spalte, die den *Partitions Schlüssel* der Tabelle darstellt, zur selben Partition gehören. Wenn die Richtlinie einen *Hash Partitions Schlüssel*enthält, wird sichergestellt, dass alle homogenen Blöcke, die derselben Partition angehören, demselben Datenknoten im Cluster zugewiesen werden.

> [!NOTE]
> Vorgänge auf Blockebene, z. b. das Zusammenführen, das Ändern von Block Tags usw., ändern vorhandene Blöcke nicht.
> Stattdessen werden in diesen Vorgängen auf der Grundlage vorhandener Quell Blöcke neue Blöcke erstellt, und dann ersetzen diese neuen Blöcke ihre forefathers in einer einzelnen Transaktion.

Der gemeinsame Lebenszyklus eines Blocks ist daher:

1. Der Block wird **durch einen** Erfassungs Vorgang erstellt.
2. Der Block wird mit anderen Blöcken zusammengeführt. Wenn die Blöcke, die zusammengeführt werden, klein sind, führt Kusto tatsächlich einen Erfassungsprozess durch (Dies wird als **Rebuild**bezeichnet). Sobald die Blöcke eine bestimmte Größe erreichen, wird die Zusammenführung nur für Indizes ausgeführt, und die Daten Artefakte der Blöcke im Speicher werden nicht geändert.
3. Der zusammengeführte Block (möglicherweise eine, der seine Herkunft auf andere zusammengeführte Blöcke nachverfolgt usw.) wird aufgrund einer Aufbewahrungs Richtlinie gelöscht. Wenn Blöcke basierend auf der Zeit (ältere x Stunden/Tage) gelöscht werden, wird das Erstellungsdatum des neuesten Wertebereichs innerhalb des zusammengeführten Bereichs in die Berechnung übernommen.

## <a name="extent-ingestion-time"></a>Erfassungs Zeit für Block

Eine der wichtigsten Informationen zu den einzelnen Blöcken ist die Erfassungs Zeit. Diese Zeit wird von Kusto für Folgendes verwendet:

1. Aufbewahrung (Blöcke, die zuvor erfasst wurden, werden zuvor gelöscht).
2. Zwischenspeichern (Blöcke, die vor kurzem erfasst wurden, sind heißer).
3. Stichprobenentnahme (bei Verwendung von Abfrage `take`Vorgängen wie werden die aktuellen Blöcke bevorzugt).

Tatsächlich verfolgt Kusto zwei `datetime` Werte pro Block nach: `MinCreatedOn` und. `MaxCreatedOn`
Diese Werte beginnen gleich, aber wenn der Block mit anderen Blöcken zusammengeführt wird, sind die Werte für den resultierenden Wertebereich der Mindest-und Höchstwert bzw. die Werte für alle zusammengeführten Blöcke.

Die Erfassungs Zeit eines Blocks kann auf eine von drei Arten festgelegt werden:

1. Normalerweise legt der Knoten, der die Erfassung ausführt, diesen Wert entsprechend seiner lokalen Uhr fest.
2. Wenn eine Erfassungs **Zeit Richtlinie** für die Tabelle festgelegt ist, legt der Knoten, der die Erfassung durchführt, diesen Wert entsprechend der lokalen Uhr des Verwaltungs Knotens des Clusters fest. Dadurch wird sichergestellt, dass alle späteren Ingestionen einen höheren Wert für die Erfassungs Zeit aufweisen.
3. Dieser Zeitraum kann vom Client festgelegt werden. (Dies ist z. b. hilfreich, wenn der Client Daten erneut erfassen und nicht möchten, dass die erneut erfassten Daten so angezeigt werden, als ob Sie später eintreffen würden, z. b. für die Aufbewahrung).    

## <a name="extent-tagging"></a>Blockmarkierung

Als Teil der Metadaten, die mit einem Block gespeichert werden, unterstützt Kusto das Anfügen mehrerer Optionaler Block *Tags* bis zu diesem Block. Ein Block-Tag (oder einfach ein *Tag*) ist eine Zeichenfolge, die dem Block zugeordnet ist. Sie können das [. Showblöcke](extents-commands.md#show-extents) -Befehle verwenden, um die einem Block zugeordneten Tags anzuzeigen, und die Block [-Tags ()](../query/extenttagsfunction.md) -Funktion, um die den Datensätzen zugeordneten Tags in einem Block anzuzeigen.
Block Tags können verwendet werden, um Eigenschaften effizient zu beschreiben, die sich auf alle Daten im Bereich beziehen.
Beispielsweise kann bei der Erfassung ein Block-Tag hinzugefügt werden, das die Quelle der erfassten Daten angibt, und später wird dieses Tag verwendet. Wenn zwei oder mehr Blöcke zusammengeführt werden, werden die zugeordneten Tags ebenfalls zusammengeführt, wenn die zu einem Ergebnis gehörigen Tags die Gesamtmenge aller Block Tags der Blöcke darstellen, die zusammengeführt werden.

Kusto weist allen Block Tags, deren Wert das Format *Präfix* *Suffix*hat, eine besondere Bedeutung zu, wobei das *Präfix* einem der folgenden Werte entspricht:

* `drop-by:`
* `ingest-by:`

### <a name="drop-by-extent-tags"></a>"Drop-by:"-Block Tags

Tags, die mit einem **`drop-by:`** Präfix beginnen, können verwendet werden, um zu steuern, welche anderen Blöcke zusammengeführt werden sollen. Blöcke mit einem bestimmten `drop-by:` Tag können zusammengeführt werden, Sie werden jedoch nicht mit anderen Blöcken zusammengeführt. Dadurch kann der Benutzer einen Befehl ausgeben, um Werte Blöcke gemäß Ihrem `drop-by:` Tag zu löschen, wie z. b. den folgenden Befehl:

```kusto
.ingest ... with @'{"tags":"[\"drop-by:2016-02-17\"]"}'

.drop extents <| .show table MyTable extents where tags has "drop-by:2016-02-17" 
```

#### <a name="performance-notes"></a>Leistungs Hinweise

* Die Verwendung `drop-by` von Tags wird nicht empfohlen. Die Unterstützung für das Ablegen von Daten in der obigen Weise ist für selten auftretende Ereignisse gedacht, nicht für das Ersetzen von Daten auf Datensatzebene, und die Tatsache, dass die Daten auf diese Weise markiert werden, ist "sperrig". Der Versuch, ein anderes Tag für jeden Datensatz oder eine kleine Anzahl von Datensätzen anzugeben, kann zu schwerwiegenden Auswirkungen auf die Leistung führen.
* In Fällen, in denen solche Tags nach dem Erfassen der Daten nicht länger erforderlich sind, wird empfohlen, [die Tags zu löschen](extents-commands.md#drop-extent-tags).

### <a name="ingest-by-extent-tags"></a>"Erfassungs-nach:"-Block Tags

Tags, die mit einem **`ingest-by:`** Präfix beginnen, können verwendet werden, um sicherzustellen, dass Daten nur einmal erfasst werden. Der Benutzer kann einen Erfassungs Befehl ausgeben, der verhindert, dass die Daten erfasst werden, wenn es bereits einen Block mit diesem `ingest-by:` bestimmten Tag gibt, **`ingestIfNotExists`** indem die-Eigenschaft verwendet wird.
Die Werte für `tags` und `ingestIfNotExists` sind Arrays von Zeichen folgen, die als JSON serialisiert werden.

Im folgenden Beispiel werden Daten nur einmal erfasst (der zweite und der dritte Befehl führen nichts aus):

```kusto
.ingest ... with (tags = '["ingest-by:2016-02-17"]')

.ingest ... with (ingestIfNotExists = '["2016-02-17"]')

.ingest ... with (ingestIfNotExists = '["2016-02-17"]', tags = '["ingest-by:2016-02-17"]')
```

> [!NOTE]
> Im Allgemeinen wird ein Erfassungs Befehl wahrscheinlich sowohl ein `ingest-by:` -Tag als auch eine `ingestIfNotExists` -Eigenschaft enthalten, die auf denselben Wert festgelegt ist (wie im obigen 3. Befehl gezeigt).

#### <a name="performance-notes"></a>Leistungs Hinweise

- Das über `ingest-by` Schreiben von Tags wird nicht empfohlen.
Wenn die Pipeline zum Füttern von Kusto Daten Duplizierungen hat, es wird empfohlen, diese so weit wie möglich zu beheben, bevor die Daten in Kusto erfasst werden, und Tags `ingest-by` in Kusto nur für Fälle zu verwenden, in denen der Teil, der zu Kusto gehört, selbst Duplikate einführen könnte (z. b. gibt es einen Wiederholungs Mechanismus, der sich mit bereits in Bearbeitung befindlichen Erfassungs aufrufen überlappen kann). Wenn Sie versuchen, ein `ingest-by` eindeutiges Tag für jeden Erfassungs Befehl festzulegen, kann dies schwerwiegende Auswirkungen auf die Leistung haben.
- In Fällen, in denen solche Tags nach dem Erfassen der Daten nicht länger erforderlich sind, wird empfohlen, [die Tags zu löschen](extents-commands.md#drop-extent-tags).
