---
title: 'String-Operator: Azure Data Explorer'
description: In diesem Artikel wird der String-Operator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/19/2020
ms.localizationpriority: high
ms.openlocfilehash: 0dffa044ff47748d46b3ab4758bb61684bb8bb10
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100359725"
---
# <a name="string-operators"></a>Zeichenfolgenoperatoren

Kusto bietet eine Vielzahl von Abfrageoperatoren zum Durchsuchen von Zeichenfolgendatentypen. Im folgenden Artikel wird beschrieben, wie Zeichenfolgenbegriffe indiziert werden, die Zeichenfolgenabfrageoperatoren werden aufgelistet, und Sie erhalten Tipps zur Leistungsoptimierung.

## <a name="understanding-string-terms"></a>Verstehen von Zeichenfolgenbegriffen

Kusto indiziert alle Spalten, einschließlich Spalten vom Typ `string`. Abhängig von den tatsächlichen Daten werden mehrere Indizes für solche Spalten erstellt. Diese Indizes werden nicht direkt bereitgestellt, sondern in Abfragen mit den `string`-Operatoren verwendet, die `has` als Teil ihres Namens aufweisen, z. B. `has`, `!has`, `hasprefix`, `!hasprefix`. Die Semantik dieser Operatoren wird durch die Art der Codierung der Spalte vorgegeben. Anstatt eine „einfache“ Teilzeichenfolge abzugleichen, gleichen diese Operatoren *Begriffe* ab.

### <a name="what-is-a-term"></a>Was ist ein Begriff? 

Standardmäßig wird jeder `string`-Wert in maximale Sequenzen von alphanumerischen ASCII-Zeichen unterteilt, und jede dieser Sequenzen wird in einen Begriff umgewandelt.
Im folgenden `string`-Element sind die Begriffe z. B. `Kusto`, `WilliamGates3rd` und die folgenden Teilzeichenfolgen: `ad67d136`, `c1db`, `4f9f`, `88ef`, `d94f3b6b0b5a`.

```
Kusto: ad67d136-c1db-4f9f-88ef-d94f3b6b0b5a;;WilliamGates3rd
```

Kusto erstellt einen Begriffsindex, der aus allen Begriffen besteht, die *vier Zeichen lang oder länger* sind. Dieser Index wird von `has`, `!has` usw. verwendet. Wenn die Abfrage nach einem Begriff sucht, der kleiner als vier Zeichen ist, oder einen `contains`-Operator verwendet, kehrt Kusto zum Scannen der Werte in der Spalte zurück, wenn keine Übereinstimmung ermittelt werden kann. Diese Methode ist viel langsamer als das Nachschlagen des Begriffs im Begriffsindex.

## <a name="operators-on-strings"></a>Operatoren für Zeichenfolgen

> [!NOTE]
> In der Tabelle unten werden die folgenden Abkürzungen verwendet:
> * RS = Rechte Seite des Ausdrucks
> * LS = Linke Seite des Ausdrucks
> 
> Bei Operatoren mit einem `_cs`-Suffix wird Groß-/Kleinschreibung beachtet.

> [!NOTE]
> Operatoren ohne Beachtung der Groß-/Kleinschreibung werden derzeit nur für ASCII-Text unterstützt. Verwenden Sie für den Vergleich von Nicht-ASCII-Text die Funktion [tolower()](tolowerfunction.md).

