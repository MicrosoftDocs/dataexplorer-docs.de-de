---
title: Ausdehnungen (Datenshards) - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden Ausdehnungen (Datenshards) in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/13/2020
ms.openlocfilehash: 16afb2dc7d2310e9e63ec3465937ac84c96b27e4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521011"
---
# <a name="extents-data-shards"></a>Ausdehnungen (Datenshards)

## <a name="overview"></a>Übersicht

Kusto wurde entwickelt, um Tabellen mit einer großen Anzahl von Datensätzen (Zeilen) und vielen Daten zu unterstützen. Um mit solchen großen Tabellen umgehen zu können, unterteilt Kusto die Daten jeder Tabelle in kleinere "Tablets", die **als Datenshards** oder **-ausbezeichnet** werden (die beiden Begriffe sind Synonyme), sodass die Vereinigung aller Tabellenausdehnungen die Daten der Tabelle enthält. Einzelne Ausdehnungen werden dann kleiner als die Kapazität eines einzelnen Knotens gehalten, und die Ausdehnungen werden über die Knoten des Clusters verteilt, wodurch scale-out erreicht wird. 

Man kann sich ein Ausmaß als eine Art Mini-Tisch vorstellen. Die Ausdehnung enthält Metadaten (die das Schema der Daten in der Ausdehnung und zusätzliche Informationen wie die Erstellungszeit und optionale Tags angeben, die den Daten in der Ausdehnung zugeordnet sind) und Daten. Darüber hinaus enthält die Ausdehnung in der Regel Informationen, die es Kusto ermöglichen, die Daten effizient abzufragen, z. B. einen Index für jede Datenspalte in der Ausdehnung und ein Codierungswörterbuch, wenn Spaltendaten codiert werden. Daher sind die Daten der Tabelle die Vereinigung aller Daten in den Ausdehnungen der Tabelle.

Ausdehnungen sind *unveränderlich*. Nach der Erstellung wird eine Ausdehnung nie geändert, und die Ausdehnung kann nur abgefragt, einem anderen Knoten zugewiesen oder aus der Tabelle entfernt werden. Die Datenänderung erfolgt durch Erstellen einer oder mehreren neuen Ausdehnungen und Transaktionsaustausch alter Ausdehnungen mit neuen Ausdehnungen.

