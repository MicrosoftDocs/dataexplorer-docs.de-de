---
title: Suchoperator - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der Suchoperator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: de63aa3fde421996809334b8a746ea4dee8cb026
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509196"
---
# <a name="search-operator"></a>search-Operator

Der Suchoperator bietet eine mehrtabellen-/mehrspaltige Suchumgebung.

## <a name="syntax"></a>Syntax

* [*TabellarischeQuelle* `|`] `search` [`kind=`*CaseSensitivity*`in` `(`] [ *TableSources*`)`] *SearchPredikcate*

## <a name="arguments"></a>Argumente

* *Tabellarische Quelle*: Ein optionaler Tabellenausdruck, der als Datenquelle fungiert, die durchsucht werden soll, z. B. ein Tabellenname, ein [Union-Operator,](unionoperator.md)die Ergebnisse einer tabellenförmigen Abfrage usw. Kann nicht zusammen mit dem optionalen Ausdruck angezeigt werden, der *TableSources*enthält.

* *CaseSensitivity*: Ein optionales Flag, `string` das das Verhalten aller Skalaroperatoren in Bezug auf die Groß-/Kleinschreibung steuert. Gültige Werte sind die `default` `case_insensitive` beiden Synonyme und (was `has`für Operatoren wie " `case_sensitive` "Ohne Groß-/Kleinschreibung" und (die alle diese Operatoren in den Groß-/Kleinschreibungsmodus zwingen) der Standardwert ist.

* *TableSources*: Eine optionale durch Kommas getrennte Liste von "wildcarded" Tabellennamen, die an der Suche teilnehmen sollen.
  Die Liste hat die gleiche Syntax wie die Liste des [Union-Operators](unionoperator.md).
  Kann nicht zusammen mit der optionalen *TabularSource*angezeigt werden.

* *SearchPredikcate*: Ein obligatorisches Prädikat, das definiert, wonach gesucht werden soll (d. h. ein boolescher Ausdruck, der für jeden Datensatz in der Eingabe ausgewertet wird und der, wenn er zurückgegeben `true`wird, ausgegeben wird.) Die Syntax für *SearchPredicate* erweitert und ändert die normale Kusto-Syntax für boolesche Ausdrücke:

  **Zeichenfolgenübereinstimmungserweiterungen**: Zeichenfolgenliterale, die als Begriffe im *SearchPredicate* `has`angezeigt `hasprefix` `hassuffix`werden, geben eine`!`Termübereinstimmung zwischen allen Spalten und dem Literal mit , , , und den invertierten ( ) oder Groß-/Kleinschreibungsversionen (`sc`) dieser Operatoren an. Die Entscheidung, `has`ob `hasprefix`, `hassuffix` , oder hängt davon ab, ob das Literal`*`durch ein Sternchen ( ) beginnt oder endet ( oder beides ). Sternchen innerhalb des Literals sind nicht zulässig.

    |Literal   |Operator   |
    |----------|-----------|
    |`billg`   |`has`      |
    |`*billg`  |`hassuffix`|
    |`billg*`  |`hasprefix`|
    |`*billg*` |`contains` |
    |`bi*lg`   |`matches regex`|

  **Spalteneinschränkung**: Standardmäßig versuchen Zeichenfolgenabgleichserweiterungen, mit allen Spalten des Datensatzes abzugleichen. Es ist möglich, diesen Abgleich auf eine bestimmte Spalte zu beschränken, indem Sie die folgende Syntax verwenden: *ColumnName*`:`*StringLiteral*.

  **Zeichenfolgengleichheit**: Genaue Übereinstimmungen einer Spalte mit einem Zeichenfolgenwert (anstelle eines Term-Match) können mit der Syntax *ColumnName*`==`*StringLiteral*durchgeführt werden.

  **Andere boolesche Ausdrücke**: Alle regulären Kusto Boolean-Ausdrücke werden von der Syntax unterstützt.
    Bedeutet z. B.: Suchen Sie `error` nach Datensätzen, die den `123` Begriff `x` in einer ihrer Spalten enthalten und den Wert in der Spalte haben." `"error" and x==123`

  **Regex-Übereinstimmung**: Der Reguläre Ausdrucksabgleich wird mit *der Column* `matches regex` *StringLiteral-Syntax* angezeigt, wobei *StringLiteral* das Regex-Muster ist.

Beachten Sie, dass, wenn sowohl *TabularSource* als auch *TableSources* weggelassen werden, die Suche über alle uneingeschränkten Tabellen und Ansichten der Datenbank im Bereich übertragen wird.

## <a name="summary-of-string-matching-extensions"></a>Zusammenfassung der Zeichenfolgenübereinstimmungserweiterungen

  |# |Syntax                                 |Bedeutung (äquivalent `where`)           |Kommentare|
  |--|---------------------------------------|---------------------------------------|--------|
  | 1|`search "err"`                         |`where * has "err"`                    ||
  | 2|`search in (T1,T2,A*) and "err"`       |<code>union T1,T2,A* &#124; where * has "err"<code>   ||
  | 3|`search col:"err"`                     |`where col has "err"`                  ||
  | 4|`search col=="err"`                    |`where col=="err"`                     ||
  | 5|`search "err*"`                        |`where * hasprefix "err"`              ||
  | 6|`search "*err"`                        |`where * hassuffix "err"`              ||
  | 7|`search "*err*"`                       |`where * contains "err"`               ||
  | 8|`search "Lab*PC"`                      |`where * matches regex @"\bLab\w*PC\b"`||
  | 9|`search *`                             |`where 0==0`                           ||
  |10|`search col matches regex "..."`       |`where col matches regex "..."`        ||
  |11|`search kind=case_sensitive`           |                                       |Bei allen Zeichenfolgenvergleichen wird die Groß-/Kleinschreibung beachtet.|
  |12|`search "abc" and ("def" or "hij")`    |`where * has "abc" and (* has "def" or * has hij")`||
  |13|`search "err" or (A>a and A<b)`        |`where * has "err" or (A>a and A<b)`   ||

## <a name="remarks"></a>Bemerkungen

**Im Gegensatz zum** `search` [find-Operator](findoperator.md)unterstützt der Operator Folgendes nicht:

1. `withsource=`: Die Ausgabe enthält immer `$table` eine `string` Spalte namens Typ, deren Wert der Tabellenname ist, aus dem jeder Datensatz abgerufen wurde (oder einen vom System generierten Namen, wenn die Quelle keine Tabelle, sondern ein zusammengesetzter Ausdruck ist).
2. `project=`, `project-smart`: Das Ausgabeschema `project-smart` entspricht dem Ausgabeschema.

## <a name="examples"></a>Beispiele

```kusto
// 1. Simple term search over all unrestricted tables and views of the database in scope
search "billg"

// 2. Like (1), but looking only for records that match both terms
search "billg" and ("steveb" or "satyan")

// 3. Like (1), but looking only in the TraceEvent table
search in (TraceEvent) and "billg"

// 4. Like (2), but performing a case-sensitive match of all terms
search "BillB" and ("SteveB" or "SatyaN")

// 5. Like (1), but restricting the match to some columns
search CEO:"billg" or CSA:"billg"

// 6. Like (1), but only for some specific time limit
search "billg" and Timestamp >= datetime(1981-01-01)

// 7. Searches over all the higher-ups
search in (C*, TF) "billg" or "davec" or "steveb"

// 8. A different way to say (7). Prefer to use (7) when possible
union C*, TF | search "billg" or "davec" or "steveb"
```

## <a name="performance-tips"></a>Tipps zur Leistungssteigerung

  |# |Tipp                                                                                  |Prefer                                        |Over                                                                    |
  |--|-------------------------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------|
  | 1| Bevorzugen Sie die `search` Verwendung eines `search` einzelnen Operators gegenüber mehreren aufeinander folgenden Operatoren|`search "billg" and ("steveb" or "satyan")`   |<code>search "billg" &#124; search "steveb" or "satyan"<code>           ||
  | 2| Bevorzugen Sie das `search` Filtern innerhalb des Bedieners                                       |`search "billg" and "steveb"`                 |<code>search * &#124; where * has "billg" and * has "steveb"<code>      ||
