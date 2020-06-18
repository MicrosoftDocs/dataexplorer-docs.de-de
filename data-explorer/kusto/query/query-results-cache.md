---
title: 'Abfrageergebnis Cache: Azure Daten-Explorer'
description: In diesem Artikel werden die Cache Funktionen für Abfrageergebnisse in Azure Daten-Explorer beschrieben.
services: data-explorer
author: amitof
ms.author: amitof
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 3c1e1eec2bb0ad4d856ca690039dea9aedd78e8f
ms.sourcegitcommit: a8575e80c65eab2a2118842e59f62aee0ff0e416
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84943085"
---
# <a name="query-results-cache"></a>Abfrageergebniscache

Kusto enthält einen Abfrageergebnis Cache. Sie können die Bereitschaft angeben, zwischengespeicherte Ergebnisse bei der Ausgabe einer Abfrage zu erhalten. Wenn die Ergebnisse der Abfrage durch den Cache zurückgegeben werden können, wird die Abfrageleistung verbessert und der Ressourcenverbrauch auf Kosten einiger "Veraltung" in den Ergebnissen gesenkt.

## <a name="indicating-willingness-to-use-the-cache"></a>Angeben der Bereitschaft, den Cache zu verwenden

Legen `query_results_cache_max_age` Sie die Option als Teil der Abfrage fest, um die Bereitschaft anzugeben, den Abfrageergebnis Cache zu verwenden. Sie können diese Option entweder im Abfragetext oder als Client Anforderungs Eigenschaft festlegen. Beispiel:

```kusto
set query_results_cache_max_age = time(5m);
GithubEvent
| where CreatedAt > ago(180d)
| summarize arg_max(CreatedAt, Type) by Id
```

Der Optionswert ist eine `timespan` , die das maximale "Alter" des Ergebnis Caches angibt, gemessen ab der Startzeit der Abfrage. Über den festgelegten TimeSpan-Wert hinaus ist der Cache Eintrag veraltet und wird nicht wieder verwendet. Wenn Sie den Wert 0 festlegen, wird die Option nicht festgelegt.

## <a name="how-compatibility-between-queries-is-determined"></a>Bestimmen der Kompatibilität zwischen Abfragen

Die query_results_cache gibt nur Ergebnisse für Abfragen zurück, die mit einer vorherigen zwischengespeicherten Abfrage als "identisch" angesehen werden. Zwei Abfragen werden als identisch angesehen, wenn alle der folgenden Bedingungen erfüllt sind:

* Die beiden Abfragen haben dieselbe Darstellung (wie UTF-8-Zeichen folgen).

* Die beiden Abfragen werden an dieselbe Datenbank durchgeführt.

* Die beiden Abfragen verwenden die gleichen [Client Anforderungs Eigenschaften](../api/netfx/request-properties.md). Die folgenden Eigenschaften werden für zwischen Speicherungs Zwecke ignoriert:
   * [ClientRequestId](../api/netfx/request-properties.md#the-clientrequestid-x-ms-client-request-id-named-property)
   * [Anwendung](../api/netfx/request-properties.md#the-application-x-ms-app-named-property)
   * [Benutzer](../api/netfx/request-properties.md#the-user-x-ms-user-named-property)

## <a name="if-the-query-results-cache-cant-find-a-valid-cache-entry"></a>Wenn der Abfrageergebnis Cache keinen gültigen Cache Eintrag finden kann

Wenn ein zwischengespeichertes Ergebnis, das die Zeiteinschränkungen erfüllt, nicht gefunden werden kann oder kein zwischengespeichertes Ergebnis aus einer "identischen" Abfrage im Cache vorhanden ist, wird die Abfrage ausgeführt, und die Ergebnisse werden zwischengespeichert, sofern: 

* Die Ausführung der Abfrage wurde erfolgreich abgeschlossen.
* Die Größe der Abfrageergebnisse überschreitet 16 MB.

## <a name="how-the-service-indicates-that-the-query-results-are-being-served-from-the-cache"></a>Gibt an, wie der Dienst anzeigt, dass die Abfrageergebnisse aus dem Cache bedient werden.

Bei der Reaktion auf eine Abfrage sendet Kusto eine zusätzliche " [ExtendedProperties](../api/rest/response.md) "-Antwort Tabelle, die eine `Key` Spalte und eine `Value` Spalte enthält.
Für zwischengespeicherte Abfrageergebnisse wird eine zusätzliche Zeile an die Tabelle angehängt:
* Die Spalte der Zeile `Key` enthält die Zeichenfolge.`ServerCache`
* Die Spalte der Zeile `Value` enthält einen Eigenschaften Behälter mit zwei Feldern:
   * `OriginalClientRequestId`: Gibt die [clientrequestid](../api/netfx/request-properties.md#the-clientrequestid-x-ms-client-request-id-named-property)der ursprünglichen Anforderung an.
   * `OriginalStartedOn`: Gibt die Startzeit der ursprünglichen Anforderung an.

## <a name="distribution"></a>Verteilung

Der Cache wird nicht von Cluster Knoten gemeinsam genutzt. Jeder Knoten verfügt über einen dedizierten Cache in seinem eigenen privaten Speicher. Wenn zwei identische Abfragen auf verschiedenen Knoten ausgeführt werden, wird die Abfrage ausgeführt und auf beiden Knoten zwischengespeichert. Dieser Prozess kann auftreten, wenn eine [schwache Konsistenz](../concepts/queryconsistency.md) verwendet wird.

## <a name="management"></a>Verwaltung

Die folgenden Verwaltungs-und Wahrnehmbarkeit-Befehle werden unterstützt:

* [Cache anzeigen](../management/show-query-results-cache-command.md).
* [Cache löschen](../management/clear-query-results-cache-command.md).

## <a name="capacity"></a>Capacity

Die Cache Kapazität wird zurzeit auf 1 GB pro Cluster Knoten korrigiert.
Die Entfernungs Richtlinie ist LRU.