Operator        |BESCHREIBUNG                                                       |Groß-/Kleinschreibung|Beispiel (ergibt `true`)
----------------|------------------------------------------------------------------|--------------|-----------------------
`==`            |Equals                                                            |Ja           |`"aBc" == "aBc"`
`!=`            |Not Equals                                                        |Ja           |`"abc" != "ABC"`
`=~`            |Equals                                                            |Nein            |`"abc" =~ "ABC"`
`!~`            |Not Equals                                                        |Nein            |`"aBc" !~ "xyz"`
`has`           |Rechte Seite (RS) ist ein ganzer Begriff innerhalb der linken Seite (LS)     |Nein            |`"North America" has "america"`
`!has`          |RS ist kein vollständiger Begriff in LS                                     |Nein            |`"North America" !has "amer"` 
[`has_all`](has-all-operator.md)       |Identisch mit `has`, funktioniert aber für alle Elemente                    |Nein            |`"North and South America" has_all("south", "north")`
[`has_any`](has-anyoperator.md)       |Identisch mit `has`, funktioniert aber für jedes der Elemente                    |Nein            |`"North America" has_any("south", "north")`
`has_cs`        |RS ist ein vollständiger Begriff in LS                                        |Ja           |`"North America" has_cs "America"`
`!has_cs`       |RS ist kein vollständiger Begriff in LS                                     |Ja           |`"North America" !has_cs "amer"` 
`hasprefix`     |RS ist ein Begriffspräfix in LS                                       |Nein            |`"North America" hasprefix "ame"`
`!hasprefix`    |RS ist kein Begriffspräfix in LS                                   |Nein            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`  |RS ist ein Begriffspräfix in LS                                       |Ja           |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs` |RS ist kein Begriffspräfix in LS                                   |Ja           |`"North America" !hasprefix_cs "CA"` 
`hassuffix`     |RS ist ein Begriffssuffix in LS                                       |Nein            |`"North America" hassuffix "ica"`
`!hassuffix`    |RS ist kein Begriffssuffix in LS                                   |Nein            |`"North America" !hassuffix "americ"`
`hassuffix_cs`  |RS ist ein Begriffssuffix in LS                                       |Ja           |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs` |RS ist kein Begriffssuffix in LS                                   |Ja           |`"North America" !hassuffix_cs "icA"`
`contains`      |Rechte Seite kommt als Teilsequenz von linker Seite vor                                |Nein            |`"FabriKam" contains "BRik"`
`!contains`     |Rechte Seite kommt auf linker Seite nicht vor                                         |Nein            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Rechte Seite kommt als Teilsequenz von linker Seite vor                                |Ja           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |Rechte Seite kommt auf linker Seite nicht vor                                         |Ja           |`"Fabrikam" !contains_cs "Kam"`
`startswith`    |Rechte Seite ist eine öffnende Teilsequenz von linker Seite                              |Nein            |`"Fabrikam" startswith "fab"`
`!startswith`   |Rechte Seite ist keine öffnende Teilsequenz von linker Seite                          |Nein            |`"Fabrikam" !startswith "kam"`
`startswith_cs` |Rechte Seite ist eine öffnende Teilsequenz von linker Seite                              |Ja           |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`|Rechte Seite ist keine öffnende Teilsequenz von linker Seite                          |Ja           |`"Fabrikam" !startswith_cs "fab"`
`endswith`      |Rechte Seite ist eine schließende Teilsequenz von linker Seite                               |Nein            |`"Fabrikam" endswith "Kam"`
`!endswith`     |Rechte Seite ist keine schließende Teilsequenz von linker Seite                           |Nein            |`"Fabrikam" !endswith "brik"`
`endswith_cs`   |Rechte Seite ist eine schließende Teilsequenz von linker Seite                               |Ja           |`"Fabrikam" endswith_cs "kam"`
`!endswith_cs`  |Rechte Seite ist keine schließende Teilsequenz von linker Seite                           |Ja           |`"Fabrikam" !endswith_cs "brik"`
`matches regex` |Linke Seite enthält eine Übereinstimmung für rechte Seite                                      |Ja           |`"Fabrikam" matches regex "b.*k"`
[`in`](inoperator.md)            |Entspricht einem der Elemente                                     |Ja           |`"abc" in ("123", "345", "abc")`
[`!in`](inoperator.md)           |Entspricht keinem der Elemente                                 |Ja           |`"bca" !in ("123", "345", "abc")`
`in~`           |Entspricht einem der Elemente                                     |Nein            |`"abc" in~ ("123", "345", "ABC")`
`!in~`          |Entspricht keinem der Elemente                                 |Nein            |`"bca" !in~ ("123", "345", "ABC")`


> [!TIP]
> Alle Operatoren, die `has`-Suchen für indizierte *Begriffe* enthalten, die vier Zeichen lang oder länger sind, und nicht für Teilzeichenfolgenübereinstimmungen. Ein Begriff wird erstellt, indem die Zeichenfolge in Sequenzen von alphanumerischen ASCII-Zeichen unterteilt wird. Weitere Informationen finden Sie unter [Verstehen von Zeichenfolgenbegriffen](#understanding-string-terms).

## <a name="performance-tips"></a>Leistungstipps

Verwenden Sie für bessere Leistung bei zwei Operatoren, die dieselbe Aufgabe ausführen, den Operator, der zwischen Groß-/Kleinschreibung unterscheidet.
Zum Beispiel:

* Verwenden Sie `==` anstelle von `=~`.
* Verwenden Sie `in` anstelle von `in~`.
* Verwenden Sie `contains_cs` anstelle von `contains`.

Um schnellere Ergebnisse zu erhalten, verwenden Sie `has` oder `in`, wenn Sie auf das Vorhandensein eines Symbols oder alphanumerischen Worts, das durch nicht-alphanumerische Zeichen gebunden ist, oder auf den Anfang oder das Ende eines Felds testen. 
`has` ist schneller als `contains`, `startswith` oder `endswith`.

Beispielsweise wird die erste dieser Abfragen schneller ausgeführt:

```kusto
EventLog | where continent has "North" | count;
EventLog | where continent contains "nor" | count
```
