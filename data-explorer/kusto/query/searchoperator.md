---
title: 'Search-Operator: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird der Such Operator in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: edd35e5e259666e8ce4360c072aaac6717e6f8c3
ms.sourcegitcommit: f9d3f54114fb8fab5c487b6aea9230260b85c41d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2020
ms.locfileid: "85071877"
---
# <a name="search-operator"></a>search-Operator

Der Such Operator bietet eine mehrspaltige und mehrspaltige Suchfunktion.

## <a name="syntax"></a>Syntax

* [*Tabularsource* `|` ] `search`[ `kind=` *CaseSensitivity*] [ `in` `(` *tablesources* `)` ] *searchpredicate*

## <a name="arguments"></a>Argumente

* *Tabularsource*: ein optionaler tabellarischer Ausdruck, der als zu durchsuchende Datenquelle fungiert, wie z. b. ein Tabellenname, ein [Union-Operator](unionoperator.md), die Ergebnisse einer tabellarischen Abfrage usw. Kann nicht mit dem optionalen Ausdruck angezeigt werden, der *tablesources*enthält.

* *CaseSensitivity*: ein optionales Flag zum Steuern des Verhaltens aller `string` skalaren Operatoren in Bezug auf die Groß-/Kleinschreibung. Gültige Werte sind die beiden Synonyme `default` und `case_insensitive` (Dies ist die Standardeinstellung für Operatoren, z. b. `has` , bei der Groß-/Kleinschreibung nicht beachtet wird) und `case_sensitive` (die alle diese Operatoren in die Groß-/Kleinschreibung unterschieden

* *Tablesources*: eine optionale durch Trennzeichen getrennte Liste von "Platzhalter"-Tabellennamen, die an der Suche teilnehmen sollen.
  Die Liste enthält dieselbe Syntax wie die Liste des Union- [Operators](unionoperator.md).
  Kann nicht in Verbindung mit der optionalen *tabularsource*angezeigt werden.

* *Searchpredicate*: ein obligatorisches Prädikat, das definiert, was gesucht werden soll (d. h. ein boolescher Ausdruck, der für jeden Datensatz in der Eingabe ausgewertet wird und bei Rückgabe `true` des Datensatzes ausgegeben wird). Die Syntax für *searchpredicate* erweitert und ändert die normale Kusto-Syntax für boolesche Ausdrücke:

  **Erweiterungen für Zeichen**folgen Übereinstimmungen: Zeichen folgen Literale, die als Begriffe in *searchpredicate* angezeigt werden, geben an, dass eine Übereinstimmung zwischen allen Spalten und dem Literalzeichen verwendet `has` wird `hasprefix` `hassuffix` `!` `sc` Die Entscheidung `has` , ob, oder anzuwenden ist, `hasprefix` `hassuffix` hängt davon ab, ob das Literale durch ein Sternchen () beginnt oder endet (oder beides) `*` . Sternchen innerhalb des Literals sind nicht zulässig.

    |Literal   |Betreiber   |
    |----------|-----------|
    |`billg`   |`has`      |
    |`*billg`  |`hassuffix`|
    |`billg*`  |`hasprefix`|
    |`*billg*` |`contains` |
    |`bi*lg`   |`matches regex`|

  **Spalten Einschränkung**: Standardmäßig wird für Zeichen folgen Vergleichs Erweiterungen versucht, eine Übereinstimmung mit allen Spalten des Datasets zu finden. Es ist möglich, diesen Abgleich auf eine bestimmte Spalte zu beschränken, indem Sie die folgende Syntax verwenden: *ColumnName* `:` *Stringliteral.*

  **Zeichen folgen Gleichheit**: genaue Übereinstimmungen einer Spalte mit einem Zeichen folgen Wert (anstelle einer Begriffs Übereinstimmung) können mithilfe der Syntax *ColumnName* `==` *stringliteralverwendet*werden.

  **Andere boolesche Ausdrücke**: alle regulären Kusto-booleschen Ausdrücke werden von der-Syntax unterstützt.
    Beispielsweise `"error" and x==123` bedeutet: Suchen Sie nach Datensätzen, deren Begriff `error` in einer ihrer Spalten enthalten ist, und weisen Sie den Wert `123` in der `x` Spalte auf.

  **Regex-Übereinstimmung**: der übereinstimmende *Column* reguläre Ausdruck wird mit `matches regex` der *stringlitersyntax* der Spalte angegeben, wobei *Stringliteral* das Regex-Muster ist.

Beachten Sie Folgendes: Wenn sowohl *tabularsource* als auch *tablesources* ausgelassen werden, wird die Suche über alle uneingeschränkten Tabellen und Sichten der Datenbank im Gültigkeitsbereich durchgeführt.

## <a name="summary-of-string-matching-extensions"></a>Zusammenfassung der Erweiterungen für Zeichen folgen Vergleiche

  |# |Syntax                                 |Bedeutung (äquivalent `where` )           |Kommentare|
  |--|---------------------------------------|---------------------------------------|--------|
  | 1|`search "err"`                         |`where * has "err"`                    ||
  | 2|`search in (T1,T2,A*) and "err"`       |<code>union T1,T2,A* &#124; where * has "err"<code>   ||
  | 3|`search col:"err"`                     |`where col has "err"`                  ||
  | 4|`search col=="err"`                    |`where col=="err"`                     ||
  | 5|`search "err*"`                        |`where * hasprefix "err"`              ||
  | 6|`search "*err"`                        |`where * hassuffix "err"`              ||
  | 7|`search "*err*"`                       |`where * contains "err"`               ||
  | 8|`search "Lab*PC"`                      |`where * matches regex @"\bLab.*PC\b"`||
  | 9|`search *`                             |`where 0==0`                           ||
  |10|`search col matches regex "..."`       |`where col matches regex "..."`        ||
  |11|`search kind=case_sensitive`           |                                       |Bei allen Zeichen folgen vergleichen wird Groß-/Kleinschreibung beachtet|
  |12|`search "abc" and ("def" or "hij")`    |`where * has "abc" and (* has "def" or * has hij")`||
  |13|`search "err" or (A>a and A<b)`        |`where * has "err" or (A>a and A<b)`   ||

## <a name="remarks"></a>Hinweise

**Im Gegensatz** zum [Find](findoperator.md)-Operator `search` unterstützt der-Operator Folgendes nicht:

1. `withsource=`: Die Ausgabe enthält immer eine Spalte `$table` mit dem Namen des Typs `string` , deren Wert dem Tabellennamen entspricht, aus dem jeder Datensatz abgerufen wurde (oder ein vom System generierter Name, wenn die Quelle keine Tabelle, sondern ein zusammengesetzter Ausdruck ist).
2. `project=`, `project-smart` : Das Ausgabe Schema entspricht dem `project-smart` Ausgabe Schema.

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
  | 1| Bevorzugen der Verwendung eines einzelnen `search` Operators für mehrere aufeinander folgende `search` Operatoren|`search "billg" and ("steveb" or "satyan")`   |<code>search "billg" &#124; search "steveb" or "satyan"<code>           ||
  | 2| Filter im `search` Operator bevorzugen                                       |`search "billg" and "steveb"`                 |<code>search * &#124; where * has "billg" and * has "steveb"<code>      ||
