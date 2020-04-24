---
title: materialize() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird materialize() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/21/2019
ms.openlocfilehash: dfaed8cf972a517c86717999b3e5423c0ddd1fb0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512613"
---
# <a name="materialize"></a>materialize()

Ermöglicht das Zwischenspeichern eines Unterabfrageergebnisses während der Ausführung von Abfragen in einer Weise, dass andere Unterabfragen auf das Teilergebnis verweisen können.

 
**Syntax**

`materialize(`*expression*`)`

**Argumente**

* *Ausdruck*: Tabellarischer Ausdruck, der während der Abfrageausführung ausgewertet und zwischengespeichert werden soll.

**Tipps**

* Verwenden Sie „materialize“, wenn Sie über „join/union“ mit Operanden mit gegenseitigen Unterabfragen verfügen, die einmal ausgeführt werden können (wie in den folgenden Beispielen zu sehen).

* Auch hilfreich in Szenarien, in denen Verzweigungsabschnitte für „join/union“ benötigt werden.

* Wenn „materialize“ in let-Anweisungen verwendet wird, muss das zwischengespeicherte Ergebnis benannt werden.

* Materialize hat eine Cache-Größenbeschränkung von **5 GB**. 
  Dieser Grenzwert gilt pro Clusterknoten und ist für alle Abfragen, die gleichzeitig ausgeführt werden, wechselseitig.
  Wenn eine `materialize()` Abfrage verwendet wird und der Cache keine zusätzlichen Daten enthalten kann, wird die Abfrage mit einem Fehler abgebrochen.

**Beispiele**

Vorausgesetzt, wir wollen einen zufälligen Satz von Werten generieren und wir sind daran interessiert, herauszufinden, wie viel unterschiedliche Werte wir haben, die Summe all dieser Werte und die obersten 3 Werte.

Dies kann mit [Chargen](batches.md) erfolgen und materialisieren:

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