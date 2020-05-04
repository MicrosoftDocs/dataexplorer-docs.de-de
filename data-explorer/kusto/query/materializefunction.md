---
title: Materialize ()-Azure Daten-Explorer
description: Dieser Artikel beschreibt Materialize () in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/21/2019
ms.openlocfilehash: e721e5809d3b0445fecc0609668332b66ef39db8
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737333"
---
# <a name="materialize"></a>materialize()

Ermöglicht das Zwischenspeichern eines Unterabfrage Ergebnisses während der Abfrage Ausführung auf eine Weise, mit der andere Unterabfragen auf das Teilergebnis verweisen können.

 
**Syntax**

`materialize(`*expression*`)`

**Argumente**

* *Ausdruck*: der tabellarische Ausdruck, der während der Abfrage Ausführung ausgewertet und zwischengespeichert werden soll.

**Tipps**

* Verwenden Sie materialisieren mit Join oder Union, wenn ihre Operanden gegenseitige Unterabfragen verfügen, die einmal ausgeführt werden können. Weitere Informationen finden Sie in den folgenden Beispielen.

* Auch hilfreich in Szenarien, in denen Verzweigungsabschnitte für „join/union“ benötigt werden.

* Die Materialisierung kann nur in Let-Anweisungen verwendet werden, wenn Sie dem zwischengespeicherten Ergebnis einen Namen geben.


* "Materialize" hat eine Cache Größenbeschränkung von **5 GB**. 
  Diese Beschränkung erfolgt pro Cluster Knoten und ist für alle gleichzeitig ausgeführten Abfragen gegenseitig.
  Wenn eine Abfrage verwendet `materialize()` und der Cache keine weiteren Daten enthalten kann, wird die Abfrage mit einem Fehler abgebrochen.

**Beispiele**

Wir möchten einen zufälligen Satz von Werten generieren und möchten Folgendes wissen: 
 * wie viele unterschiedliche Werte haben wir 
 * die Summe aller dieser Werte. 
 * die ersten drei Werte

Dieser Vorgang kann mithilfe von [Batches](batches.md) und materialisieren ausgeführt werden:

 ```kusto
let randomSet = materialize(range x from 1 to 30000000 step 1
| project value = rand(10000000));
randomSet
| summarize dcount(value);
randomSet
| top 3 by value;
randomSet
| summarize sum(value)

```