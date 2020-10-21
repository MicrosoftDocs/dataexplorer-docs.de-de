---
title: 'schmale Plug-in: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird das schmale Plug-in in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5a27794647eed3e8b30533d73456a0b1fb8ccde6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243185"
---
# <a name="narrow-plugin"></a>narrow-Plug-In

```kusto
T | evaluate narrow()
```

Das `narrow` Plug-in "entpivotiert" eine breite Tabelle in eine Tabelle mit nur drei Spalten: Zeilennummer, Spaltentyp und Spaltenwert (as `string` ).

Das `narrow` Plug-in wird hauptsächlich zu Anzeige Zwecken entwickelt, da es ermöglicht, dass Breite Tabellen ohne horizontales Scrollen bequem angezeigt werden.

## <a name="syntax"></a>Syntax

`T | evaluate narrow()`

## <a name="examples"></a>Beispiele

Das folgende Beispiel zeigt eine einfache Möglichkeit, die Ausgabe des Kusto-Steuerelement Befehls zu lesen `.show diagnostics` .

```kusto
.show diagnostics
 | evaluate narrow()
```

Die Ergebnisse von `.show diagnostics` selbst sind eine Tabelle mit einer einzelnen Zeile und 33 Spalten. Mithilfe des Plug-Ins `narrow` "drehen" wir die Ausgabe in etwa wie folgt:

Zeile  | Column                              | Wert
-----|-------------------------------------|-----------------------------
0    | Ishealthy                           | True
0    | Isrebalancerequired                 | False
0    | Isscaleumquired                  | False
0    | Machinestotal                       | 2
0    | Machinesoffline                     | 0
0    | Nodelastrestartedon                 | 2017-03-14 10:59:18.9263023
0    | Adminlastelectedon                  | 2017-03-14 10:58:41.6741934
0    | Clusterwarmdatacapacityfactor       | 0.130552847673333
0    | Extentstotal                        | 136
0    | Diskcoldzugecationprozentsatz        | 5
0    | Instancestargetbasedondatacapacity  | 2
0    | Totaloriginaldatasize               | 5167628070
0    | Totalextentsize                     | 1779165230
0    | IngestionsLoadFactor                | 0
0    | Ingestionsinprogress                | 0
0    | Ingestionserfolgreiches Rate               | 100
0    | Mergesinprogress                    | 0
0    | BuildVersion                        | 1.0.6281.19882
0    | Buildtime                           | 2017-03-13 11:02:44.0000000
0    | ClusterDataCapacityFactor           | 0.130552847673333
0    | Isdatawarmingrequired               | False
0    | Rebalancelastrauunon                  | 2017-03-21 09:14:53.8523455
0    | Datawarminglastrauunon                | 2017-03-21 09:19:54.1438800
0    | Mergessuccess Rate                   | 100
0    | Noderalthyreason                    | normal
0    | Isattentionrequired                 | False
0    | Attentionrequirements dreason             | normal
0    | ProductVersion                      | KustoRelease_2017.03.13.2
0    | Failedingestoperations              | 0
0    | Failedmergeoperations               | 0
0    | Maxextentsinsingletable             | 64
0    | Tablewithmaxextents                 | Kustomonitoringpersistentdatabase. kustomonitoringtable
0    | Warmextentsize                      | 1779165230