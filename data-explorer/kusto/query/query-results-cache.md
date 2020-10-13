---
title: 'Abfrageergebnis Cache: Azure Daten-Explorer'
description: In diesem Artikel werden die Cache Funktionen für Abfrageergebnisse in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: d0942a949454bf12840626ff25d3703a23aed2cc
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002949"
---
# <a name="query-results-cache"></a>Abfrageergebniscache

Kusto enthält einen Abfrageergebnis Cache. Sie können auswählen, ob zwischengespeicherte Ergebnisse bei der Ausgabe einer Abfrage ausgegeben werden sollen. Sie erhalten eine bessere Abfrageleistung und einen geringeren Ressourcenverbrauch, wenn die Ergebnisse der Abfrage vom Cache zurückgegeben werden können. Diese Leistung führt jedoch zu einer gewissen "Veraltung" in den Ergebnissen.

## <a name="use-the-cache"></a>Verwenden des Caches

Legen `query_results_cache_max_age` Sie die Option als Teil der Abfrage fest, um den Abfrageergebnis Cache zu verwenden. Sie können diese Option im Abfragetext oder als Client Anforderungs Eigenschaft festlegen. Beispiel:

```kusto
set query_results_cache_max_age = time(5m);
GithubEvent
| where CreatedAt > ago(180d)
| summarize arg_max(CreatedAt, Type) by Id
```

Der Optionswert ist eine `timespan` , die das maximale "Alter" des Ergebnis Caches angibt, gemessen ab der Startzeit der Abfrage. Über den festgelegten TimeSpan-Wert hinaus ist der Cache Eintrag veraltet und wird nicht wieder verwendet. Wenn Sie den Wert 0 festlegen, wird die Option nicht festgelegt.

## <a name="compatibility-between-queries"></a>Kompatibilität zwischen Abfragen

### <a name="identical-queries"></a>Identische Abfragen

Der Abfrageergebnis Cache gibt Ergebnisse nur für Abfragen zurück, die mit einer vorherigen zwischengespeicherten Abfrage als "identisch" angesehen werden. Zwei Abfragen werden als identisch angesehen, wenn alle der folgenden Bedingungen erfüllt sind:

* Die beiden Abfragen haben dieselbe Darstellung (wie UTF-8-Zeichen folgen).
* Die beiden Abfragen werden an dieselbe Datenbank durchgeführt.
* Die beiden Abfragen verwenden die gleichen [Client Anforderungs Eigenschaften](../api/netfx/request-properties.md). Die folgenden Eigenschaften werden für zwischen Speicherungs Zwecke ignoriert:
   * [ClientRequestId](../api/netfx/request-properties.md#the-clientrequestid-x-ms-client-request-id-named-property)
   * [Anwendung](../api/netfx/request-properties.md#the-application-x-ms-app-named-property)
   * [Benutzer](../api/netfx/request-properties.md#the-user-x-ms-user-named-property)

### <a name="incompatible-queries"></a>Inkompatible Abfragen

Die Abfrageergebnisse werden nicht zwischengespeichert, wenn eine der folgenden Bedingungen zutrifft:
 
* Die Abfrage verweist auf eine Tabelle, für die die [restrictedviewaccess](../management/restrictedviewaccesspolicy.md) -Richtlinie aktiviert ist.
* Die Abfrage verweist auf eine Tabelle, für die die [rowlevelsecurity](../management/rowlevelsecuritypolicy.md) -Richtlinie aktiviert ist.
* Die Abfrage verwendet eine der folgenden Funktionen:
    * [current_principal](current-principalfunction.md)
    * [current_principal_details](current-principal-detailsfunction.md)
    * [current_principal_is_member_of](current-principal-ismemberoffunction.md)
* Die Abfrage greift auf eine [externe Tabelle](schema-entities/externaltables.md) oder auf [externe Daten](externaldata-operator.md)zu.
* Die Abfrage verwendet den Operator zum [Auswerten von Plug](evaluateoperator.md) -ins.

## <a name="no-valid-cache-entry"></a>Kein gültiger Cache Eintrag.

Wenn ein zwischengespeichertes Ergebnis, das die Zeiteinschränkungen erfüllt, nicht gefunden werden kann oder kein zwischengespeichertes Ergebnis aus einer "identischen" Abfrage im Cache vorhanden ist, wird die Abfrage ausgeführt, und die Ergebnisse werden zwischengespeichert, sofern: 

* Die Ausführung der Abfrage wurde erfolgreich abgeschlossen.
* Die Größe der Abfrageergebnisse überschreitet 16 MB.

## <a name="results-from-the-cache"></a>Ergebnisse aus dem Cache

Wie zeigt der Dienst an, dass die Abfrageergebnisse aus dem Cache bedient werden?
Bei der Reaktion auf eine Abfrage sendet Kusto eine zusätzliche " [ExtendedProperties](../api/rest/response.md) "-Antwort Tabelle, die eine `Key` Spalte und eine `Value` Spalte enthält.
Für zwischengespeicherte Abfrageergebnisse wird eine zusätzliche Zeile an die Tabelle angehängt:
* Die Spalte der Zeile `Key` enthält die Zeichenfolge. `ServerCache`
* Die Spalte der Zeile `Value` enthält einen Eigenschaften Behälter mit zwei Feldern:
   * `OriginalClientRequestId` : Gibt die [clientrequestid](../api/netfx/request-properties.md#the-clientrequestid-x-ms-client-request-id-named-property)der ursprünglichen Anforderung an.
   * `OriginalStartedOn` : Gibt die Startzeit der ursprünglichen Anforderung an.

## <a name="distribution"></a>Distribution

Der Cache wird nicht von Cluster Knoten gemeinsam genutzt. Jeder Knoten verfügt über einen dedizierten Cache in seinem eigenen privaten Speicher. Wenn zwei identische Abfragen auf verschiedenen Knoten ausgeführt werden, wird die Abfrage ausgeführt und auf beiden Knoten zwischengespeichert. Dieser Prozess kann auftreten, wenn eine [schwache Konsistenz](../concepts/queryconsistency.md) verwendet wird.

## <a name="management"></a>Verwaltung

Die folgenden Verwaltungs-und Wahrnehmbarkeit-Befehle werden unterstützt:

* [Cache anzeigen](../management/show-query-results-cache-command.md).
* [Cache löschen](../management/clear-query-results-cache-command.md).

## <a name="capacity"></a>Capacity

Die Cache Kapazität wird zurzeit auf 1 GB pro Cluster Knoten korrigiert.
Die Entfernungs Richtlinie ist LRU.
