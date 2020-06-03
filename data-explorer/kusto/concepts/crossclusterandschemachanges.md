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
ms.openlocfilehash: 9ed8ba6ac5e66326e6aa1d9e7a7f474506b57e26
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84328975"
---
# <a name="cross-cluster-queries-and-schema-changes"></a>Clusterübergreifende Abfragen und Schemaänderungen

Beim Ausführen einer Cluster übergreifenden Abfrage muss der Cluster, der die anfängliche Abfrage Interpretation durchführt, über das Schema der Entitäten verfügen, auf die in den Remote Clustern verwiesen wird.
Wenn die folgende Abfrage an *Cluster1* gesendet wird

```kusto
Table1 | join ( cluster("Cluster2").database("SomeDB").Table2 ) on KeyColumn
``` 

*Cluster1* muss wissen, welche Spalten *Table2* auf *Cluster2* hat, und die Datentypen dieser Spalten. Um diese Informationen zu erhalten, wird der spezielle Befehl von *Cluster1* an *Cluster2*gesendet.
Das Senden des Befehls kann kostspielig sein. Nach dem Abrufen werden die Schemas für Remote Entitäten zwischengespeichert, und die nächste Abfrage, die auf die gleichen Entitäten verweist, führt weniger Netzwerk Vorgänge aus.

Änderungen im Schema der Remote Entität können zu unerwünschten Auswirkungen führen. Beispielsweise werden hinzugefügte Spalten nicht erkannt, oder gelöschte Spalten verursachen anstelle eines semantischen Fehlers einen "partiellen Abfrage Fehler".

Um dieses Problem zu beheben, laufen zwischengespeicherte Schemas nach dem Abruf eine Stunde ab, sodass jede Abfrage, die mehr als eine Stunde nach der Änderung ausgeführt wird, mit dem aktuellen Schema funktioniert.
