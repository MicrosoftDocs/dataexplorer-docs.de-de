---
title: 'Abfrage Anweisungen: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel werden die Abfrage Anweisungen in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: e546f71c3eb4eda09ff7061dd1a3be9eb6ad6c88
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252884"
---
# <a name="query-statement-types"></a>Abfrageanweisungstypen

::: zone pivot="azuredataexplorer"

Eine Abfrage besteht aus einer oder mehreren **Abfrage Anweisungen**, getrennt durch ein Semikolon ( `;` ).
Mindestens eine dieser Abfrage Anweisungen muss eine [tabellarische Ausdrucks Anweisung](./tabularexpressionstatements.md)sein.
Die tabellarische Ausdrucks Anweisung generiert ein oder mehrere tabellarische Ergebnisse.
Wenn die Abfrage mehr als eine tabellarische Ausdrucks Anweisung enthält, enthält die Abfrage einen [Batch](./batches.md) von Tabellen Ausdrucks Anweisungen, und die von diesen Anweisungen generierten tabellarischen Ergebnisse werden von der Abfrage zurückgegeben.

Zwei Typen von Abfrage Anweisungen:

* Anweisungen, die in erster Linie von Benutzern verwendet werden ([Benutzer Abfrage Anweisungen](#user-query-statements)),
* Anweisungen, die entwickelt wurden, um Szenarios zu unterstützen, in denen Anwendungen der mittleren Ebene Benutzer Abfragen durchführen und eine geänderte Version von Ihnen an Kusto ([Anwendungs Abfrage Anweisungen](#application-query-statements)) senden.

Einige Abfrage Anweisungen sind in beiden Szenarien nützlich.

> [!NOTE]
> Der Effekt einer Abfrage Anweisung beginnt an dem Punkt, an dem die Anweisung in der Abfrage angezeigt wird, und endet am Ende der Abfrage. Sobald die Abfrage abgeschlossen ist, werden alle zugehörigen Ressourcen freigegeben, und Sie hat keine Auswirkungen auf zukünftige Abfragen (mit Ausnahme von Nebeneffekten, z. b. das Aufzeichnen der Abfrage in einem Protokoll aller Abfragen oder das Zwischenspeichern der Ergebnisse).

## <a name="user-query-statements"></a>Benutzer Abfrage Anweisungen

Im folgenden finden Sie eine Liste der Benutzer Abfrage Anweisungen:

* Eine [Let-Anweisung](./letstatement.md) definiert eine Bindung zwischen einem Namen und einem Ausdruck.
  Let-Anweisungen können verwendet werden, um eine lange Abfrage in kleine benannte Teile zu unterteilen, die leichter zu verstehen sind.

* Eine [Set-Anweisung](./setstatement.md) legt eine Abfrage Option fest, die sich darauf auswirkt, wie die Abfrage verarbeitet und die Ergebnisse zurückgegeben werden.

* Eine [tabellarische Ausdrucks Anweisung](./tabularexpressionstatements.md), die wichtigste Abfrage Anweisung, gibt die "interessanten" Daten als Ergebnisse zurück.

## <a name="application-query-statements"></a>Anwendungs Abfrage Anweisungen

Im folgenden finden Sie eine Liste der Anwendungs Abfrage Anweisungen:

* Eine [Alias Anweisung](./aliasstatement.md) definiert einen Alias für eine andere Datenbank (im gleichen Cluster oder auf einem Remote Cluster).

* Eine [Pattern-Anweisung](./patternstatement.md), die von Anwendungen verwendet werden kann, die auf Kusto basieren, und ihren Benutzern die Abfragesprache zur Verfügung stellt, um Sie in den Prozess der Abfrage Namensauflösung einzufügen.

* Eine [Abfrage Parameter-Anweisung](./queryparametersstatement.md), die von Anwendungen verwendet wird, die auf Kusto aufbauen, um sich gegen Injection-Angriffe zu schützen (ähnlich wie Befehlsparameter SQL gegen SQL-Injection-Angriffe schützen).

* Eine [Einschränkungs Anweisung](./restrictstatement.md), die von Anwendungen verwendet wird, die auf Kusto aufbauen, um Abfragen auf eine bestimmte Teilmenge von Daten in Kusto einzuschränken (einschließlich der Einschränkung des Zugriffs auf bestimmte Spalten und Datensätze).

::: zone-end

::: zone pivot="azuremonitor"

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
