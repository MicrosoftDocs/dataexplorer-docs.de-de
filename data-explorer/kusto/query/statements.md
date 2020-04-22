---
title: Abfrageanweisungen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden Abfrageanweisungen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 20eeb1aa755fcf4e3068cba061a2738a375e1847
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765561"
---
# <a name="query-statements"></a>Abfrageanweisungen

::: zone pivot="azuredataexplorer"

Eine Abfrage besteht aus einer oder mehreren **Abfrageanweisungen**`;`, die durch ein Semikolon ( ) getrennt sind.
Mindestens eine dieser Abfrageanweisungen muss eine [tabellarische Ausdrucksanweisung](./tabularexpressionstatements.md)sein.
Die tabellenförmige Ausdrucksanweisung generiert ein oder mehrere tabellarische Ergebnisse.
Wenn die Abfrage über mehr als eine tabellenförmige Ausdrucksanweisung verfügt, verfügt die Abfrage über einen [Batch](./batches.md) von Tabellenausdrucksanweisungen, und die durch diese Anweisungen generierten Tabellenergebnisse werden alle von der Abfrage zurückgegeben.

Zwei Arten von Abfrageanweisungen:

* Anweisungen, die in erster Linie von Benutzern verwendet werden ([Benutzerabfrageanweisungen](#user-query-statements)),
* Anweisungen, die für die Unterstützung von Szenarien entwickelt wurden, in denen Mid-Tier-Anwendungen Benutzerabfragen annehmen und eine geänderte Version davon an Kusto senden ([Anwendungsabfrageanweisungen](#application-query-statements)).

Einige Abfrageanweisungen sind in beiden Szenarien nützlich.

> [!NOTE]
> Der "Effekt" einer Abfrageanweisung beginnt an dem Punkt, an dem die Anweisung in der Abfrage angezeigt wird, und endet am Ende der Abfrage. Sobald die Abfrage abgeschlossen ist, werden alle Ressourcen freigegeben, und sie hat keine Auswirkungen auf zukünftige Abfragen (außer Nebenwirkungen, z. B. das Aufzeichnen der Abfrage in einem Protokoll aller Abfragen oder das Zwischenspeichern der Ergebnisse).

## <a name="user-query-statements"></a>Benutzerabfrageanweisungen

Im Folgenden finden Sie eine Liste der Benutzerabfrageanweisungen:

* Eine [let-Anweisung](./letstatement.md) definiert eine Bindung zwischen einem Namen und einem Ausdruck.
  Let-Anweisungen können verwendet werden, um eine lange Abfrage in kleine benannte Teile aufzuteilen, die leichter zu verstehen sind.

* Eine [set-Anweisung](./setstatement.md) legt eine Abfrageoption fest, die sich darauf auswirkt, wie die Abfrage verarbeitet und ihre Ergebnisse zurückgegeben werden.

* Eine [tabellarische Ausdrucksanweisung](./tabularexpressionstatements.md), die wichtigste Abfrageanweisung, gibt die "interessanten" Daten als Ergebnisse zurück.

## <a name="application-query-statements"></a>Anwendungsabfrageanweisungen

Im Folgenden finden Sie eine Liste der Anwendungsabfrageanweisungen:

* Eine [Aliasanweisung](./aliasstatement.md) definiert einen Alias in einer anderen Datenbank (im selben Cluster oder in einem Remotecluster).

* Eine [Musteranweisung](./patternstatement.md), die von Anwendungen verwendet werden kann, die auf Kusto basieren, und die Abfragesprache für ihre Benutzer verfügbar machen, um sich selbst in den Abfragenamensauflösungsprozess einzuschleusen.

* Eine [Abfrageparameteranweisung](./queryparametersstatement.md), die von Anwendungen verwendet wird, die auf Kusto basieren, um sich vor Injektionsangriffen zu schützen (ähnlich wie Befehlsparameter SQL vor SQL-Injektionsangriffen schützen).

* Eine [restrict-Anweisung](./restrictstatement.md), die von Anwendungen verwendet wird, die auf Kusto basieren, um Abfragen auf eine bestimmte Teilmenge von Daten in Kusto zu beschränken (einschließlich der Beschränkung des Zugriffs auf bestimmte Spalten und Datensätze).

::: zone-end

::: zone pivot="azuremonitor"

Dies wird in Azure Monitor nicht unterstützt.

::: zone-end
