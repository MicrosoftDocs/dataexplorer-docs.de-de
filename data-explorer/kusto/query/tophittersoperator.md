---
title: Top-Hitters-Operator - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der Top-Hitter-Operator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7105bd4f9818deab1f0fa5dbe25dbb2d5b1b4afc
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663108"
---
# <a name="top-hitters-operator"></a>top-hitters-Operator

Gibt eine Annäherung der ersten *N-Ergebnisse* zurück (vorausgesetzt, die verzerrte Verteilung der Eingabe wird übernommen).

```kusto
T | top-hitters 25 of Page by Views 
```

**Syntax**

*T* `| top-hitters` *NumberOfRows* `of` *sort_key* `[` `by` *Ausdruck*`]`

**Argumente**

* *NumberOfRows*: Die Anzahl der zurückzugebenden Zeilen von *T.* Sie können einen beliebigen numerischen Ausdruck angeben.
* *sort_key*: Der Name der Spalte, nach der die Zeilen sortiert werden sollen.
* *Ausdruck*: (optional) Ein Ausdruck, der für die Top-Hitter-Schätzung verwendet wird. 
    * *ausdruck*: Top-Hitters geben *NumberOfRows-Zeilen* zurück, die ein ungefähres Maximum an Summe *(Ausdruck)* aufweisen. Ausdruck kann eine Spalte oder ein anderer Ausdruck sein, der zu einer Zahl ausgewertet wird. 
    *  Wenn *der Ausdruck* nicht erwähnt wird, zählt der Top-Hitter-Algorithmus die Vorkommen des *Sortierschlüssels*.  

**Hinweise**

`top-hitters`ist ein Annäherungsalgorithmus und sollte bei der Ausführung mit großen Daten verwendet werden. Die Annäherung der Top-Hitter basiert auf dem [Count-Min-Sketch-Algorithmus.](https://en.wikipedia.org/wiki/Count%E2%80%93min_sketch)  

**Beispiel**

## <a name="getting-top-hitters-most-frequent-items"></a>Top-Hitter (meist häufige Artikel) 

Das nächste Beispiel zeigt, wie Sie die Top-5-Sprachen mit den meisten Seiten in Wikipedia finden (abgerufen nach April 2016). 

```kusto
PageViews
| where Timestamp > datetime(2016-04-01) and Timestamp < datetime(2016-05-01) 
| top-hitters 5 of Language 
```

|Sprache|approximate_count_Language|
|---|---|
|en|1539954127|
|zh|339827659|
|de|262197491|
|ru|227003107|
|fr|207943448|

## <a name="getting-top-hitters-based-on-column-value-"></a>Abrufen von Top-Hittern (basierend auf dem Spaltenwert) ***

Das nächste Beispiel zeigt, wie Sie die meistgesehenen englischen Seiten von Wikipedia des Jahres 2016 finden. Die Abfrage verwendet 'Ansichten' (ganzzahlige Zahl), um die Seitenpopularität (Anzahl der Ansichten) zu berechnen. 

```kusto
PageViews
| where Timestamp > datetime(2016-01-01)
| where Language == "en"
| where Page !has 'Special'
| top-hitters 10 of Page by Views
```

|Seite|approximate_sum_Views|
|---|---|
|Main_Page|1325856754|
|Web_scraping|43979153|
|Java_(programming_language)|16489491|
|United_States|13928841|
|Wikipedia|13584915|
|Donald_Trump|12376448|
|YouTube|11917252|
|The_Revenant_(2015_film)|10714263|
|Star_Wars:_The_Force_Awakens|9770653|
|Portal:Current_events|9578000|