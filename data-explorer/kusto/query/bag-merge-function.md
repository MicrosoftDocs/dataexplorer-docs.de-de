---
title: bag_merge ()-Azure Daten-Explorer
description: In diesem Artikel wird bag_merge () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: elgevork
ms.author: elgevork
ms.reviewer: ''
ms.service: data-explorer
ms.topic: reference
ms.date: 06/14/2020
ms.openlocfilehash: f5ca4888b0c3aad976d78c10bbbfa0810483c75b
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/15/2020
ms.locfileid: "84784598"
---
# <a name="bag_merge"></a>bag_merge ()

Führt dynamische Eigenschaften Behälter Objekte der Eingabe in einem dynamischen Eigenschaften Behälter Objekt zusammen.

**Syntax**

`bag_merge(`*dynamisches Objekt* `,` *dynamisches Objekt*`)`

**Argumente**

* Eingabe dynamischer Objekte, getrennt durch Kommas. Die Funktion unterstützt 2 bis 64 dynamische Eingabe Objekte.

**Rückgabe**

Gibt einen `dynamic` Eigenschaften Behälter zurück. Ergebnisse aus der Zusammenführung aller Eingabe Eigenschaften Behälter-Objekte.
Wenn ein Schlüssel in mehr als einem Eingabe Objekt angezeigt wird, wird ein beliebiger Wert (aus den möglichen Werten für diesen Schlüssel) ausgewählt.

**Beispiel**

Ausdruck:

`print result = bag_merge(dynamic({ 'A1':12, 'B1':2, 'C1':3}), dynamic({ 'A2':81, 'B2':82, 'A1':1}))`

Ergibt:

`{
  "A1": 12,
  "B1": 2,
  "C1": 3,
  "A2": 81,
  "B2": 82
}`
