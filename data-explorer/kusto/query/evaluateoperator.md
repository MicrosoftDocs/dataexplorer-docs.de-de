---
title: 'Evaluieren des Plug-in-Operators: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt die Auswertung des Plug-in-Operators in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: d01b3b5178801fe1b5e55f51987564674ce4aeae
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128630"
---
# <a name="evaluate-operator-plugins"></a>evaluate-Operator-Plug-Ins

Ruft eine Dienst seitige Abfrageerweiterung (Plug-in) auf.

Der `evaluate` Operator ist ein tabellarischer Operator, der die Möglichkeit bietet, Abfrage Spracherweiterungen aufzurufen, die als Plug-ins bezeichnet werden. **plugins** Plug-Ins können aktiviert oder deaktiviert werden (im Gegensatz zu anderen Sprachkonstrukten, die immer verfügbar sind) und nicht durch den relationalen Charakter der Sprache "gebunden" sind (z. b. verfügen Sie möglicherweise nicht über ein vordefiniertes, statisch ermittelte Ausgabe Schema).

**Syntax** 

[*T* `|` ] `evaluate` [ *evaluateparameters* ] *PluginName* `(` [*PluginArg1* [ `,` *PluginArg2*]...`)`

Hierbei gilt:

* *T* ist eine optionale tabellarische Eingabe für das Plug-in. (Einige Plug-ins nehmen keine Eingaben an und fungieren als tabellarische Datenquelle.)
* *PluginName* ist der obligatorische Name des Plug-ins, das aufgerufen wird.
* *PluginArg1*,... die optionalen Argumente für das Plug-in.
* *evaluateparameters*: NULL oder mehr (durch Leerzeichen getrennte) Parameter in der Form des *namens* `=` *Werts* , die das Verhalten des Auswertungs Vorgangs und Ausführungs Plans steuern. Jedes Plug-in kann sich anders entscheiden, wie die einzelnen Parameter behandelt werden. Ein bestimmtes Verhalten finden Sie in der Dokumentation zu den einzelnen Plug-ins.  

Die folgenden Parameter werden unterstützt: 

  |Name                |Werte                           |Beschreibung                                |
  |--------------------|---------------------------------|-------------------------------------------|
  |`hint.distribution` |`single`, `per_node`, `per_shard`| [Verteilungs Hinweise](#distributionhints) |
  |`hint.pass_filters` |`true`, `false`| Zulassen `evaluate` , dass der Operator alle übereinstimmenden Filter vor dem Plug-in durchläuft. Der Filter wird als ' abgeglichen ' betrachtet, wenn er auf eine Spalte verweist, die vor dem Operator vorhanden ist `evaluate` . Standard: `false` |
  |`hint.pass_filters_column` |*column_name*| Ermöglicht dem Plug-in-Operator das Passthrough von Filtern, die auf *column_name* vor dem Plugin verweisen Der-Parameter kann mehrmals mit unterschiedlichen Spaltennamen verwendet werden. |

**Notizen**

* Syntaktisch `evaluate` verhält sich ähnlich wie der [Aufruf Operator](./invokeoperator.md), der tabellarische Funktionen aufruft.
* Plug-ins, die über den evaluierungsoperator bereitgestellt werden, sind nicht durch die regulären Regeln der Abfrage Ausführung oder Argument Auswertung
* Für bestimmte Plug-ins gelten möglicherweise bestimmte Einschränkungen. Beispielsweise können Plug-ins, deren Ausgabe Schema von den Daten abhängt (z. b. [bag_unpack Plug](./bag-unpackplugin.md) -in und [Pivot-Plug](./pivotplugin.md)-in), beim Ausführen von Cluster übergreifenden Abfragen nicht verwendet werden.

<a id="distributionhints"/>**Verteilungs Hinweise**</a>

Verteilungs Hinweise geben an, wie die Ausführung des Plug-Ins auf mehrere Cluster Knoten verteilt wird. Jedes Plug-in kann eine andere Unterstützung für die Verteilung implementieren. Die Dokumentation des Plug-ins gibt die vom Plug-in unterstützten Verteilungs Optionen an.

Mögliche Werte:

* `single`: Eine einzelne Instanz des Plug-ins wird über die gesamten Abfrage Daten ausgeführt.
* `per_node`: Wenn die Abfrage vor dem Plug-in-aufrufknoten über Knoten verteilt wird, wird eine Instanz des Plug-Ins auf jedem Knoten über die darin enthaltenen Daten ausgeführt.
* `per_shard`: Wenn die Daten vor dem Plug-in-Aufrufen über Shards verteilt werden, wird eine Instanz des Plug-ins über jeden Shard der Daten ausgeführt.
