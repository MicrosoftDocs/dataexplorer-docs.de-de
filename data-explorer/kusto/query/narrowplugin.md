---
title: Schmales Plugin - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt ein eingeschränktes Plugin in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 75b211f32c15eefc60ca40b0408345be4a656652
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512239"
---
# <a name="narrow-plugin"></a>schmales Plugin

```kusto
T | evaluate narrow()
```

Das `narrow` Plugin "entpivotiert" eine breite Tabelle in eine Tabelle mit nur drei Spalten: Zeilennummer, Spaltentyp und Spaltenwert (als `string`).

Das `narrow` Plugin ist hauptsächlich für Display-Zwecke konzipiert, da es ermöglicht, breite Tabellen bequem ohne die Notwendigkeit von horizontalen Scrollen angezeigt werden.

**Syntax**

`T | evaluate narrow()`

**Beispiele**

Das folgende Beispiel zeigt eine einfache Möglichkeit, `.show diagnostics` die Ausgabe des Kusto-Steuerbefehls zu lesen.

```kusto
.show diagnostics
 | evaluate narrow()
```

Das Ergebnis `.show diagnostics` selbst ist eine Tabelle mit einer einzelnen Zeile und 33 Spalten. Mit dem `narrow` Plugin "drehen" wir die Ausgabe auf etwa wie folgt:

Zeile  | Column                              | Wert
-----|-------------------------------------|-----------------------------
0    | IsHealthy                           | True
0    | IsRebalanceErforderlich                 | False
0    | IsScaleOutRequired                  | False
0    | MaschinenTotal                       | 2
0    | MaschinenOffline                     | 0
0    | NodeLastRestartedOn                 | 2017-03-14 10:59:18.9263023
0    | AdminLastElectedOn                  | 2017-03-14 10:58:41.6741934
0    | ClusterWarmDataCapacityFactor       | 0.130552847673333
0    | ExtentsTotal                        | 136
0    | DiskColdAllocationProzentsatz        | 5
0    | InstanzenTargetBasedOnDataCapacity  | 2
0    | TotalOriginalDataSize               | 5167628070
0    | TotalExtentSize                     | 1779165230
0    | IngestionsLoadFactor                | 0
0    | IngestionsInProgress                | 0
0    | IngestionsSuccessRate               | 100
0    | MergesInProgress                    | 0
0    | BuildVersion                        | 1.0.6281.19882
0    | BuildTime                           | 2017-03-13 11:02:44.0000000
0    | ClusterDataCapacityFactor           | 0.130552847673333
0    | IsDataWarmingErforderlich               | False
0    | RebalanceLastRunOn                  | 2017-03-21 09:14:53.8523455
0    | DatawarmingLastRunon                | 2017-03-21 09:19:54.1438800
0    | MergesSuccessRate                   | 100
0    | NotHealthyReason                    | [null]
0    | IsAttentionRequired                 | False
0    | AchtungRequiredReason             | [null]
0    | ProductVersion                      | KustoRelease_2017.03.13.2
0    | FailedIngestOperations              | 0
0    | FailedMergeOperations               | 0
0    | MaxExtentsInSingleTable             | 64
0    | TableWithMaxExtents                 | KustoMonitoringPersistentDatabase.KustoMonitoringTable
0    | WarmExtentSize                      | 1779165230