Extents enthalten eine Sammlung von Datensätzen, die physisch in Spalten angeordnet sind.
Diese Technik **(spaltenar erwerden**) ermöglicht das effiziente Codieren und Komprimieren der Daten (da sich unterschiedliche Werte aus derselben Spalte häufig gegenseitig "ähneln" und das Abfragen großer Datenspannen effizienter macht, da nur die von der Abfrage verwendeten Spalten geladen werden müssen. Intern wird jede Datenspalte in der Ausdehnung in Segmente und die Segmente in Blöcke unterteilt. Diese Division (die für Abfragen nicht beobachtbar ist) ermöglicht Es Kusto, die Spaltenkomprimierung und -indizierung zu optimieren.

Um die Abfrageeffizienz aufrechtzuerhalten, werden kleinere Ausdehnungen in größeren Ausdehnungen zusammengeführt.
Dies wird automatisch von Kusto als Hintergrundprozess gemäß der konfigurierten [Mergerichtlinie](mergepolicy.md) und [Sharding-Richtlinie](shardingpolicy.md)durchgeführt.
Durch das Zusammenführen von Erweiterungen verringert sich der Verwaltungsaufwand für eine große Anzahl von Nachverfolgungen, aber noch wichtiger ist, dass Kusto seine Indizes optimieren und die Komprimierung verbessern kann. Die Ausdehnungszusammenführung stoppt, sobald eine Ausdehnung bestimmte Grenzen erreicht, wie z. B. Größe, da die Zusammenführung über einen bestimmten Punkt hinaus die Effizienz eher verringert als erhöht.

Wenn eine [Datenpartitionierungsrichtlinie](partitioningpolicy.md) in einer Tabelle definiert ist, durchlaufen Ausdehnungen einen anderen Hintergrundprozess, nachdem sie erstellt wurden (Nacheinnahme). Dieser Prozess erfasst die Daten aus den Quellausdehnungen erneut und erstellt *homogene* Ausdehnungen, in denen die Werte der Spalte, die der *Partitionsschlüssel* der Tabelle ist, alle zur gleichen Partition gehören. Wenn die Richtlinie einen *Hashpartitionsschlüssel*enthält, ist sichergestellt, dass alle homogenen Ausdehnungen, die zur gleichen Partition gehören, demselben Datenknoten im Cluster zugewiesen werden.

> [!NOTE]
> Vorgänge auf Ausdehnungsebene, z. B. Zusammenführen, Ändern von Ausdehnungs-Tags usw., ändern keine vorhandenen Ausdehnungen.
> Vielmehr werden in diesen Vorgängen neue Ausdehnungen basierend auf vorhandenen Quellausdehnungen erstellt, und dann ersetzen diese neuen Ausdehnungen ihre Vorfahren in einer einzigen Transaktion.

Der gemeinsame "Lebenszyklus" eines Ausmaßes ist daher:

1. Die Ausdehnung wird durch einen **Aufnahmevorgang** erstellt.
2. Die Ausdehnung wird mit anderen Ausdehnungen zusammengeführt. Wenn die Zusammenlaufausdehnungen klein sind, führt Kusto tatsächlich einen Aufnahmeprozess für sie durch (dies wird **rebuild**genannt). Sobald Ausdehnungen eine bestimmte Größe erreichen, erfolgt die Zusammenführung nur für Indizes, und die Datenartefakte der Ausdehnungen im Speicher werden nicht geändert.
3. Die zusammengeführte Ausdehnung (möglicherweise eine, die ihre Abstammung auf andere zusammengeführte Ausdehnungen und so weiter verfolgt) wird schließlich aufgrund einer Aufbewahrungsrichtlinie verworfen. Wenn Ausdehnungen basierend auf der Zeit (ältere x Stunden / Tage) fallen gelassen werden, wird das Erstellungsdatum der neuesten Ausdehnung innerhalb der zusammengeführten in die Berechnung einbezogen.

## <a name="extent-ingestion-time"></a>Extent-Einnahmezeit

Eine der wichtigeren Informationen für jedes Ausmaß ist ihre Einnahmezeit. Diese Zeit wird von Kusto für verwendet:

1. Aufbewahrung (Ausdehnungen, die zuvor aufgenommen wurden, werden früher gelöscht).
2. Caching (Ausdehnungen, die kürzlich aufgenommen wurden, werden heißer).
3. Sampling (bei Verwendung von `take`Abfragevorgängen wie , werden aktuelle Ausdehnungen bevorzugt).

Tatsächlich verfolgt Kusto `datetime` zwei Werte `MinCreatedOn` pro `MaxCreatedOn`Ausdehnung: und .
Diese Werte beginnen gleich, aber wenn die Ausdehnung mit anderen Ausdehnungen zusammengeführt wird, sind die Werte der resultierenden Ausdehnung der minimale bzw. maximale Wert über alle zusammengeführten Ausdehnungen.

Die Aufnahmezeit einer Ausdehnung kann auf eine von drei Arten festgelegt werden:

1. Normalerweise legt der Knoten, der die Aufnahme durchführt, diesen Wert entsprechend seiner lokalen Uhr fest.
2. Wenn eine **Erfassungszeitrichtlinie** für die Tabelle festgelegt ist, legt der Knoten, der die Aufnahme durchführt, diesen Wert entsprechend der lokalen Uhr des Administratorknotens des Clusters fest, um sicherzustellen, dass alle späteren Erfassungen einen höheren Aufnahmezeitwert haben.
3. Der Client kann diese Zeit festlegen. (Dies ist z. B. nützlich, wenn der Client Daten erneut erfassen möchte und nicht möchte, dass die erneut aufgenommenen Daten so angezeigt werden, als ob sie verspätet angekommen wären, z. B. zu Aufbewahrungszwecken).    

## <a name="extent-tagging"></a>Extent-Tagging

Als Teil der Metadaten, die mit einer Ausdehnung gespeichert sind, unterstützt Kusto das Anfügen mehrerer optionaler *Ausdehnungs-Tags* in der Ausdehnung. Ein Extent-Tag (oder einfach *tag*) ist eine Zeichenfolge, die der Ausdehnung zugeordnet ist. Sie können die [Befehl .show extents](extents-commands.md#show-extents) verwenden, um die Tags anzuzeigen, die einer Ausdehnung zugeordnet sind, und die [Extent-tags()-Funktion,](../query/extenttagsfunction.md) um die Tags anzuzeigen, die Datensätzen in einer Ausdehnung zugeordnet sind.
Ausdehnungs-Tags können verwendet werden, um Eigenschaften effizient zu beschreiben, die sich auf alle Daten in der Ausdehnung beziehen.
Beispielsweise könnte man während der Aufnahme ein Ausdehnungs-Tag hinzufügen, das die Quelle der aufgenommenen Daten angibt, und dieses Tag später verwenden. Wie sie Daten beschreiben, werden die zugehörigen Tags, wenn zwei oder mehr Ausdehnungen zusammengeführt werden, auch zusammengeführt, indem die Tags der resultierenden Ausdehnung die Vereinigung aller Ausdehnungs-Tags der zu verschmelzenden Ausdehnungen sind.

Kusto weist allen Ausdehnungstags, deren Wert das *Formatpräfixsuffix* *suffix*hat, eine besondere Bedeutung zu, wobei *das Präfix* eines von:

* `drop-by:`
* `ingest-by:`

## <a name="drop-by-extent-tags"></a>'drop-by:' Ausdehnungs-Tags

Tags, die **`drop-by:`** mit einem Präfix beginnen, können verwendet werden, um zu steuern, mit welchen anderen Erweiterungen zusammengeführt werden soll. Erweiterungen, die `drop-by:` ein bestimmtes Tag haben, können zusammengeführt werden, aber sie werden nicht mit anderen Ausdehnungen zusammengeführt. Auf diese Weise kann der Benutzer einen Befehl `drop-by:` ausgeben, um Ausdehnungen gemäß seinem Tag zu löschen, z. B. den folgenden Befehl:

```kusto
.ingest ... with @'{"tags":"[\"drop-by:2016-02-17\"]"}'

.drop extents <| .show table MyTable extents where tags has "drop-by:2016-02-17" 
```

### <a name="performance-notes"></a>Leistungshinweise

* Eine über-verwendungsbasierte `drop-by` Tags wird nicht empfohlen. Die Unterstützung für das Löschen von Daten in der oben genannten Weise ist für selten auftretende Ereignisse gedacht, dient nicht zum Ersetzen von Datensätzen auf Datensatzebene und verlässt sich kritisch darauf, dass die Daten, die auf diese Weise markiert werden, "bulky" sind. Der Versuch, für jeden Datensatz oder eine kleine Anzahl von Datensätzen ein anderes Tag zu geben, kann schwerwiegende Auswirkungen auf die Leistung haben.
* In Fällen, in denen solche Tags nach der Erfassung von Daten nicht einen gewissen Zeitraum benötigen, wird empfohlen, [die Tags zu löschen.](extents-commands.md#drop-extent-tags)

## <a name="ingest-by-extent-tags"></a>'ingest-by:' Ausdehnungs-Tags

Tags, die **`ingest-by:`** mit einem Präfix beginnen, können verwendet werden, um sicherzustellen, dass Daten nur einmal aufgenommen werden. Der Benutzer kann einen Ingest-Befehl ausgeben, der verhindert, dass die Daten `ingest-by:` aufgenommen werden, **`ingestIfNotExists`** wenn die Eigenschaft bereits eine Ausdehnung mit diesem bestimmten Tag vorhanden ist.
Die Werte `tags` für `ingestIfNotExists` beide und sind Arrays von Zeichenfolgen, serialisiert als JSON.

Im folgenden Beispiel werden Daten nur einmal erfasst (der 2. und der dritte Befehl tun nichts):

```kusto
.ingest ... with (tags = '["ingest-by:2016-02-17"]')

.ingest ... with (ingestIfNotExists = '["2016-02-17"]')

.ingest ... with (ingestIfNotExists = '["2016-02-17"]', tags = '["ingest-by:2016-02-17"]')
```

> [!NOTE]
> Im allgemeinen Fall enthält ein Ingest-Befehl `ingest-by:` wahrscheinlich sowohl `ingestIfNotExists` ein Tag als auch eine Eigenschaft, die auf denselben Wert festgelegt ist (wie im 3. Befehl oben gezeigt).

### <a name="performance-notes"></a>Leistungshinweise

- Eine `ingest-by` Überverwendung von Tags wird nicht empfohlen.
Wenn die Pipeline-Zuführung Kusto bekannt ist, datenduplizierungen haben, wird empfohlen, diese so weit wie `ingest-by` möglich zu lösen, bevor die Daten in Kusto aufgenommen werden, und Tags in Kusto nur für Fälle zu verwenden, in denen der Teil, der in Kusto aufgenommen wird, Selbst duplizieren könnte (z. B. gibt es einen Wiederholungsmechanismus, der sich mit bereits laufenden Aufnahmeaufrufen überlappen kann). Der Versuch, für `ingest-by` jeden Aufnahmeaufruf ein eindeutiges Tag festzulegen, kann schwerwiegende Auswirkungen auf die Leistung haben.
- In Fällen, in denen solche Tags nach der Erfassung von Daten nicht einen gewissen Zeitraum benötigen, wird empfohlen, [die Tags zu löschen.](extents-commands.md#drop-extent-tags)