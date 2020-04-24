---
title: Plugin-Operator auswerten - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der Evaluierungs-Plugin-Operator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 1aae36df29abf705ba821abdc2d1da96e4635a60
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515741"
---
# <a name="evaluate-plugin-operator"></a>evaluate-Plug-In-Operator

Ruft eine dienstseitige Abfrageerweiterung (Plugin) auf.

Der `evaluate` Operator ist ein tabellarischer Operator, der die Möglichkeit bietet, Abfragesprachenerweiterungen aufzurufen, die als **Plugins**bezeichnet werden. Plugins können aktiviert oder deaktiviert werden (im Gegensatz zu anderen Sprachkonstrukten, die immer verfügbar sind) und sind nicht durch die relationale Natur der Sprache "gebunden" (z. B. haben sie möglicherweise kein vordefiniertes, statisch festgelegtes Ausgabeschema).

**Syntax** 

[*T* `|`T `evaluate` ] [ *evaluateParameters* ] *PluginName* `(` [*PluginArg1* [`,` *PluginArg2*]...`)`

Hierbei gilt:

* *T* ist ein optionaler tabellarischer Eingang zum Plugin. (Einige Plugins nehmen keine Eingaben ein und fungieren als tabellarische Datenquelle.)
* *PluginName* ist der obligatorische Name des aufgerufenen Plugins.
* *PluginArg1*, ... sind die optionalen Argumente für das Plugin.
* *evaluateParameters*: Null oder mehr (raumgetrennte) Parameter in Form von *Name* `=` *Value,* die das Verhalten des Auswertens vorgangs- und Ausführungsplans steuern. Die folgenden Parameter werden unterstützt: 

  |name                |Werte                           |Beschreibung                                |
  |--------------------|---------------------------------|-------------------------------------------|
  |`hint.distribution` |`single`, `per_node`, `per_shard`| [Verteilungshinweise](#distribution-hints) |

**Hinweise**

* Syntaktisch `evaluate` verhält er sich ähnlich wie der [Aufrufoperator](./invokeoperator.md), der tabellarische Funktionen aufruft.
* Plugins, die über den Evaluate-Operator bereitgestellt werden, sind nicht an die regulären Regeln der Abfrageausführung oder Argumentauswertung gebunden.
Bestimmte Plugins können bestimmte Einschränkungen haben. Plugins, deren Ausgabeschema von den Daten abhängt (z. B. [bag_unpack-Plugin),](./bag-unpackplugin.md)können beispielsweise nicht verwendet werden, wenn clusterübergreifende Abfragen ausgeführt werden.

## <a name="distribution-hints"></a>Verteilungshinweise

Verteilungshinweise geben an, wie die Pluginausführung auf mehrere Clusterknoten verteilt wird. Jedes Plugin kann eine andere Unterstützung für die Distribution implementieren. Die Dokumentation des Plugins gibt die vom Plugin unterstützten Verteilungsoptionen an.

Mögliche Werte:

* `single`: Eine einzelne Instanz des Plugins wird über die gesamten Abfragedaten ausgeführt.
* `per_node`: Wenn die Abfrage vor dem Plugin-Aufruf auf Knoten verteilt ist, wird auf jedem Knoten eine Instanz des Plugins über die darin enthaltenen Daten ausgeführt.
* `per_shard`: Wenn die Daten vor dem Plugin-Aufruf auf Shards verteilt werden, wird eine Instanz des Plugins über jeden Shard der Daten ausgeführt.