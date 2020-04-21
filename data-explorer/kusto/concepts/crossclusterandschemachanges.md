---
title: Clusterübergreifende Abfragen und Schemaänderungen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden clusterübergreifende Abfragen und Schemaänderungen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a9473add657fe8a888818f83c72f12d47138c00e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523289"
---
# <a name="cross-cluster-queries-and-schema-changes"></a>Clusterübergreifende Abfragen und Schemaänderungen 

Bei der Ausführung einer clusterübergreifenden Abfrage muss der Cluster, der die anfängliche Abfrageinterpretation durchführt, über das Schema der Entitäten verfügen, auf die in Remoteclustern verwiesen wird.
Wenn also die folgende Abfrage an *Cluster1* gesendet wird

```kusto
Table1 | join ( cluster("Cluster2").database("SomeDB").Table2 ) on KeyColumn
``` 

*Cluster1* muss wissen, welche Spalten *Table2* auf *Cluster2* hat und welche Datentypen diese Spalten aufweisen. Um zu erreichen, dass der spezielle Befehl von *Cluster1* an *Cluster2*gesendet wird.
Dieses Senden dieses Befehls kann ziemlich teuer sein, daher werden nach dem Abrufen die Schemas für Remoteentitäten zwischengespeichert, und die nächste Abfrage, die auf dieselben Entitäten verweist, muss weniger Netzwerkvorgänge ausführen.

Dies kann unerwünschte Auswirkungen verursachen, wenn sich das Schema der Remoteentität ändert (hinzugefügte Spalten, die nicht erkannt werden, oder gelöschte Spalten, die "Partial Query Error" anstelle eines semantischen Fehlers verursachen).

Um dieses Problem zu beheben, laufen zwischengespeicherte Schemas in 1 Stunde nach dem Abruf ab, sodass jede Abfrage, die in mehr als 1 Stunde nach der Änderung ausgeführt wird, mit dem aktuellen Schema funktioniert.