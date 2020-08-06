---
title: 'Top-Hitters-Operator: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird der Top-Hitters-Operator in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: be05a3a546bb6f1db003be14e4a1417841b54671
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/05/2020
ms.locfileid: "87804065"
---
# <a name="top-hitters-operator"></a>top-hitters-Operator

Gibt einen Näherungswert für die ersten *N* Ergebnisse zurück (unter der Voraussetzung einer verzerrten Verteilung der Eingabe).

```kusto
T | top-hitters 25 of Page by Views 
```

> [!NOTE]
> `top-hitters`ist ein Näherungs Algorithmus und sollte bei der Ausführung mit großen Daten verwendet werden. Die Näherung der Top-Hitters basiert auf dem [count-min-Sketch-](https://en.wikipedia.org/wiki/Count%E2%80%93min_sketch) Algorithmus.  

## <a name="syntax"></a>Syntax

*T* - `| top-hitters` *numofrows* - `of` *sort_key* `[` `by` *Ausdruck*`]`

## <a name="arguments"></a>Argumente

* *Nummeriofrows*: die *Anzahl von Zeilen, die* zurückgegeben werden sollen. Sie können einen beliebigen numerischen Ausdruck angeben.
* *sort_key*: der Name der Spalte, nach der die Zeilen sortiert werden sollen.
* *Expression*: (optional) ein Ausdruck, der für die Top-Hitters-Schätzung verwendet wird. 
    * *Ausdruck*: Top-Hitters geben *numofrows* -Zeilen zurück, die eine ungefähre maximale Summe von Sum (*Expression*) aufweisen. Der Ausdruck kann eine Spalte oder ein beliebiger anderer Ausdruck sein, der zu einer Zahl ausgewertet wird. 
    *  Wenn *Expression* nicht angegeben wird, zählt der Top-Hitters-Algorithmus die Vorkommen des *Sortier Schlüssels*.  

## <a name="examples"></a>Beispiele

### <a name="get-most-frequent-items"></a>Die häufigsten Elemente erhalten 

Im nächsten Beispiel wird gezeigt, wie Sie Top-5-Sprachen mit den meisten Seiten in Wikipedia suchen (auf die nach dem 2016-Mal zugegriffen wird). 

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

### <a name="get-top-hitters-based-on-column-value"></a>Top-Hitters basierend auf dem Spaltenwert erhalten

Im nächsten Beispiel wird gezeigt, wie Sie die am häufigsten angezeigten englischen Seiten von Wikipedia im Jahr 2016 suchen. Die Abfrage verwendet "Views" (ganzzahlige Zahl) zum Berechnen der Seiten Beliebtheit (Anzahl der Sichten). 

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
|Java_ (programming_language)|16489491|
|United_States|13928841|
|Wikipedia|13584915|
|Donald_Trump|12376448|
|YouTube|11917252|
|The_Revenant_ (2015_film)|10714263|
|Star_Wars: _The_Force_Awakens|9770653|
|Portal: Current_events|9578000|
