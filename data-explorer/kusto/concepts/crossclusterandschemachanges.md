---
title: 'Kusto-Cluster übergreifende Abfragen & Schema Änderungen: Azure Daten-Explorer'
description: In diesem Artikel werden Cluster übergreifende Abfragen und Schema Änderungen in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 58fd8cef3836d17eb327734338b1567d815d7349
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382028"
---
# <a name="cross-cluster-queries-and-schema-changes"></a>Clusterübergreifende Abfragen und Schemaänderungen 

Beim Ausführen einer Cluster übergreifenden Abfrage muss der Cluster, der die anfängliche Abfrage Interpretation durchführt, über das Schema der Entitäten verfügen, auf die in Remote Clustern verwiesen wird.
Wenn die folgende Abfrage an *Cluster1* gesendet wird.

```kusto
Table1 | join ( cluster("Cluster2").database("SomeDB").Table2 ) on KeyColumn
``` 

*Cluster1* muss wissen, welche Spalten *Table2* auf *Cluster2* und welche Datentypen diese Spalten haben. Dies hat zur Folge, dass der spezielle Befehl von *Cluster1* an *Cluster2*gesendet wird.
Das Senden dieses Befehls kann sehr kostspielig sein. nach dem Abrufen werden die Schemas für Remote Entitäten zwischengespeichert, und die nächste Abfrage, die auf die gleichen Entitäten verweist, muss weniger Netzwerk Vorgänge ausführen.

Dies kann unerwünschte Effekte verursachen, wenn sich das Schema der Remote Entität ändert (hinzugefügte Spalten, die nicht erkannt werden, oder gelöschte Spalten, die zu einem Teil Abfrage Fehler führen, anstatt Semantik Fehler)

Um dieses Problem zu beheben, laufen zwischengespeicherte Schemas nach dem Abruf innerhalb von 1 Stunde ab, sodass jede Abfrage, die seit mehr als einer Stunde nach der Änderung ausgeführt wird, mit dem aktuellen Schema funktioniert.